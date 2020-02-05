---
layout: post
title: Android App Release Build
tags: [android, build, compile, zipalign, app]
excerpt_separator: <!--more-->
---

## Android Studio Release Build

안드로이드 스튜디오는 기본적으로 Gradle을 사용하여 빌드를 한다. 안드로이드 개발자 공식 홈페이지에서 안드로이드 스튜디오에서 빌드를 하면 출시용 패키지 빌드를 손쉽게 할 수 있다고 소개한다. 아래와 같이 릴리즈용 APK와 디버그용 APK의 차이점을 설명한다.  
- 애플리케이션을 사용자에게 출시하려면 사용자가 Android 구동 기기에서 설치하고 실행할 수 있는 출시용 패키지를 만들어야 합니다. 출시용 패키지는 디버그 APK 파일과 동일한 구성요소, 즉 컴파일된 소스 코드, 리소스, manifest 파일 등을 포함하며 동일한 빌드 도구를 사용하여 빌드됩니다. 그러나 디버그 APK 파일과는 달리 출시용 APK 파일은 고유 인증서로 서명되며 zipalign 도구로 최적화됩니다.  
  
실재로 생성된 파일의 크기를 보면 릴리즈APK의 크기가 더 큰것을 확인 할 수 있다. APK 구성에 들어가는 정보가 릴리즈용 APK가 더 많음에도 용량이 더 적은 이유는 zipalign 도구를 이용해 APK를 최적화 하고 있기 때문일까. 아래는 공식 홈페이지에 있는 zipalign에 대한 설명이다.
- zipalign은 Android 애플리케이션(APK) 파일의 중요한 최적화 기능을 제공하는 보관 파일 정렬 도구입니다. zipalign의 목적은 압축되지 않은 모든 데이터가 파일 시작 부분에 맞게 특정 정렬이 적용된 상태로 시작되도록 하는 것입니다. 구체적으로 이미지, 원시 파일 등 APK 내 압축되지 않은 모든 데이터가 4바이트 경계에 맞게 정렬됩니다. 따라서 정렬이 제한된 바이너리 데이터가 포함되어 있어도 mmap()을 사용하여 모든 부분에 직접 액세스할 수 있습니다. 장점은 애플리케이션을 실행할 때 사용되는 RAM의 양이 줄어든다는 것입니다.  
  
  
  
출처: https://developer.android.com/studio/publish/preparing?hl=ko  
https://developer.android.com/studio/command-line/zipalign?hl=ko
