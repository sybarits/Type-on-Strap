---
layout: post
title: Memory Hierarchy
tags: [memory, hierachy]
---

* 레지스터(Resister)
* 캐시 메모리(Cache Memory)
* 메인 메모리(Main Memory)
* 보조 기억장치(HDD, CD-ROM)


1. 레지스터  
레지스터는 CPU Core 내부에 존재, Core에서 연산을 수행할 때 직접 참조할 수 있는 유일한  기억장치  
레지스터는 Core 내부에 몇 개 있는 귀한 기억장치  
보통 한 워드가 레지스터 하나의 크기  
word: 프로세서가 처리할 수 있는 가장 자연스러운 데이터의 크기이며, 보통 4Byte=32Bit  
레지스터들은 각각 사용되는 용도가 정해져 있으며, 유사한 기능을 하는 레지스터들 끼리 모아서 레지스터 파일(Register File)을 구성  

2. 캐시 메모리  
캐시 메모리도 레지스터와 마찬가지로 CPU 내부에 존재  
Core와 입출력 버스를 통해 정보를 주고받음  
보통 명령어를 저장하는 Instruction Cache와 데이터를 저장하는 Data Cache로 구분  
때로는 캐시 메모리도 L1 Cache, L2 Cache 등 여러 레벨으로 분할해서 세부 계층화 하기도 함  
캐시 메모리는 주로 Refresh(메모리의 내용을 보존하기 위해서 주기적으로 데이터를 갱신하는 작업. 이 작업이 수행중일 때는 메모리에 읽거나 쓰는 동작을 할 수 없다.)가 필요 없는 SRAM(Static RAM)으로 구성  

3. 메인 메모리  
메인 메모리(Main Memory)는 CPU 외부에 존재  
CPU와 North Bridge를 통한 데이터 버스를 통해 통신  
메인 메모리는 데이터를 보존하기 위해 주기적으로 Refresh가 필요한 DRAM으로 구성

4. 보조 기억장치  
보조 기억장치는 하드 디스크나 CD, DVD, USB메모리 등을 포함하며, 메인 메모리에 비해 접근 속도가 현저하게 떨어짐  
  
  
출처  
https://tuwlab.com/ece/1977

  -끝-
