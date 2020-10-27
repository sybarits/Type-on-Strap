---
layout: post
title: React에서 PureComponent의 특징
tags: [React, component, purecomponent, shallow, comparison]
---

React.PureComponent의 특징에 대해서 알아본다.  

## React.PureComponent
PureComponent와 Component는 React의 생명주기 메소드 중 하나인 shouldComponentUpdate를 다루는 방식이 다르다. React.PureComponent는 이미 shouldComponentUpdate가 구현되어있다. 이 함수는 props와 state를 얕은 비교를 통해 변경된 것이 있을 때 리랜더링을 한다.

## 얕은 비교
얕은 비교(shallow comparison)를 설명하기 전에 깊은 비교(deep comparison)을 알아본다. 깊은 비교는 숫자나 문자열 같은 값들은 값을 비교한다. array나 function과 같은 object는 그 안에 들어있는 값을 일일이 확인한다. 깊은 비교를 정확하게 하기는 쉽지않다. 얕은 비교는 이에 반해 object들의 레퍼런스 체크(참조하고 있는 객체가 같은지)만 한다.

## PureComponent의 장점과 단점
장점은 컴포넌트를 리랜더링 할때 얕은 비교를 통해 리랜더링을 결정하기 때문에 성능상의 이점이 있다. 단점은 복잡한 구조의 데이터는 비교하지 못하기 때문에 리랜더링 조건에 대해 직접 다루어야 하는 경우가 발생할 수도 있다.





출처: https://wonism.github.io/react-pure-component/  
https://blog.majecty.com/posts/2019-10-03-react-component-pure-component.html  
https://ideveloper2.tistory.com/159
