---
layout: post
title: cGroups & namespace
tags: [cGroups, namespace]
---

## cGroups
리눅스의 프로세스는 하나 이상의 스레드 모음으로 동작한다. cgroups는 프로세스와 스레드를 그룹화하여 그 그룹안에 존재하는 프로세스와 스레드에 대한 관리를 수행하기 위한 기능이다. 예를 들어 호스트 OS의 CPU나 메모리와 같은 자원에 대해 그룹별로 제한을 둘 수 있다.
cGroups로 컨테이너 안의 프로세스에 대해 자원을 제한 함으로써 특정 컨테이너가 호스트 OS의 자원을 모두 사용하는 일을 막는다.

### cGroups 서브 시스템
1. CPU - cpu 사용량 제한
2. CPUacct - CPU 사용량 통계
3. CPUset - CPU나 메모리 배치를 제어
4. memory - 메모리 사용량 제한
5. devices - 디바이스 엑세스 허가/거부
6. freezer - 그룹에 속한 프로세스 정지/재개
7. net_cls - 네트워크 제어 태그를 부가
8. blkio - 블록 디바이스 입출력량 제어


## namespace
* 하나의 시스템에서 프로세스를 격리시킬 수 있는 가상화 기술(각 별개의 독립된 공간을 사용하는 것처럼 격리된 환경을 제공하는 경량 프로세스 가상화 기술)
* VM에서는 각 게스트 머신별 독립적인 공간을 제공하고 서로가 충돌하지 않도록 하는 기능을 갖고 있다. 리눅스에서 동일한 역할을 하는 것이 namespace이며 커널에 내장되어있다.
* Hypervisor는 Hardware Resource를 가상화 하고 위에 올라가는 Guest OS에는 가상화 된 형태의 H/W를 제공하게 되므로 각 Guest OS는 완전히 다른 환경으로 분리된다. 하지만 namespace의 경우 Hardware Resource 레벨의 가상화가 아니고 동일한 OS와 동일한 Kernel에서 작동하게 된다. 단지 격리된 환경만 제공하는 것(namespace는 H/W 자원을 가상화하는 것이 아니라 Linux 내의 자원을 가상화 하는 것)
* unshare: namespace를 분리하는 명령어로 확인 가능

### Namespace 기능

1. PID namespace
- 프로세스에 할당된 고유한 ID를 말하며 이를 통해 프로세스를 격리할 수 있다.
- namespace가 다른 프로세스 끼리는 서로 액세스할 수 없다.
- systemd 프로세스만 가질 수 있는 PID 1번을 독립적을 추가 할당하며 동일한 OS에서 systemd 프로세스 뿐만 아니라 여러 프로세스가 PID 충돌없이 실행 가능하게 한다.

2. Network namespace
- namespace 간에 network 충돌 방지(중복 포트 바인딩 등)
- 네트워크 디바이스, IP 주소, Port 번호, 라우팅 테이블, 필터링 테이블 등의 네트워크 리소스를 namespace마다 격리시켜 독립적으로 가질 수 있다. 이 기능을 이용하면 OS 상에서 사용중인 Port가 있더라도 컨테이너 안에서 동일한 Port를 사용 가능하다.
- 네트워크 스택의 논리적 복사본을 효과적으로 생성하여 여러 namespace가 동일 포트로 다수의 서비스를 제공할 수 있다.

3. USR namespace
- UID, GID를 namespace 별로 독립적으로 가질 수 있도록 한다.
- 독립적인 사용자 할당, UID와 GID 격리

4. Mount namespace
- 호스트 OS와 namespace가 서로 다른 격리된 파일시스템 트리를 가질 수 있도록 한다.(마운트는 컴퓨터에 연결된 기기나 기억장치를 OS에 인식시켜 사용가능한 상태로 만드는 것을 의미)
- 호스트 파일시스템에 구해받지 않고 독립적으로 파일시스템을 마운트하거나 언마운트 가능

5. UTS namespace
- namespace 별로 호스트명이나 도메인명을 독자적으로 가질 수 있다.
- 각각의 리눅스 컨테이너가 자신의 식별자(hostname -f로 확인)를 유지관리하기 위해 호스트이름과 도메인이름을 네임스페이스별로 격리

6. IPC namespace
- 프로세스간 통신(IPC) 오브젝트를 namespace 별로 독립적으로 가질 수 있다.
- 프로세스간 데이터 교환 및 프로세스와 쓰레드간의 작업을 동기화하는 기능을 제공
- semaphore, file locking, mutex와 같은 자원에 대한 접근제어를 제공하며 컨테이너에서 실제 프로세스를 분리하기 위해서도 필요함



출처: https://galid1.tistory.com  https://lascrea.tistory.com/163
