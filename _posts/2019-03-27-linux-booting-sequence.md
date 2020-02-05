---
layout: post
title: linux booting sequence
tags: [boot, booting]
---

## 전원 스위치 ON !
- 시스템 전원 공급하면 메인보드의 ROM-BIOS에 있는 BIOS 프로그램 자동실행
- BIOS 프로그램은 전원 공급과 함께 메모리의 특정 번지(예:FFFF0H)에 자동 로드 됨
- CPU는 전원공급과 함께 특정 번지(예:FFFF0H)의 BIOS 프로그램을 자동 실행
  
## BIOS 프로그램들의 실행 내용들
- 자체 진단 기능(POST): CMOS 검사, CPU, MEMORY, 그래픽 카드, 키보드, 마우스 등 각종 장치들의 이상유무를 검사하고 이 장치들을 초기화 시킴
- POST 과정이 이상없이 진행완료되면 검색된 부팅매체에서 부트로더(예: GRUB, LILO)를 불러들임
- 예를 들어 하드디스크가 부팅매체로 선택되었다면 하드디스크의 부팅파티션에 있는 0번섹터 (대부분 MBR이라고도 함)에 있는 부트로더(Boot Loader GRUB)을 읽어들임
- 부트로더(GRUB)가 메모리에 적재되면 BIOS는 종료되고, 시스템 제어권은 부트로더가 가짐
  
## 부트로더(GRUB)의 실행
- GRUB은 실행과 함께 /boot/grub/grub.conf 파일을 읽어서 어떤 부팅메뉴(커널)로 부팅을 할 것인가를 결정(자동 또는 사용자 선택)
- GRUB은 커널(kernel) 이미지를 불러들임
- 시스템 제어권을 커널에게 넘겨줌
  
## 커널의 로딩
- 커널은 swapper 프로세스(PID 0번)를 호출
- swapper는 커널이 사용할 각 장치드라이브들을 초기화하고 init 프로세스 (PID 1번)를 실행
- init 프로세스가 실행되면서 /etc/inittab 파일을 읽어들여서 그 내용들을 차례대로 실행
  
## init 프로세스의 실행
- 이후 과정은 init 프로세스의 실행 내용(/etc/inittab 파일의 실행 내용들)
- 즉, 로그인 프롬프트가 나오기 까지 init 프로세스에 의해서 실행되는 내용
- id:3:initdefault: ~~ 시스템 부팅 레벨을 결정하는 행
- si::sysinit:/etc/rc.d/rc.sysinit  ~~ /etc/rc.d/rc.sysinit 파일 실행, 시스템 초기화하는 스크립트들이 있음
- l3:3:wait:/etc/rc.d/rc 3 ~~ 부팅레벨에 따라 rc3.d 와 같은 파일들이 실행
- ca::ctrlaltdel:/sbin/shutdown -t3 -r now ~~ CTRL + ALT + DEL 키를 동시에 눌렀을 때 실행될 내용을 설정한 행(일종의 magic key)
- 1:2345:respawn:/sbin/mingetty tty1 ~~ tty 1부터 6까지 행이 있음, 부팅의 마지막 단계로 6개의 가상 콘솔이 실행 ALT+F1~F6 누르면 해당 번호의 가상콘솔 보여짐


출처: https://www.linux.co.kr/lecture/lec_linux_01/lec-data/08data.pdf
