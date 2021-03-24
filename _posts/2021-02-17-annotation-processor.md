---
layout: post
title: Annotation processor
tags: [annotation, processor, java, maven]
---

## Annotation
어노테이션은 메타데이터의 한 종류이다. @Override, @Component와 같이 아주 익숙한 표현이 있는데 이처럼 @가 붙어있는 단어를 어노테이션이라 한다. 클래스, 메소드, 변수 등에 어노테이션을 붙일 수 있다. 어노테이션은 메타데이터라고 말할 것과 같이 소스코드 상에서 주석처럼 읽을 수도 있고, 컴파일러가 어노테이션을 찾아 컴파일 단계에서 특정 동작이 이루어지도록 할 수있다. 런타임에 JVM에 의해 특정 동작에 사용 될 수도 있다. 

## Custom Annotation
스프링 부트에서 익숙하게 사용하는 어노테이션들을 이용하면 예상대로 그대로 동작한다. 여기서 사용하는 어노테이션은 스프링 부트를 만드는 사람들이 만든 어노테이션이다. 이처럼 실제 동작하는 어노테이션을 직접 만들어보자.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Cafe {
	String id() default "";
	CafeMenu menu();
}
```
위 처럼 @interface 로 타입을 정의 하고 id와 menu를 정의한다. 이것들은 변수 처럼 사용된다. defualt는 아무런 값을 지정해 주지 않았을 때 들어가는 값이다. @target 은 어노테이션을 어디에 적용할 지에 대한 내용이다. 위 코드는 메소트에 적용할 어노테이션이라는 것을 나타낸다. @Retetion 은 어노테이션을 언제 적용할지를 정해주는 값이다. RUNTIME은 코드 실행 중에 적용하겠다는 의미다.

```java
public enum CafeMenu {
	AMERICANO,
	ICE_AMERICANO
}

//////////////////////////////

import java.lang.reflect.Method;

public class TwoSome {
	@Cafe(menu = CafeMenu.AMERICANO)
	public int getAmericanoPrice() { return 4100; }
	
	@Cafe(menu = CafeMenu.ICE_AMERICANO)
	public int getIceAmericanoPrice() { return 4500; }
	
	public static void main(String[] args) {
		Method[] methods = TwoSome.class.getMethods();
		
		for (Method method : methods) {
			Cafe cafe = method.getAnnotation(Cafe.class);
			if (cafe != null) {
				System.out.println(cafe.menu());
				if (method.getName().equals("getAmericanoPrice")) {
					System.out.println("method getAmericanoPrice");
				} else if (method.getName().equals("getIceAmericanoPrice")) {
					System.out.println("method getIceAmericanoPrice");
				}
			}
		}
	}
}

```

실제 어노테이션을 사용해 프로그램 실행 중에 메소드의 정보를 가져와서 메소드이름과 메소드에 적용되어 있는 어노테이션 이름을 출력해 보았다. 다음은 어노테이션을 적용해 컴파일시 코드를 작성하는 어노테이션을 만들어 본다.

## Annotation Processor
어노테이션 프로세서는 컴파일 단계에서 어노테이션을 검사하여 코드베이스를 검사하거나 수정, 생성하는 일을 한다. 코드를 생성하거나 수정하는 일을 이용해 개발자는 반복적인 소스코드 작업을 줄일 수 있다. 실제 코드를 생성하는 어노테이션을 만들어보자.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
public @interface ServiceHandler {
    String serviceName();
    String method() default "CUSTOM";
}
```
@Tartget에 TYPE을 주어 class에 어노테이션을 지정하겠다고 명시한다. @Retention은 SOURCE를 준다. 아래 코드는 어노테이션이 적용된 클래스들을 찾아서 이 클래스와 관련된 다른 소스코드를 생성해주는 annotation procees를 실행하는 코드이다.

```java
import com.google.auto.service.AutoService;

@AutoService(Processor.class)
public class ServiceHandlerProcessor extends AbstractProcessor {

    private Messager messager;
    private Elements elementUtils;
    private Filer filer;
    
    @Override
    public synchronized void init(ProcessingEnvironment processingEnv) {
        super.init(processingEnv);
        this.messager = processingEnv.getMessager();
        this.elementUtils = processingEnv.getElementUtils();
        this.filer = processingEnv.getFiler();
    }

    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        Set<? extends Element> elementList = roundEnv.getElementsAnnotatedWith((Class)ServiceHandler.class);
        for (Element e : elementList) {
            if (!e.getKind().equals(ElementKind.CLASS)) {
                this.messager.printMessage(Diagnostic.Kind.ERROR, 
                    String.format("Only Class will be used for @%s", new Object[] { ServiceHanlder.class.getSimpleName() }));
                return true;
            } 
            TypeElement te = (TypeElement)e;
           
            try {
                makeSourceFile(te);
            } catch (IOException ioe) {
                this.messager.printMessage(Diagnostic.Kind.ERROR, "File IO Failed: ".concat(ioe.getMessage()));
                return true;
            } 
        } 
        return true;
    }

    private void makeSourceFile(TypeElement handlerFile) throws IOException {
        String fileName = handlerFile.getQualifiedName().toString() + "Handler";
        JavaFileObject jfo = this.filer.createSourceFile(fileName, new Element[0]);
        try (Writer writer = jfo.openWriter()) {
            writer.write(makeSourceCode(handlerFile));
        } 
    }

    private String makeSourceCode(TypeElement te) {
        String packageName = this.elementUtils.getPackageOf(te).toString();
        String className = te.getSimpleName().toString();
        String sourceCode = String.format("package %s;\n"
                                        + "public class %s {\n"
                                        + "@Override\n"
                                        + "public Object doit(Object arg0, String arg1) throws Throwable {\n"
                                        + "return arg0;\n"
                                        + "}\n"
                                        + "}"
                                        , new Object[] { packageName
                                        , className + "Handler"
                                        });
        
        return sourceCode;
    }

    @Override
    public Set<String> getSupportedAnnotationTypes() {
        Set<String> annotataions = new LinkedHashSet<String>();
        annotataions.add(ServiceHandler.class.getCanonicalName());
        return annotataions;
    }

    @Override
    public SourceVersion getSupportedSourceVersion() {
        // TODO Auto-generated method stub
        return super.getSupportedSourceVersion();
    }

    
}
```
@AutoService를 추가해 어노테이션 프로세서를 지정해준다. init 메소드는 처음 한번 실행된다. process가 실제로 annotation processor가 동작할 때 일하는 메소드이다. 위 코드는 실제 업무에 쓰이는 코드를 임의로 수정했기 때문에 동작을 보장 할 수 없지만 대략 이런식으로 소스가 구성된다 정도만 이해하자. 이렇게 작성한 코드를 아래와 같이 pom.xml에 설정을 추가한 후 jar 로 컴파일 한다. gradle로 설정하는 법은 따로 공부해보자.

```xml
<build>
    <pluginManagement>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.1</version>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-source-plugin</artifactId>
          <executions>
            <execution>
              <id>attach-sources</id>
              <goals>
                <goal>jar</goal>
              </goals>
            </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>

  <dependencies>
    <dependency>
        <groupId>com.google.auto.service</groupId>
        <artifactId>auto-service</artifactId>
        <version>1.0-rc7</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```


```
<dependency>
  <groupId>com.gom</groupId>
  <artifactId>annotations</artifactId>
  <version>0.0.1</version>
  <scope>system</scope>
  <systemPath>${project.basedir}/lib/annotations-0.0.1.jar</systemPath>
  <optional>true</optional>
</dependency>
```
빌드한 결과를 실제 적용할 프로젝트로 가져와서 의존성을 위처럼 추가한다. 클래스에 어노테이션을 적용하고 빌드하면 빌드 결과물에 소스가 생성되고 컴파일된 클래스가 추가되어있는 것을 확인할 수 있다.

출처: https://mysend12.medium.com/java-annotation-1-ee04963c7d73    
https://www.charlezz.com/?p=1167  
