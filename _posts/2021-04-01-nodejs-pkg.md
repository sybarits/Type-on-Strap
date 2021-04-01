---
layout: post
title: Nodejs Packaging
tags: [nodejs, node, pkg, package]
---

nodejs Excutable Binary 만들기

## pkg
nodejs로 앱을 만들고 나서 앱을 배포하려면 어떻게 할가? 배포 환경에 nodejs를 설치하고, 앱에서 사용한 module들을 설치한 후 앱을 실행하는 방법이 있겠다. 아니면 실행 가능한 하나의 바이너리로 만들어 배포 환경에서는 실행만 할 수 있으면 좋겠다. pkg 라는 모듈이 이런 고민을 해결해 준다.

```
npm i pkg --save-dev
```
pkg 모듈을 설치한다. 실제 앱에서 사용되는 모듈이 아니므로 --save-dev 옵션을 준다.

```json
{
  ...
  "bin": {
    "app": "./src/app.js"
  },
  "scripts": {
    "build": "pkg ."
  },
  ...
}
```

package.json에  위와 같이 추가 한다. bin에 들어있는  app 설정 값은 앱의 엔트리 포인트이다. script의 build의 설정 값은 아래 명령을 실행할 때 build 에 쓰이는 값이다. 빌드를 위한 명령은 아래와 같다.
```
npm run build
```
이렇게 하면 실행 가능한 바이너리 파일이 생성 된다.

배포 환경에서는 이 바이너리를 실행만 해주면 끝이다. nodejs를 설치할 필요도, 모듈을 설치할 필요도 없다.


출처: https://www.npmjs.com/package/pkg  
https://m.blog.naver.com/PostView.nhn?blogId=rnjsrldnd123&logNo=221598759112&proxyReferer=https:%2F%2Fwww.google.com%2F  
https://blog.outsider.ne.kr/1379  
