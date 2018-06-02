---
layout: post
title: Git 체리픽으로 특정 커밋 하기
tags: [git, cherry-pick ]
excerpt_separator: <!--more-->
---

master, new-dev 처럼 두개의 브랜치가 있다. 예를 들어 오타가 발견되었다거나 공통적으로 기능을 추가하는 경우처럼 두개의 브랜치에 공통적으로 반영해야 할 경우.  

  
```
git checkout master
git commit -am "hohoho"
```
위처럼 하나의 브랜치에 커밋을 하고 해당 커밋 id를 아래처럼 다른 브랜치에 적용해준다.
```
git checkout new-dev
git cherry-pick 7432f3
```
  
  
언제 cherry-pick을 사용할까? 기존의 Pull Request가 머지가 안된 경우 체리픽을 사용하여 현재 브랜치에 반영한뒤 계속해서 필요한 작업을 이어나갈 때. 각각의 브랜치가 master역할을 할 경우 머지를 하지 않고 변경사항 커밋을 가져오고 싶을 때.

  -끝-
