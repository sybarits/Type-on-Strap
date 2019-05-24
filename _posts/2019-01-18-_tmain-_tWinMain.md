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



```C
int WINAPI \_tWinMain(HINSTANCE hInstance, HINSTANCE hPrevInst,
                      PTSTR pszCmdLine, int nCmdShow) 
{
  g_hInstance = hInstance;
  
  ...                
   MSG msg;
    BOOL bRet;

    while(1)
    {
        bRet = GetMessage(&msg, NULL, 0, 0);

        if (bRet > 0)  // (bRet > 0 이면 메시지가 처리되어야 한다.)
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }
        else if (bRet < 0)  // (bRet == -1 은 에러를 뜻한다.)
        {
            // 에러에 대한 처리나 로그 남김이나 프로그램 종료 등의 구문.
            // ...
        }
        else  // (bRet == 0 은 프로그램 종료를 의미한다.)
        {
            break;
        }
    }
    return msg.wParam;
```

여기서 hInstance 매개변수를 '해당 애플리케이션의 현재 인스턴스에 대한 핸들(A handle to the current instance of the application)' 이라고 정의하고 있다. 인스턴스(Instance)라는 단어 그대로의 의미는 '사례', '경우' 등의 뜻을 갖고 있으며, 어떤 구체적인 사실을 의미한다. 프로그래밍의 관점에서 볼 때 인스턴스는 어떤 하나의 '실체'를 뜻한다고 볼 수 있으며, 이 경우 무엇에 대한 실체인가가 중요하다. 그리고 그 무엇을 규정하는 것이 프로그래밍 언어에서 제공하는 다양한 데이터 타입이 될 것이다. 이 타입에는 C나 C++ 등에서 기본적으로 제공하는 int, double 등의 프리미티브 타입뿐만 아니라 여러분이 정의하는 구조체나 클래스 등도 포함된다. 하지만 이러한 타입은 칸트의 표현을 비리자면 내용없는 공허한 '형식'만 제공할 뿐 그자체로는 아무것도 아니다. 이 공허한 형식이 맹목적이지 않도록 내용을 갖추려면 메모리 상에서 자신의 공간을 확보해야함 하는데, 특정 타입으로 선언된 변수가 프로세스의 가상 주고 공간에 자신의 영역을 확보한 상태가 인스턴스로서의 '실체'가 되는 것이다.  
WinMain 의 hInstance 매개변수는 바로 ImageBase 필드와 관련이 있다. hInstance의 값은 PE가 로드된 메모리의 시작 번지를 가지며, 이 값은 디스크 상의 PE가 아니라 메모리에 로드된 PE의 ImageBase 필드 값이 된다.  

전형적으로 이벤트 루프는 가상 키 입력을 문자열로 변환하기 위한 TranslateMessage()를 각 메시지에 대해 호출한다. TranslateMessage() 호출은 필수는 아니지만 호출되지 않는 경우 문제가 발생할 소지가 있다. 메시지 루프는 DispatchMessage()는 반드시 호출해야 한다.

메시지 루프는 핸들링하고 있는 메시지 자체에 대해서 직접적으로 처리하지는 않는다. 메시지 루프는 DispatchMessage()를 통해서 메시지들을 해당 메시지가 전달되어야 하는 윈도우의 "윈도우 프로시저"에 전달함으로써 메시지를 전파(Dispatch; 디스패치)시킨다. ("윈도우 프로시저"는 윈도우 클래스가 등록될 때 함께 등록되는 콜백 프로시저를 뜻한다. 여러 개의 창이 하나의 윈도우 프로시저를 공유하는 것도 가능하다.)

프로그램 코드를 통해서 윈도우 프로시저에 직접 메시지를 보낼 수도 있으며, 시스템 큐를 거치지 않고 바로 전달되는 이런 메시지들을 가리켜 비큐 메시지(Nonqueued Messages)라고 부른다.

메시지 루프는 윈도우즈 GUI 프로그램에 반드시 필요한 부분이지만 엄격한 규칙이나 요구사항은 없다. 메시지를 받아들이고 디스패치하는 부분은 프로그램 코드의 어느 부분에라도 구현될 수 있으며 다양한 옵션이 있다. 예를 들어 GetMessage()는 메시지가 들어올 때까지 스레드를 블록시키지만, GetMessage() 대신 PeekMessage()와 같이 메시지가 있을 때는 읽어들이지만 메시지가 없을 경우에는 즉시 반환하는 논블록킹(Non-blocking) 함수를 이용하는 수도 있다. 또한 WaitMessage()를 이용하면 큐에 메시지가 있을 때까지 스레드를 슬립시키는 것도 가능하다.

한편 윈도우 폼, 윈도우 프레젠테이션 파운데이션, MFC, 델파이, Qt와 같은 최근의 GUI 프레임워크로 프로그램을 개발할 때에는 프로그래머가 메시지 루프를 별도로 구현할 필요가 없다. 하지만 프레임워크가 내부적으로 메시지 루프와 같은 형태로 사용자 입력을 처리하는 부분을 구현하고 있으며, 깊이 있는 프로그래밍이 필요한 경우 프로그래머에 의해 수정될 여지가 있다.

출처: MSDN, 리버스 엔지니어링 이호동 저, wikipedia
-끝-
