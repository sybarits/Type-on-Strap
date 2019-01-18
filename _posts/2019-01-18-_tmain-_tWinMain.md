---
layout: post
title: _tmain, _tWinMain
tags: [main, wmain, _tmain ]
excerpt_separator: <!--more-->
---

main -> 윈도우 시스템에서 콘솔 응용프로그램 또는 DOS 응용프로그램의 엔트리 포인트  
WinMain -> 윈도우즈 시스템에서 GUI 응용프로그램의 엔트리 포인트  
wmain -> 유니코드 버전의 main  
wWinMain -> 유니코드 버전의 WinMain  
_tmain -> 매크로 환경에 따라 main 과 wmain 으로 전처리  
_tWinMain -> 매크로 환경에 따라 WinMain 과 wWinMain 으로 전처리  
  
  
WinMain의 MSDN 설명은 아래와 같다.
> Your WinMain should initialize the application, display its main window, and enter a message retrieval-and-dispatch loop that is the top-level control structure for the remainder of the application's execution.

짧게 해석 하자면 어플리케이션 윈도우 생성과 메시지 처리 루프 가동

-끝-
