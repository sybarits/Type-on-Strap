---
layout: post
title: Github Action
tags: [github, action, ci, cd]
---

github action에 대해 알아보자. 깃헙 액션에 대한 소개 페이지를 적절히 번역하였다.  

## Github Action
소프트웨어 개발 workflow 자동화 도구이다. workflow는 테스트 코드를 수행하거나 빌드와 배포를 수행하는 등의 작업들을 의미한다. 깃헙 액션을 이용해 CI/CD를 수행할 수 있다.  

## The Component of Github action 
### workflows
- 저장소에 추가할 수 있는 자동화 프로시저.
- 하나 이상의 job으로 이루어짐.
- 이벤트에 의해 시작됨.
- 깃헙에 있는 프로젝트를 빌드, 테스트, 패키징, 배포 할 수 있음.

### Events
- 커밋 혹은 이슈 등록, pull request와 같은 workflow를 시작시키는 특정 활동.
- 웹훅을 이용한 외부 이벤트 등록 가능.
- 이벤트에 대한 자세한 사항은 링크 참조(https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

### Jobs
- 하나의 runner에서 동작하는 steps의 집합.
- 기본적으로 workflow에 있는 job들은 병렬처리.
- job들을 순차적으로 처리하도록 설정 가능.
- 빌드 후 테스트하도록 job을 설정하고, 빌드 실패 시 테스트 안하도록 함.

### Steps
- job에 있는 개별 실행 명령어들.
- 하나의 step은 특정 동작이나 쉘 명령어가 될 수 있음.
- 같은 runner에서 동작하는 job에 각각의 step들은 서로 데이터를 공유함.

### Actions
- step을 action은 개별 명령어.
- workflow를 이루는 가장 작은 블럭 단위
- 커스텀 action을 생성하고 다른 사람이 만든 action 사용 가능.
- action을 사용하기 위해 step에 포함해야함.

### Runners
- runner는 GitHub Actions runner application이 설치되어있는 서버.
- 깃헙이 호스팅하는 러너를 사용할수도 있고 각자의 서버를 사용할 수 있음.
- 러너는 한번에 하나의 job을 실행하고 실행 상태, 로그, 결과를 깃헙에 리포드.
- 깃헙이 호스팅하는 러너는 우분투, 윈도우, 맥 기반이고 각 job은 가상 환경에서 실행 됨.


## Create an example workflow
1. 깃헙 저장소에 .github/workflows/ 디렉토리를 만든다.
2. 해당 위치에 learn-github-actions.yml 파일을 생성하고 아래와 같이 작성한다.

```
name: learn-github-actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```
3. 깃헙에 푸시한다.

## Understading the workflow file
앞서 작성한 파일의 내용을 살펴보자

```
name: learn-github-actions
```
이름은 액션 탭에 보여질 이름이다.
```
on: [push]
```
workflow를 실행시킬 특정 액션을 의미한다. 이 경우는 저장소에 push를 했을 때를 의미한다. 자세한 액션 설정은 링크를 참조한다. https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#onpushpull_requestpaths
```
jobs:
```
해당 workflow에서 수행 될 모든 job의 묶음을 의미한다.
```
check-bats-version:
```
하나의 job이름을 의미한다.
```
runs-on: ubuntu-latest
```
job이 수행될 runner를 의미한다. 이 경우 깃헙에서 제공하는 가상환경 상의 우분투를 의미한다.
```
steps:
```
check-bats-version job에서 수행 될 step들의 모음을 의미한다.
```
- uses: actions/checkout@v2
```
uses는 actions/checkout@v2이라는 액션을 사용하겠다는 것을 의미한다. 이 액션은 저장소를 체크아웃하고 다운받는다. 
```
- uses: actions/setup-node@v2
  with:
    node-version: '14'
```
이 step은 특정 버전의 노드를 설치하여 npm과 같은 명령어를 사용 가능하게 한다.
```
- run: npm install -g bats
```
run 키워드로 특정 명령을 runner에 내린다. 이 명령어는 bats를 npm으로 설치하는 것을 의미한다.
```
- run: bats -v
```
bats 명령어 실행을 한다.
  
  
  
  지금까지 간단하게 깃헙 액션을 사용하는 법에 대해 알아보았다. 공식 문서를 이용하여 더 다양한 활용 방법을 찾아볼 수 있겠다. 혹은 누군가 만들어 놓은 액션을 활용하는 것도 하나의 방법이다.
  
  
출처: https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions  
https://zzsza.github.io/development/2020/06/06/github-action/
