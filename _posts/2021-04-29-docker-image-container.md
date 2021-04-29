---
layout: post
title: Docker Image and Container
tags: [docker, image, container]
---

## Docker
도커는 컨테이너 기반 가상화 도구이다. 도커는 리눅스 상에서 프로세스를 컨테이너 단위로 격리해서 관리 할 수 있도록 해준다. 하드웨어를 가상화하는 기술과는 달리 프로세스를 격리하는 방법으로 가상화를 구현한다. 컨테이너는 별도의 파일 시스템과 함께 cgroup, namspace 등을 이용해 호스트위에서 별도의 머신이 돌아가는 것 처럼 보인다. 본문에서는 도커나 컨테이너의 기술 상세에 대해 이야기 하지 않고 도커를 사용하는 방법에 대해 알아보고자 한다.

## Docker Image
도커 이미지는 어플리케이션을 실행하기 위한 환경을 담고 있습니다. 이 이미지를 도커에서 실행하면 컨테이너가 생성되고 그 컨테이너 안에서 어플리케이션이 실행됩니다. 도커 이미지는 변경할 수 없고 변경을 위해서는 별도의 이미지를 생성해야 한다. 도커 이미지를 추가하는 법은 세가지가 있다.

### pull 명령어를 통해 이미 만들어져 있는 이미지를 가져오는 법
nginx 이미지를 가져와 보자.
```
docker pull nginx
docker images
```
nginx 이미지를 받아온 것을 확인 할 수 있다.

### 컨테이너 변경사항을 기반으로 커밋을 통해 이미지를 만드는 법
우분투 이미지 기반으로 nginx를 실행 할 수 있는 이미지를 만들어 보자
```
docker pull ubuntu
docker run -it ubuntu bash
apt update
apt install -y nginx
nginx -v
```
컨테이너에 nginx가 설치 된것을 확인 할 수 있다. docker diff $containerid 를 이용하면 컨테이너의 변경 사항을 확인해 볼 수도 있다. 이제 이 컨테이너를 커밋해보자.
```
docker commit $containerid ubuntu-nginx
docker images
```
커밋을 통해 이미지를 추가해 보았다.

### 도커파일(Dockerfile)을 이용해 이미지를 빌드하는 법
Dockerfile이라는 파일 내에 이런 저런 내용을 채워 놓고 빌드하는 방법이 있다.
```
mkdir ubuntu-nginx
cd ubuntu-nginx
```
이렇게 디렉토리 하나 만들어 그안에서 Dockerfile을 아래와 같이 작성한다.
```
FROM ubuntu
RUN apt update &&\
  apt install -y nginx
```
도커파일이 있는 경로에서 아래와 같이 빌드 명령어를 실행한다.
```
docker build -t ubuntu-nginx-build .
docker images
```
빌드를 통해 이미지를 추가할 수 있다. 보통은 첫번째 방법을 통해 이미지를 추가하고 이미지의 변경이 필요할 경우 세번째 방법을 이용한다. 나도 세번째 방법을 이용해야겠다. Dockerfile은 FROM 으로 시작한다. 기본이 되는 이미지를 지정하기 위한 내용이다. RUN은 명령을 직접 실행하기 위한 지시자이다. RUN 뒤에 쓰여있는 명령어가 쉘에서 실행된다고 보면 된다. 그 외 WORKDIR 지시자는 작업이 진행되는 디렉토리를 변경한다. 도커파일에 대해서는 따로 더 자세하게 알아보기로 한다.
```
WORKDIR /tmp
```
처럼 사용하면 이후 실행 되는 작업들이 /tmp에서 진행 된다. ENV 라는 지시자도 있는데 컨테이너 실행 환경에 적용되는 환경변수의 값을 지정할 수 있다. 
```
ENV JAVA_HOME /usr/lib/jvm/openjdk1.8
```
이런식으로 설정하고 컨테이너에서 싱행되는 어플리케이션은 이 환경변수 값을 받아 동작을 제어한다. 도커에서 권장 하는 방식이다. EXPOSE는 컨테이너가 오픈할 포트를 지정해준다.
```
EXPOSE 8080
```
이 값은 컨테이너에서 실행되는 어플리케이션이 사용하는 포트를 적어주고 호스트에서 컨테이너에 있는 어플리케이션을 포트 8080을 통해 접근 할 수 있다.

### 도커 이미지 삭제
도커 이미지를 만들어 봤으니 삭제도 해보자
```
docker rmi $imageid
```
이미지를 삭제하기 위해서는 해당 이미지를 기반으로 하는 컨테이너도 모두 지워주어야 한다.

## Docker Container
도커 이미지는 어플리케이션을 실행하기 위한 환경이었다. 이런저런 파일들의 모음이라고 봐도 된다. 이미지를 실제 프로세스로 띄운것이 컨테이너이다. 컨테이너에서 수정된 내용은 이미지에 영향을 미치지 않고 컨테이너가 종료되면 변경 내용이 사라진다.

### 도커 컨테이너 실행
```
docker run -d -p 8080:8080 ubuntu-nginx
```
-d 컨테이너를 데몬으로 실행하겠다는 옵션이다. -p 8080:8080 이렇게 쓰면 컨테이너의 8080 포트(:구분자의 뒤)와 호스트의 8080 포트(: 구분자의 앞)가 포트포워드 된다. 하나의 이미지로 여러 컨테이너 생성이 가능하다. 이때 포트는 겹치지 않도록 한다.

### 도커 컨테이너 접속
```
docker exec -it ubuntu-nginx /bin/bash
```
exec 명령어는 도커 컨테이너 안쪽으로 명령어를 전송한다. 명령어는 컨테이너 이름뒤에 /bin/bash이다. i 옵션은 표준 입출력 사용, t 옵션은 가상 tty 사용을 의미 한다. 터미널을 통해 ssh 접근을 한것과 같은 환경을 사용할 수 있다.

### 도커 컨테이너 확인 및 삭제
```
docker ps -a
docker rm $containerid
```
docker ps 명령어를 통해 컨테이너들을 확인한다. -a 옵션을 주면 실행중이 아닌 컨테이너도 확인할 수 있다.
  
이제 시작이다.
  
  
출처: https://www.44bits.io/ko/post/easy-deploy-with-docker  
  https://hiseon.me/linux/ubuntu/install-docker/
  https://kscory.com/dev/nginx/install
  https://itholic.github.io/docker-enter-container/
  
  
  
