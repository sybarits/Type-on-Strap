---
layout: post
title: calling convention
tags: [stdcall, cdecl]
---

* calling convention(호출 규약)  
스택 프래임을 사용하는 함수들 간의 대화 방식  
두개만 알아보자

* stdcall
 windows 에서 사용하는 호출 규약
 windows API들이 사용
 함수가 return 하면서 인자를 정리하므로 별도의 명령과 절차가 필요 없음
 callee가 스택을 정리
 가변인자 사용 불가

* cdecl  
 c에서 에서 만든 호출 규약
 caller가 스택을 정리
 가변인자 사용 가능



출처: 여기 저기
