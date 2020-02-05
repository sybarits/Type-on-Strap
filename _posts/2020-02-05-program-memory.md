---
layout: post
title: Program and Memory
tags: [program, memory, code, data, stack, heap]
excerpt_separator: <!--more-->
---

### c 언어로 만들어진 프로그램이 실행되면 메모리로 로드되며, 메모리에 올라온 프로그램은 기계어 코드가 들어있는 코드 섹션(Code Section)과 데이터를 저장하는 데이터 섹션(Data Section)으로 나뉜다. 데이터 섹션은 전역 메모리(Global Memory), 스택 메모리(Stack Memory)와 힙 메모리(Heap Memory)로 나뉜다.

- 코드 섹션에는 program code(cpu에 의해서 수행되는 기계어 명령어들)이 모여있다.

- 전역 메모리에는 모든 지역 함수(Local Function) 바깥에서 선언된 변수(Global Variable)와 정적 변수(Static Variables)를 할당한다. 메인 함수는 운영체제가 프로그램 실행을 위해 최초로 호출하는 일종의 지역 함수로 구분된다. 프로그램 시작과 동시에 할당되고, 프로그램이 종료되어야만 메모리에서 소멸된다.
```
int start = 0;//global variable
void main()
{
  static int count = 0;////static variable
}
```
  
- 스택 메모리는 함수내에 선언된 지역 변수(Local Variables)와 매개 변수(Parameter Variables)를 저장한다. 컴파일 시에 필요한 공간의 크기를 확정한다. 함수 호출시 그에 해당하는 지역 변수를 위한 공간을 확보하며 해당 함수의 실행이 끝나면 지역 변수의 공간을 자동으로 해제한다. 스택 영역은 SP(stack pointer)를 베이스로 하여 스택 함수(push, pop)를 통해 스택 영역에 데이터를 write한다. 즉 함수 호출이 되면 4개(시스템마다 다를 수 있음)까지는 레지스터에 변수가 매핑이 되고 그 이후의 함수 파라미터들은 스택에 push 된 후 pop을 통해 파라미터들을 전달 받는다. 함수 내부의 변수들 만큼 스택에 push를 한다. 함수 호출이 완료되면 지역 변수의 공간을 자동으로 해제 한다는 것은 함수가 종료되어 필요하지 않은 변수들은 pop을 해서 sp를 감소시킨다는 의미이다. 반대로 push는 sp를 증가시킨다.
```
void main()
{
  int a;//local variable
}
```
  
- 힙 메모리는 프로그램 실행 중 필요에 의한 동적 메모리 할당을 위해 마련한 일정량의 메모리 공간이다. C에서는 malloc, C++에서는 new를 사용하여 메모리를 할당한다. 힙 메모리의 크기는 운영체제가 정한 최대 가상 메모리(Virtual Memory) 만큼이다. 
```
void main()
{
  char *p = (char *)malloc(1000*sizeof(char));
}
```
  
  
## 메모리 구조
높은 번지(High Address) | 힙(Heap)
                       | 미사용 공간(Available)
                       | 스택(Stack)
                       | 전역 변수(Global Variables)
낮은 번지(Low Address)  | 기계어 코드(Machine Code) 
  
미사용 공간은 스택의 정적으로 할당, 힙의 동적 할당에 따라 줄어든다. 이말의 의미는 처음부터 모든 스택과 힙 공간이 할당되어 있는 것이 아니라 동적이든 정적이든 함수의 호출로 인하여 메모리 공간이 필요한 시기에 할당된다는 의미이다.
  
- 
  
  출처: https://clarus.tistory.com/entry/%EC%BD%94%EB%93%9C%EC%84%B9%EC%85%98-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%84%B9%EC%85%98
  https://pro-gen.tistory.com/18
