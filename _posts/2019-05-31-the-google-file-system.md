---
layout: post
title: The Google File System
tags: [google, file, system, bigdata]
excerpt_separator: <!--more-->
---

구글 파일 시스템에 대해서 공부할 일이 있어서 요약본을 남긴다.

## The Google File System

1. Introduction  
  
Google에서 요구하는 데이터 처리 요수사항을 충족하기 위해 Google File System(GFS)를 설계하고 구현했습다. 성능, 확장성, 안정성, 가용성을 목표한다.
  
    
2. Design Overview  
  
2.1 Assumtion  
GFS는 다음과 같은 가정을 기반으로 설계한다.
* 일반적인 IBM호환 부품을 이용한다.
* 시스템은 적은 수의 큰 파일을 저장한다. 작은 파일은 지원하지만 최적화 안한다.
* 작업 부하는 주로 두 종류의 읽기입니다. 수백 MB를 읽는 큰 스트리밍 읽기와 작은 랜덤 읽기입니다.
* 많은 순차적 쓰기가 있다. 일단 기록되면 파일이 거의 수정되지 않는다.
* 동기화 오버헤드를 최소화 한다.
* 낮은 레이턴시보다 높은 대역폭이 중요하다.
  
2.2 Interface  
POSIX와 호환되지 않는 interface를 제공한다. 파일 생성, 삭제, 열기, 닫기, 읽기, 쓰기 작업을 지원한다. 스냅샷은 파일 또는 디렉토리 트리의 복사본을 만든다. 레코드 추가 기능을 이용하여 많은 클라이언트가 lock 없이 데이터를 하나의 파일에 동시에 추가할 수 있다.
  
2.3 Architecture  
하나의 master와 여러개의 chunkservers로 이루어 진다. 각각의 서버는 범용 linux 시스템이다. 파일은 고정크기의 chunk로 나뉘고 각 청크는 64비트의 청크핸들로 식별된다. 파일이 저장될 때 chunkserver는 linux file로 저장한다. chunk는 여러 chunkserver에 저장이 되며, 기본적으로 세개의 복제본을 저장한다.  
master는 모든 파일 시스템의 메타 데이터를 유지 관리한다. file과 chunk의 매핑 및 위치 정보를 포함한다. 클라이언트는 메타데이터 작업을 위해 master와 통신하지만 데이터 관련 통신은 chunkserver와 직접한다. 클라이언트와 chunkserver는 파일 데이터를 캐시하지 않는다. 파일이 너무 크기 때문이다.
  
2.4 Single Master  
단일 마스터를 사용하여 설계를 단순화 한다. 파일시스템의 모든 메타데이터를 한곳에서 다루기 때문에 병목현상이 발생하지 않도록 한다. 클라이언트는 마스터에게 file이 저장되어있는 chunk의 위치와 chunk handle에 대한 정보를 묻고 해당 정보를 캐시한 후 chunkserver와 직접 통신한다.
  
2.5 Chunk Size  
Chunk Size는 64MB 이다. 각 chunk의 복재본들은 chunkserver에 linux file로 저장된다. chunk 가 크면 chunk의 개수가 줄어든다. 좋은 점이 있다. chunk단위로 위치에 대한 질의를 master-client가 하기 때문에 master의 작업 부하를 줄인다. chunk하나에 대한 많은 작업을 가능성이 높기 때문에 chunkserver와의 TCP연결을 유지하여 네트워크 오버헤드를 줄인다. 메타데이터의 양이 작아진다. chunk가 크기 때문에 많은 클라이언트가 하나의 chunk에 접근하려 할 수도 있다는 단점이 있다.
  
2.6 Metadata  
master는 file 및 chunk namespace, file-chunk mapping 정보, chunk복사본의 위치와 같은 세가지 주요 메타 데이터를 가진다.
  
2.6.1 In-Memory Data Structures  
메타 데이터는 메모리에 저장되어 작업이 빠르다. 마스터는 64MB의 chunk 하나에 64바이트 미만의 메타데이터를 유지한다.file namespace 데이터는 접두사 압축을 사용하여 파일당 64바이트이다.
  
2.6.2 Chunk Locations  
master는 시작시와 주기적으로 chunkserver의 상태정보를 요청하여 metadata를 최신으로 유지한다.
  
2.6.3 Operation Log  
메타 데이터의 변경사항에 대한 기록을 남긴다. 로그가 일정크기 이상을 커질때마다 상태를 점검하고 체크포인트 한다.
  
2.7 Consistancy Model 일단 통과  
2.7.1 Guarantees by GFS  
file namespace 변경(예: 파일 생성)은 원자적이다. namespace locking을 통해 구현 된다.
  
    
3. SYSTEM INTERACTIONS  

3.1 Leases and Mutation Order  
Mutation은 chunk의 내용이나 메타 데이터를 추가 하거나 쓰는 작업을 의미한다. 마스터는 클라이언트가 mutation을 요청 할 경우 chunk 중 하나에 Lease를 부여한다. lease의 부여시간은 기본 60이며 시간을 연장하기위해 heartbeat 메시지를 통한다.  
1.client 가 master에게 mutation 요청
2. master는 해당 chunk가 lease되어있는지 확인하고 없으면 lease를 부여 후 chunk의 모든 복제본의 위치를 반환한다.
3. 클라이언트는 모든 복제본에 데이터를 넣는다.
4. 모든 복제본이 데이터 수신을 허락하면 클라이언트는 쓰기 요청을 보낸다.
5. 주 복제본이 보조 복제본에게 쓰기 요청을 보낸다.
6. 보조 복제본은 작업을 완료했음을 주 복제본에게 보낸다.
7. 주 복제본이 클라이언트에게 모든 작업에 대한 결과를 응답하고 클라이언트는 결과를 확인하고 실패한 부분은 재시도한다.
  
3.2 Data Flow  
앞서 본 순서 처럼 제어의 흐름과 데이터의 흐름이 분리된다. 데이터는 트리에 분산되지 않고 chunkserver의 체인 흐름을 따라 이동한다. 한번에 여러 chunkserver에 데이터를 보냄으로 대역폭을 나누지 않고 하나의 데이터 이동 요청을 가능한 빠르게 처리한다. IP를 통해 "거리"를 추정할 수 있는 네트워크 토폴로지를 사용한다.  
TCP 연결을 통한 데이터 전송을 파이프로 연결하여 하나의 chunkserver가 데이터를 받기 시작하는 즉시 다른 chunkserver에 데이터 전송을 시작한다. 전이중 네트워크를 사용하기때문에 수신률의 감소는 없다.
  
3.3 Atomic Record Appends  
GFS는 record append라 불리는 원자적 append를 지원한다. 여러 사용자가 race condition 없이 쓰기 동작이 가능한 unix의 O_APPEND 모드의 파일열기와 유사한 동작을 한다. 파일에 쓰려고 할 때 오프셋을 주어 파일을 쓰고 클라이언트에게 해당 오프셋을 리턴한다.
  
3.4 Snapshot  
스냅샷 작업은 파일이나 디렉토리 트리의 복사본을 거의 즉각적으로 만든다. 스냅샷 구현을 위해 표준 copy-on-write기술을 사용한다.
  
4. MASTER OPERATION  
  
마스터는 모든 네임 스페이스 작업, 모든 시스템의 chunk 복제 관리, chunkserver의 로드를 조정등 다양한 활동을 한다.
  
4.1 Namespace Management and Locking  
마스터의 작업을 지연하지 않기위해 namespace 영역에서 locking을 사용한다. GFS에서 namespace는 논리적으로 저체 경로 이름을 메타데이터에 매핑하는 조회 테이블로 나타낸다. 접두어 압축을 사용하여 테이블의 크기를 줄인다. namespace의 각 노드는 읽기-쓰기 잠금이 있다.
  
4.2 Replica Placement  
GFS는 분산 시스템이다. 여러개의 랙에 chunkserver가 분산되어 있다. chunk 복제 배치 정책은 신뢰성과 가용성을 극대화하고 네트워크 대역폭 사용을 최소화 하는 목적을 수행한다. chunk 복제본을 랙 전체에 분산하므로서 일부 랙이 다운이 될경우도 데이터를 보존할 수 있다.
  
4.3 Creation, Re-replication, Rebalancing  
chunk 복제본은 creation, re-replication, rebalancing의 세가지 이유로 생성된다. master가 chunk를 만들면 디스크의 여유가 있는 chunkserver에 배치한다. 하나의 chunkserver에 chunk가 연속적으로 많이 쓰여지는 것을 제한하고, 최대한 분산된 랙의 chunkserver로 복제본을 전한다. chunk 복제본의 수가 줄어들 경우 master는 chunk가 있는 chunkserver에 직접 복사를 요청한다. master는 주기적으로 복제본 배포상황을 검사하고 디스크 공간 및 로드의 균형 조정을 위해 복제본을 이동한다.
  
4.4 Garbage Collection  
GFS는 정기적으로 삭제된 파일와 chunk 의 삭제 기록을 확인하여 자원을 회수한다.
  
4.4.1 Mechanism  
응용프로그램에서 파일 삭제시 파일은 삭제 시점이 포함된 숨겨진 이름으로 바뀌고 마스터는 정기적으로 namespace를 검색하면서 숨겨진 이름의 파일을 제거한다. namespace에서 제거되면 해당 메타 데이터가 삭제되고 master와 chunkserver가 HeartBeat 통신 시 받은 메타 데이터에 없는 chunk의 복제본을 chunkserver는 삭제 한다.
  
5. Tault Tolerance and Diagnosis  
  
빈번하게 발생하는 장비의 오류를 극복하기 위한 방법에 대해 논한다.
  
5.1 High Availability  
GFS 클러스터는 빠른 복구와 복제를 통해 전체 시스템의 가용성을 높인다.
  
5.1.1 Fast Recovery  
마스터와 chunkserver는 정상종료와 비정상 종료를 구분하지 않고 종료 상태에 상관없이 상태를 복원하고 초단위로 시작하도록 한다.
  
5.1.2 Chunk Replication  
chunk는 여러 랙에 나뉘어 복제된다. 기본적으로 3개를 복제하며 사용자가 복제 수준을 지정 할 수 있다.마스터는 체크섬 확인을 통해 각 청크가 완전히 복제 되도록 한다.
  
5.1.3 Master Replication  
마스터도 신뢰성을 위해 복제된다. 로그와 체크포인트는 여러 시스템에 복제된다. mutation은 로그레코드가 디스크와 복제된 마스터에 전달이 된 후 커밋이 되었다고 간주된다. 하나의 마스터가 모든 mutation을 담당하고 가비지컬랙션 같은 작업은 다 같이 한다.
복제본 마스터는 기본 마스터가 다운 될 경우 파일시스템의 읽기 기능을 제공하여 읽기 가용성을 높인다.
  
5.2 Data Integrity  
각 chunkserver는 체크섬을 사용하여 자체적으로 저장된 데이터의 손상을 감지한다. chunk는 64KB 블록으로 분리되어있고 각각에는 32bit 체크섬이 있다. 체크섬도 메모리에 저장되고 로그와 함께 별도로 저장된다.
읽기를 요청받은 chunkserver는 해당 블록의 체크섬을 확인하고 오류 발생시 마스터에게 보고한다.
  
5.3 Diagnostic Tools  
GFS 는 중요한 이벤트와 모든 RPC 요청 및 응답을 기록하는 진단 로그를 생성한다. 요청과 응답에 대한 로그를 다른 컴퓨터에서의 로그와 대조함으로서 전체 상호작용 내역을 재구성하여 문제를 진단하고 성능 분석을 위한 자료로 사용되기도 한다. 로그는 순차적이고 비동기적으로 기록된다.
  
이하는 벤치마크와 운용과정에서 생긴 경험에 대한 기술이므로 생략
