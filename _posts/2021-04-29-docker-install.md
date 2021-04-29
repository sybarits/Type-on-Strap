---
layout: post
title: Docker Install
tags: [mq, rabbitmq, queue, brocker, amqp, model]
---

## Docker Install
Docker를 우분투에 설치하는 방법에 대해서 알아본다.

## Docker-ce
docker.com에서 직접 배포하는 도커는 docker-ce와 docker-ee가 있다. ce는 개인 혹은 작은 팀이 사용하는 버전이고, ee는 실제 운용하는 회사에서 사용하는 버전이다. 이번에는 ce설치에 대해 알아본다. docker.io 로도 도커를 설치가 가능한데, 데비안/우분투에서 직접 배포하는 버전으로 최신 도커 버전이 아닐 수 있다. docker.com에서 배포하는 것과 혼동을 방지하기 위해 이름을 다르게 하였다고 한다.

## 도커 설치하기
아래 명령어들을 차례로 입력한다.
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
```
도커가 실행 중인 것을 확인 할 수 있다.  
데이안에 설치하는 것도 거의 유사하다. ubuntu를 debian으로 바꾸어 준다.

## 도커 그룹에 사용자 추가
도커는 기본 설정이 root 사용자만 실행 할 수 있다. 일반 유저를 추가하기 위해 다음과 같은 명령어를 입력한다.
```
sudo usermod -aG docker $USER
```
이 유저로 도커를 실행 할 때는 sudo 명령어를 추가한다.
```
sudo su - $USER
```
이렇게 실행 하면 sudo를 추가하지 않아도 된다.  
  
이제 시작이다.
  
  
출처: https://stackoverflow.com/questions/45023363/what-is-docker-io-in-relation-to-docker-ce-and-docker-ee  
  https://hiseon.me/linux/ubuntu/install-docker/
  
  
