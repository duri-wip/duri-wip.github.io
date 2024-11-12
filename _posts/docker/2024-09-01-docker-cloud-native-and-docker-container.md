---
title: "클라우드 네이티브와 도커"
excerpt: "도커 컨테이너를 활용한 애플리케이션 개발 과정"

categories:
  - docker
tags:
  - [Docker]

permalink: /docker/cloud-native-and-docker-container/

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-10-01
---
# 도커란?

도커를 활용하면 복잡한 리눅스 애플리케이션을 여러 컨테이너로 분리하여 실행할 수 있으며, 각 컨테이너는 독립적으로 동작하게 된다. 이를 통해 마이크로서비스 아키텍처를 지원하는 애플리케이션을 쉽게 구현할 수 있다. 


## 클라우드 네이티브

클라우드 환경에서 확장 가능한 애플리케이션을 개발하고 실행할 수 있게 돕는 기술을 통칭하며, 특히 컨테이너, 서비스매치, 마이크로 서비스, 불변 인프라, 선언형 api기술을 활용한다.
느슨하게 결합된 시스템을 가능하게 하며, 빠르고 유연하게 변경 사항을 반영할 수 있고, 높은 회복성을 제공한다. 

리눅스 재단의 하위기관인 CNCF(Cloud Native Computing Foundation)에서 만든 용어.

참고 : [cncf.io](cncf.io)

![vm vs container](/assets/images/post_img/docker-container-architecture.png)
모노리틱 환경에서 마이크로 서비스 환경으로 이동하면서, 고객의 다양한 요구사항을 커스텀해서 받아줄 수 있도록 운영환경도 물리서버 -> 가상서버 -> 컨테이너 순으로 변화했다.

## 컨테이너

애플리케이션과 그 실행환경을 함께 묶어 제공하는 독립적인 패키지다. 소스코드, 소스코드가 운영되기 위한 스크립트가 함께 들어있어 컨테이너 각각이 독립적으로 움직이면서 애플리케이션을 독립적으로 운영할 수 있도록 해준다. vm 가상화보다 경량화된 가상 머신 기술이다. 도커는 리눅스 커널에서 제공하는 LXC(linux containers)기술을 바탕으로 컨테이너를 구현한다.


### 주요기술

- **chroot** : 독립된 Container 환경 구성
  = pid 1을 제공하고 방화벽을 닫아 격리
- **cgroups** : 생성된 Container에 자원 할당
  = 컨트롤 그룹 자원(cpu, memory, disk, network)를 할당한다.
  근데 자원 할당에 제한이 없으므로 무제한으로 할당될수 있으니 이걸 주의해야함
- **network namespaces** : container에 IP 등을 배치하여 독립된 가상 환경을 제공
  = 운영체제의 동작과 연관되어 샌드박스를 컨테이너 내부에 배치하는 기능을 말한다.

### 도커 컨테이너 워크플로우

![](/assets/images/post_img/docker-image-lifecycle.png)

1. 클라이언트 : 도커 명령어를 통해 요청을 전달
2. 도커 호스트 : 서비스 데몬이 이미지를 실행 및 관리
3. 레지스트리 : 이미지를 저장 및 배포하는 역할

### 도커와 podman

도커는 데몬 형태로 실행되기 때문에 데몬이 중지되면 모든 컨테이너도 중단되는 문제가 있다. 이를 해결하기 위해 podman이 등장했으며, 데몬에 의지하지 않고 컨테이너를 관리할 수 있다. podman은 루트 권한 없이도 컨테이너를 운영할 수 있다. 


## 이미지

이미지는 읽기 전용 템플릿이라는 점에서 컨테이너와 구분된다. 
즉 이미지는 애플리케이션의 실행 환경과 소스코드, 필요한 라이브러리를 모두 포함하고 있지만 실행되고 있는 상태는 아닌것이다. 이미지를 실행하면 컨테이너가 생성되며 이 컨테이너가 애플리케이션을 실행하는 프로세스가 된다. 

**/var/lib/docker/image**, **/var/lib/docker/overlay2**에 이미지가 저장된다.

![](/assets/images/post_img/Multilayer%20image.png)

컨테이너 이미지는 **레이어**로 구성되는데, 각 레이어는 베이스 이미지 위로 쌓이며 오버레이로 구현된다. 마지막 레이어에서 read write 가장 상단에 배치되어 전체 레이어를 머지한다. 



컨테이너 이미지는 수정되지 않고 컨테이너 자체에 기록되며, 따라서 컨테이너가 삭제되면 수정 내용 즉 레이어도 삭제된다.


## 컨테이너 레지스트리의 종류
![](/assets/images/post_img/container-registry.png)

### 1. 퍼블릭 레지스트리
누구나 접근할 수 있는 공개적인 이미지 저장소. 오픈소스 프로젝트나 기본적 애플리케이션을 위한 이미지를 제공한다. 
 
#### docker hub

### 2. 클라우드 레지스트리
클라우드 서비스 제공업체에서 관리하는 레지스트리. 퍼블릭 레지스트리와 비슷하게 이미지를 관리할 수 있지만 주로 해당 클라우드 환경과 통함하여 사용한다. 기업이나 팀 단위로 사용하며 자동화 파이프라인과 쉽게 연동된다는 장점이 있다. 


#### Amazon ECR
#### Azure container registry(ACR)

### 3. 프라이빗 레지스트리
기업 내부에서 외부에 공개되지 않는 비공개 컨테이너 이미지를 저장하기 위해 사용한다. 팀이나 조직 내에서만 이미지를 공유할 수 있고 온프레미스 환경에서도 운영 가능하다. 

#### Harbor
CNCF에서 관리하는 오픈소스 프라이빗 컨테이너 레지스트리. 이미지 스캔, 접근제어, 감사 기능 등 보안기능을 제공한다. 온프레미스 환경에서 운영 가능하다.

#### gitlab container registry
gitlab에서 제공하는 레지스트리로 CI/CD 파이프라인과 통합되어 개발과 배포를 자동화할 수 있다는 장점이 있다 .

#### docker registry
도커 자체에서 제공하는 레지스트리로 로컬, 프라이빗 환경에서 자체적으로 운영할 수 있다. 