---
layout: post
title: Observer Pattern
tags: [pattern, observer, design]
---

## Observer Pattern
- 한 객체 상태 변화에 다라 다른 객체 상태도 연동되도록 일대다 객체 의존 관계를 구성하는 패턴
- 데이터 변경이 발생했을 결우 상대 클래스나 객체에 의존하지 않으면서 데이터 변경을 통보하고자 할 때 유용
- 옵저버 패턴은 통보 대상 객체의 관리르 Subject 클래스와 Observer 인터페이스로 일반화
- 데이터 변경을 통보하는 클래스(CondreteSubject)는 통보 대상 클래스나 객체에 대한 의존성을 없앨 수 있음

## 구현 예시
야구 카운트 정보에 대한 정보를 보여주는 시스템이 있다고 가정하자. 야구 카운트에 대한 정보를 가진 Subject가 존재하고 이를 여러군데에서 보여주는 Observer 구현체들이 있다. 정보는 한군데에서 제공하고 Observer 구현체들은 여러 곳에서 여러 방식으로 정보를 보여줄 수 있다. Observer 구현체들은 여러가지 방법으로 구현 될 수 있고 이에 따라 정보를 보여주는 방법도 여러가지가 될 수 있다. Observer 구현체가 추가 되거나 다른 방식을 구현 되더라도 정보를 제공하는 Subject는 정보를 제공하기만 하기 때문에 이에 따른 변경이 필요 없다.

```java
public interface Subject {
	public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers();
}
```
```java
public class BaseballStatus implements Subject {
	private ArrayList<Observer> observers;
    private int strike;
    private int ball;
    private int out;

    public BaseballStatus() {
        this.observers = new ArrayList<>();
    }

    public int getStrike() {
    	return strike;
    }
    
    public void setStrike(int strike) {
    	this.strike = strike;
    }

    public int getBall() {
		return ball;
	}

	public void setBall(int ball) {
		this.ball = ball;
	}

	public int getOut() {
		return out;
	}

	public void setOut(int out) {
		this.out = out;
	}

	@Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        int i = observers.indexOf(o);
        if (i >= 0) {
            observers.remove(i);
        }
    }

    @Override
    public void notifyObservers() {
        for (Observer o : observers) {
            o.update();
        }
    }

    public void gameChanged() {
        notifyObservers();
    }

    public void setGame(int strike, int ball, int out) {
        this.setStrike(strike);
        this.ball = ball;
        this.out = out;
        gameChanged();
    }
    
}
```
위는 야구 정보를 제공하는 Subject BaseballStatus에 대한 코드

```java
public interface Observer {
	public void update();
}
```
```java
public interface Display {
	public void display();
}
```
```java
public class CurrentStateDisplayMonitor implements Observer, Display {

	private String monitorName;
	private BaseballStatus baseballStatus;
	
	public CurrentStateDisplayMonitor(String dn, BaseballStatus bs) {
		this.monitorName = dn;
		this.baseballStatus = bs;
		bs.registerObserver(this);
	}
	
	@Override
	public void display() {
		System.out.println("monitor: " + monitorName);
		System.out.println("Strike: " + baseballStatus.getStrike() + " Ball: " + baseballStatus.getBall() + " Out: " + baseballStatus.getOut() );
	}

	@Override
	public void update() {
		display();
	}

}
```
위는 정보를 받아 보여주는 Observer 구현체이다. 이 구현체는 다른 방식을 구현된 클래스가 추가 될 수 있으나 정보를 제공하는 BaseballStatus인 Subject는 변경이 필요 없다.
```java
public class ObserverMain {
	
	public static void main(String[] args) {
		BaseballStatus bs = new BaseballStatus();
		CurrentStateDisplayMonitor monitor1 = new CurrentStateDisplayMonitor("시청역 전광판", bs);
		CurrentStateDisplayMonitor monitor2 = new CurrentStateDisplayMonitor("광화문 전광판", bs);
		CurrentStateDisplayMonitor monitor3 = new CurrentStateDisplayMonitor("테해란로 전광판", bs);
		
		bs.setGame(0, 1, 0);
		bs.setGame(1, 1, 0);
		bs.setGame(1, 2, 0);
		bs.setGame(1, 2, 1);

	}
}

monitor: 시청역 전광판
Strike: 0 Ball: 1 Out: 0
monitor: 광화문 전광판
Strike: 0 Ball: 1 Out: 0
monitor: 테해란로 전광판
Strike: 0 Ball: 1 Out: 0
monitor: 시청역 전광판
Strike: 1 Ball: 1 Out: 0
monitor: 광화문 전광판
Strike: 1 Ball: 1 Out: 0
monitor: 테해란로 전광판
Strike: 1 Ball: 1 Out: 0
monitor: 시청역 전광판
Strike: 1 Ball: 2 Out: 0
monitor: 광화문 전광판
Strike: 1 Ball: 2 Out: 0
monitor: 테해란로 전광판
Strike: 1 Ball: 2 Out: 0
monitor: 시청역 전광판
Strike: 1 Ball: 2 Out: 1
monitor: 광화문 전광판
Strike: 1 Ball: 2 Out: 1
monitor: 테해란로 전광판
Strike: 1 Ball: 2 Out: 1
```
BaseballStatus를 observer 구현체들에 등록하고 setGame을 통해 BaseballStatus가 변경될 때마다 정보가 표시되는 것을 확인 할 수 있다.




출처: https://gmlwjd9405.github.io/2018/07/08/observer-pattern.html  
  https://johngrib.github.io/wiki/observer-pattern/
  
