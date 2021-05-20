---
layout: post
title: Unbuntu Mirror Server 구축
tags: [linux, mirror, server, ubuntu, debian]
---

  Mirror server 구축하는 방법에 대해서 알아본다.
  
### apt-mirror 설치
```
$ apt update
$ apt install apt-mirror
```
미러를 하기 위한 패키지를 설치했다.
  
### mirror 설정 파일 편집
/etc/apt/mirror.list 이 파일을 편집한다.
```
############# config ##################
#
set base_path   /var/spool/apt-mirror
#set base_path /ex-data/goorm-mirror
#
# set mirror_path  $base_path/mirror
# set skel_path    $base_path/skel
# set var_path     $base_path/var
# set cleanscript $var_path/clean.sh
# set defaultarch  <running host architecture>
# set postmirror_script $var_path/postmirror.sh
# set run_postmirror 0
set nthreads     20
set _tilde 0
#
############# end config ##############

#deb http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-src http://ftp.us.debian.org/debian unstable main contrib non-free

# mirror additional architectures
#deb-alpha http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-amd64 http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-armel http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-hppa http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-i386 http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-ia64 http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-m68k http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-mips http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-mipsel http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-powerpc http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-s390 http://ftp.us.debian.org/debian unstable main contrib non-free
#deb-sparc http://ftp.us.debian.org/debian unstable main contrib non-free

#clean http://ftp.us.debian.org/debian
deb-amd64  http://update.gooroom.kr/gooroom gooroom-1.0 main
clean  http://update.gooroom.kr/gooroom
```
  위 파일을 데비안에 apt-mirror를 설치했을 때 생성되는 파일을 편집한 것이다. set base_path /aaa/bbb/ccc 이부분을 편집함으로서 미러링 할때 패키지들이 저장되는 경로를 설정할 수 있다. apt-mirror를 설치할 경우 생성되는 경로는 /var/spool/apt-mirror 이다. 이 디렉토리 아래 var/clean.sh와 var/postmirror.sh 파일이 있는데 base_path를 바꿀 경우 이 파일들도 함께 복사해주면 미러링 시 별다른 알림 없이 진행된다.
  deb-amd64  http://update.gooroom.kr/gooroom gooroom-1.0 main 이런식으로 미러링하고자 하는 저장소를 추가한다.
  
### 미러링 하기
```
$ apt-mirror
```
위와 같이 명령어를 실행 하면 미러링이 시작 된다. base-path아래 mirror 디렉토리 아래 미러링을 한 경로 아래를 찾아보면 deb 파일들이 있는 것을 확인할 수 있다.
  
### 주기적으로 미러링 하기
크론탭을 이용해 주기적으로 미러링을 할 수 있도록 설정한다.
```
50 3  * * * /usr/bin/apt-mirror
```
직접 미러링을 진행해도 된다.
  
### http로 서비스 하기
nginx를 이용해 http 서비스를 해보자
```
$ apt-get install nginx
$ systemctl enable nginx
$ systemctl start nginx
$ vim /etc/nginx/conf.d/mirrors.conf
```
mirrors.conf 파일을 아래와 같이 수정한다.

```
server {
 listen 80;
 server_name domain.com; 
 root /var/mirrors/apt-mirror/mirror/archive.ubuntu.com;

location / {
 autoindex on;
 }
}
```
서버내임에 자신의 서버 설정과 같이 하고, root 에는 base_path 아래 mirror/미러링 패키지경로를 써준다. /etc/apt/mirror.list 설정에 따르면 /var/spool/apt-mirror/mirror/update.gooroom.kr 가 되겠다.
  
### 브라우저를 통해 확인
브라우저를 통해 자신의 아이피 혹은 server_name 뒤에 /var/spool/apt-mirror/mirror/update.gooroom.kr 아래 존재하는 디렉토리 이름을 적어준다. 브라우저를 통해 패키지 서버가 미러링 된것을 확인 할 수 있을 것이다.
  
출처: https://www.snoopybox.co.kr/1719
  https://kycfeel.github.io/2019/07/22/Docker%EB%A1%9C-%EC%89%BD%EA%B2%8C-%EC%98%AC%EB%A6%AC%EB%8A%94-%EB%82%98%EB%A7%8C%EC%9D%98-APT-%EB%AF%B8%EB%9F%AC-%EC%84%9C%EB%B2%84/
  https://computingforgeeks.com/creating-ubuntu-mirrors-using-apt-mirror/
  
