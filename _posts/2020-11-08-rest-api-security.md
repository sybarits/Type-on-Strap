---
layout: post
title: Spring Security를 이용한 REST API 보안
tags: [spring, security, rest, api]
---

Spring Security를 이용해 REST API를 보다 안전하게 사용하는 법에대해 알아본다.  
서버는 적절한 절차를 거쳐 클라이언트에게 토큰을 발급하고, 클라이언트는 발급 받은 토큰을 이용해 REAT API를 호출할때 사용한다.  
구글링을 통해 얻은 내용을 짜집기해 만들어보았다. 아래 출처에서 밝혔듯이 happydaddy님과 victolee님의 블로그를 주로 참고했다. 물론 이분들은 여기에 자신들의 글이 참고되었는지 알지 못한다... 이글을 빌어 감사하다는 말을 전한다. 까먹기 전에 내용정리해보자.
spring security를 적용하면서 spring framework에 좀 더 집중해서 공부할 필요성을 느꼈다.  

## 의존성 설정
spring boot와 gradle을 기반으로 작성한다. 아래 처럼 build.gradle에 dependencies 부분에 spring security 설정을 위한 의존성을 추가 한다.
```
dependencies {
  ...
  implementation 'org.springframework.boot:spring-boot-starter-security'
  implementation 'io.jsonwebtoken:jjwt:0.9.1'
  implementation "javax.xml.bind:jaxb-api"
  ...
}
```

## Filter
Spring Framework에서 클라이언트의 요청은 받으면 서버의 컨트롤러에 도달하기 전에 filter를 거친다. spring security를 사용하기 위해서는 이 필터 설정이 필요하다. REST API 서버기 때문에 별도의 UI가 없고, session도 없다. 따라서 Token기반 인증 절차를 구현한다.  

### SecurityConfiguration
@Configuration 을 추가하여 설정 클래스를 추가 한다. 이 클래스를 통해 등록할 필터를 설정한다.
* 지금보니 UsernamePasswordAuthenticationFilter를 나는 사용하지 않는다. 이 부분에 대해서 좀 더 조사해보아야겠다.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    private final JwtTokenProvider jwtTokenProvider;

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .httpBasic().disable() // rest api 이므로 기본설정 사용안함. 기본설정은 비인증시 로그인폼 화면으로 리다이렉트 된다.
            .csrf().disable() // rest api이므로 csrf 보안이 필요없으므로 disable처리.
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS) // jwt token으로 인증하므로 세션은 필요없으므로 생성안함.
            .and()
                .authorizeRequests() // 다음 리퀘스트에 대한 사용권한 체크
                    .antMatchers("/signin", "/signup").permitAll() // 가입 및 인증 주소는 누구나 접근가능
                    // .antMatchers(HttpMethod.GET, "helloworld/**").permitAll() // hellowworld로 시작하는 GET요청 리소스는 누구나 접근가능
                    .anyRequest().hasRole("USER") // 그외 나머지 요청은 모두 인증된 회원만 접근 가능
            .and()
                .addFilterBefore(new JwtAuthenticationFilter(jwtTokenProvider), UsernamePasswordAuthenticationFilter.class); // jwt token 필터를 id/password 인증 필터 전에 넣는다
    }
}
```

### JwtAuthenticationFilter
발급 받은 토큰을 클라이언트가 요청에 포함해 보내면 이를 확인하는 필터를 구현한다. 토큰의 유효성을 확인하는 과정이 이 필터에서 이루어진다.

```java
import java.io.IOException;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;

import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.filter.GenericFilterBean;

public class JwtAuthenticationFilter extends GenericFilterBean{
    private JwtTokenProvider jwtTokenProvider;

    // Jwt Provier 주입
    public JwtAuthenticationFilter(JwtTokenProvider jwtTokenProvider) {
        this.jwtTokenProvider = jwtTokenProvider;
    }

    // Request로 들어오는 Jwt Token의 유효성을 검증(jwtTokenProvider.validateToken)하는 filter를 filterChain에 등록
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws IOException, ServletException {
        String token = jwtTokenProvider.resolveToken((HttpServletRequest) request);
        if (token != null && jwtTokenProvider.validateToken(token)) {
            Authentication auth = jwtTokenProvider.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        filterChain.doFilter(request, response);
    }
}
```

### JwtTokenProvider
토큰을 생성하고 발행하거나 토큰을 파싱하고 유효성을 검사하는등의 기능이 이 클래스에 구현되었다.

```java
import java.util.Base64;
import java.util.Date;
import java.util.List;

import javax.annotation.PostConstruct;
import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.stereotype.Component;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jws;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@Component
public class JwtTokenProvider {
    @Value("spring.jwt.secret")
    private String secretKey;

    private long tokenValidMilisecond = 1000L * 60 * 60; // 1시간만 토큰 유효

    @Autowired
    private final UserDetailsService userDetailsService;

    @PostConstruct
    protected void init() {
        secretKey = Base64.getEncoder().encodeToString(secretKey.getBytes());
    }

    // Jwt 토큰 생성
    public String createToken(String userPk, List<String> roles) {
        Claims claims = Jwts.claims().setSubject(userPk);
        claims.put("roles", roles);
        Date now = new Date();
        return Jwts.builder()
                .setClaims(claims) // 데이터
                .setIssuedAt(now) // 토큰 발행일자
                .setExpiration(new Date(now.getTime() + tokenValidMilisecond)) // set Expire Time
                .signWith(SignatureAlgorithm.HS256, secretKey) // 암호화 알고리즘, secret값 세팅
                .compact();
    }

    // Jwt 토큰으로 인증 정보를 조회
    public Authentication getAuthentication(String token) {
        UserDetails userDetails = userDetailsService.loadUserByUsername(this.getUserPk(token));
        return new UsernamePasswordAuthenticationToken(userDetails, "", userDetails.getAuthorities());
    }

    // Jwt 토큰에서 회원 구별 정보 추출
    public String getUserPk(String token) {
        return Jwts.parser().setSigningKey(secretKey).parseClaimsJws(token).getBody().getSubject();
    }

    // Request의 Header에서 token 파싱 : "X-AUTH-TOKEN: jwt토큰"
    public String resolveToken(HttpServletRequest req) {
        return req.getHeader("X-AUTH-TOKEN");
    }

    // Jwt 토큰의 유효성 + 만료일자 확인
    public boolean validateToken(String jwtToken) {
        try {
            Jws<Claims> claims = Jwts.parser().setSigningKey(secretKey).parseClaimsJws(jwtToken);
            return !claims.getBody().getExpiration().before(new Date());
        } catch (Exception e) {
            return false;
        }
    }
}
```

## 회원 가입과 토큰 발급
위에서 언급한 security 설정을 살펴보면 signin과 signup을 제외한 요청은 불가하도록 되어있다. 클라이언트가 적절한 절차를 거쳐 토큰을 발급받으면 서버는 모든 요청에 대한 응답을 해준다. 회원가입(signup)과 로그인(signin) 기능을 보자

### SignController
먼저 컨트롤러 작성이다. 로그인과 회원가입을 위한 엔트리 포인트를 작성한다.

```java
import java.util.Arrays;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import com.tmax.errorreport.VO.SignVO;
import com.tmax.errorreport.VO.UserVO;
import com.tmax.errorreport.security.JwtTokenProvider;
import com.tmax.errorreport.services.CustomUserDetailService;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class SignController {

    @Autowired
    private CustomUserDetailService customUserDetailService;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private JwtTokenProvider jwtTokenProvider;

    // signin, login
    @PostMapping(value = "/signin")
    @ResponseBody
    public SignVO signInUser(HttpServletRequest request, @RequestBody UserVO user) {
        UserVO result = (UserVO)customUserDetailService.findByName(user.getName());
        SignVO signVO = new SignVO();
        if (!passwordEncoder.matches(user.getPassword(), result.getPassword())) {
            signVO.setResult("fail");
            signVO.setMessage("ID or Password is invalid.");
            return signVO;
        }
        List<String> roleList = Arrays.asList(result.getRoles().split(","));
        signVO.setResult("success");
        signVO.setToken(jwtTokenProvider.createToken(result.getId(), roleList));
        return signVO;
    }

    // signup, 
    @PostMapping(value = "/signup")
    @ResponseBody
    public SignVO addUser(HttpServletRequest request, @RequestBody UserVO signupUser) {
        UserVO user = signupUser;
        user.setRoles("ROLE_USER");
        user.setPassword(passwordEncoder.encode(signupUser.getPassword()));
        SignVO signVO = new SignVO();
        int result = customUserDetailService.signInUser(user);
        if (result == 1) {
            signVO.setResult("success");
            signVO.setMessage("SignUp complete");
            return signVO;
        } else if (result == -1) {
            signVO.setResult("fail");
            signVO.setMessage("There is the same name, please change your name.");
            return signVO;
        } else {
            signVO.setResult("fail");
            signVO.setMessage("Ask system admin");
            return signVO;
        }
    }
}
```


### SignVO
로그인이 성공하면 토큰을 담아 보내거나, 실패하면 메세지를 담아 보내기 위한 클래스다.

```java
import lombok.Data;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Data
@Getter
@Setter
@ToString
public class SignVO {

    private String result, message, token;

}
```

### UserVO
유저 관련 정보를 담아 처리하는 클래스. UserDetails를 상속해서 구현한다. is로 시작하는 메소드들은 사용하지 않기 때문에 패스한다.

```java
import java.util.ArrayList;
import java.util.Collection;

import com.fasterxml.jackson.annotation.JsonProperty;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import lombok.Data;
import lombok.ToString;

@Data
@ToString
public class UserVO implements UserDetails {

    /**
     *  This VO is for security.
     */
    private static final long serialVersionUID = 1L;

    private String id;
    private String name;
    private String password;
    private String roles;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        ArrayList<GrantedAuthority> auth = new ArrayList<GrantedAuthority>();
        auth.add(new SimpleGrantedAuthority(roles));
        return auth;
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return name;
    }

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @Override
    public boolean isEnabled() {
        return true;
    }
    
}
```

### CustomUserDetailService
컨트롤러에서 호출하는 DB 조회 서비스다. 회원 가입을 하거나 로그인을 위한 기능이다.

```java
import com.tmax.errorreport.VO.UserVO;
import com.tmax.errorreport.mybatis.mapper.UserMapper;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailService implements UserDetailsService {

    @Autowired
    UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String id) throws UsernameNotFoundException {
        return userMapper.findById(id);
    }

    public UserDetails findByName(String name) {
        return userMapper.findByName(name);
    }

    public int signInUser(UserVO user) {
        if (userMapper.findByName(user.getName()) == null) {
            return userMapper.insertUser(user);
        } else {
            return -1;
        }
    }

    public int deleteUser(String name) {
        return userMapper.deleteUser(name);
    }
    
}
```

## Client
클라이언트는 회원가입을 통해 권한이 주어진 아이디를 발급 받아야 한다. 회원가입이 된 아이디를 이용해 로그인을 시도할 경우 서버는 토큰을 발급한다. 클라이언트는 발급받은 토큰을 헤더에 담아 REAT API를 호출한다. 적절한 토큰이 요청 헤더에 담겨있지 않다면 API호출을 할 수 없다구!


출처: https://daddyprogrammer.org/post/636/springboot2-springsecurity-authentication-authorization/    
https://victorydntmd.tistory.com/328  
