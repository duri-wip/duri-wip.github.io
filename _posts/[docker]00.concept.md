---
title: "Docker 개념과 구조"
excerpt: "클라우드 네이티브와 도커 컨테이너"

categories:
  - Docker
tags:
  - [Docker]

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-10-01
---

## 개념

복잡한 리눅스 애플리케이션을 여러 컨테이너로 통합하여 실행할 수 있고, 컨테이너를 공유할 수 있다. 마이크로서비스의 개념.

### 클라우드 네이티브

CNCF 에서 만든 용어. 리눅스의 산하 기관임. 조직이 클라우드와 같은 동적인 환경에서 확장 가능한 애플리케이션을 개발하고 실행할 수 있게 해주는것. 특히 컨테이너, 서비스매치, 마이크로 서비스, 불변 인프라, 선언형 api기술을 활용한다.
느슨하게 결합된 시스템을 가능하게 하고, 최소한의 노력으로 변경사항을 반영할 수 있도록 도와줌. 회복성도 좋다!
참고 : cncf.io
모노리틱 환경에서 마이크로 서비스 환경으로 이동하면서, 고객의 다양한 요구사항을 커스텀해서 받아줄 수 있도록 운영환경도 물리서버 -> 가상서버 -> 컨테이너 순으로 변화했다.

### 컨테이너

애플리케이션과 운영환경이 모두 들어있는 독립된 공간. 소스코드, 소스코드가 운영되기 위한 스크립트가 함께 들어있어 컨테이너 각각이 독립적으로 움직이면서 애플리케이션을 독립적으로 운영할 수 있도록 해준다. vm 가상화보다 경량화된 가상 머신 기술이다.

![Docker Container Architecture](https://github.com/duri-wip/Docker101/blob/main/images/docker-container-architecture.png)

이런느낌

![Untitled](https://github.com/duri-wip/Docker101/blob/main/images/docker-inside-architecture.png)

**컨테이너를 공유하는 방식 :** 도커파일을 만들어서 빌드 → 이미지 생성 → 이미지를 등록(도커 레지스트리에 푸시)

컨테이너 기술은 lxc 방식을 활용해서 만들었다.

lxc : linux container. 리눅스 커널에서 제공하는 기능. 커널 명령어를 통해서 하드웨어 가상화 없이 격리된 환경에서 프로세스를 실행하는 원리.

- chroot : 독립된 Container 환경 구성
  = pid 1을 제공하고 방화벽을 닫아 격리
- cgroups : 생성된 Container에 자원 할당
  = 컨트롤 그룹 자원(cpu, memory, disk, network)를 할당한다.
  근데 자원 할당에 제한이 없으므로 무제한으로 할당될수 있으니 이걸 주의해야함
- network namespaces : container에 IP 등을 배치하여 독립된 가상 환경을 제공
  = 운영체제의 동작과 연관되어 샌드박스를 컨테이너 내부에 배치하는 기능을 말한다.

![Untitled](https://github.com/duri-wip/Docker101/blob/main/images/vm-versus-container.png)

- docker d : docker daemon
  - docker CLI제공
  - swarm kit : 도커 클러스터 생성
  - logs mgmt : 모든 로그 관리
  - storage mgmt : 도커 볼륨. 저장소를 관리하는 법
  - lib network : 도커 네트워크. 도커 0. 브리지 네트워크이며 맥 주소 기반의 통신을 수행함(L2)
  - build kit : 이미지 생성
  - dct : 보안 설정
  - image mgmt
- containerd
  - 런타임 엔진 및 컨테이너의 생명주기 관리
  - cri-o
- run C runtime
- 컨테이너 생성에 관여

도커 컨테이너 워크플로우

- 클라이언트 : 서비스
- 도커호스트 : 서비스 데몬이 동작
- 레지스트리 : 이미지가 저장.
  애플리케이션을 만들어서 레지스트리에 저장하고, 데몬에게 클라이언트 커맨드로 명령을 전달하면, 레지스트리에서 이미지를 다운로드 받고(풀) 실행하고(런) 업로드한다(푸시) 이미지를 만든다(빌드)

이미지: 읽기전용의 컨테이너 템플릿. 애플리케이션 실행환경, 소스, 런타임이 포함된 독립적 컨테이너 애플리케이션
/var/lib/docker/image, /var/lib/docker/overlay2에 이미지가 저장된다
컨테이너 이미지는 레이어로 구성됨. 각 레이어는 이미지로 구성되고 템플릿을 제공해줌. 이미지를 실행하면 컨테이너가 되는거. 고 실행중일때는 프로세스가 떠있는거
이미지 레이어: 이미지는 한개 이상의 읽기전용 레이어 집합임.
베이스 이미지부터 블록 쌓듯이 레이어가 배치되어 오버레이로 구현된다. 마지막 레이어에서 read write 레이어가 가장 상단에 배치되어 전체 레이어를 머지한다. 이때 union mount merge를 하는데 이게 오버레이라는거심..
오버레이~!! 정말 신기하다 그런게 오버레이구나
컨테이너 이미지는 수정되지 않고 컨테이너 자체에 기록되는거. 그래서 컨테이너 삭제되면 수정 내용도 사라집니다.

컨테이너 레지스트리의 종류
퍼블릭 : 허브 도커
퍼블릭한 순서대로
퍼블릭레지스트리 > 클라우드 레지스트리 > 프라이빗 레지스트리 > 레파지토리

- amazon ECR
- Azure container registry

온프렘으로 운영 가능(= 프라이빗 레지스트리)
-Harbor
-Gitlab container registry

- Docker registry

도커 vs podman
도커는 데몬 형태로운영되기 때문에 데몬이 중지되면 컨테이너 관련 앱 서비스가 중단된다는 문제가 있음. 데몬 의존성이 강함. 또 데몬은 루트 계정으로 실행되기 때문에 서비스를 루트 계정이 아니면 운영할 수 없음. 이런 문제를 해결하기 위해 파드만이라고 하는 엔진이 출범.
Oci 호환 컨테이너 관리 툴. 도커와 유사하기 때문에 명령어도 비슷함. 데몬 형태로 운영되지 않기 때문에 독립적으로 레지스트리, 컨테이너, 커널에 접근이 가능하다는 특징이 있음. 루트 권한이 필요하지 않음.

## 도커를 활용한 애플리케이션 개발 과정

1. 앱 개발
2. write docker file
3. create images defined at Dockerfiles

- get base images from remote repository
- build image on local

4. define services by writing
5. run containers and compose app
6. test
7. push or continue developing -> 1

```
docker container create
docker container start
docker container run
docker container stop
docker container rm
```
