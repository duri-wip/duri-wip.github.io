---
title: "spark cluster의 리소스 매니저"
excerpt: "리소스 매니저를 선택하는 방법"

categories:
  - spark
tags:
  - [Spark, Resource Manager]

permalink: /spark/selecting-resource-manager/

toc: true
toc_sticky: true

date: 2024-10-16
last_modified_at: 2024-10-16
---

# 스파크 클러스터에서 리소스 매니저란?

리소스 매니저는 스파크 클러스터에서 자원(cpu, 메모리 등)을 효율적으로 관리하고, 스파크 애플리케이션의 실행을 조율하는 시스템이다. 대규모 데이터를 처리하는 분산 시스템에서 작업을 최적화하고, 여러 작업 간 자원 분배를 조정하는데 필수적인 역할을 한다.

###  **클러스터 리소스 매니저의 역할**
   - **클러스터 관리**
     - 애플리케이션을 실행하기 위해 적절한 수의 노드를 자동으로 시작, 중지, 확장하여 클러스터의 자원을 효율적으로 관리한다.
   - **클러스터 최적화**
     - 클러스터 전체의 자원 사용률을 최적화하고, 각 노드가 과부하에 걸리지 않도록 조정.
   - **워커 관리**
     - 애플리케이션 작업을 수행하는데 도움이 되는 다양한 유형의 워커를 관리하는데, 여기에 실행자 및 드라이버가 포함된다.
   - **자원 할당**
     - 각 작업이 필요한 자원을 얼마나 할당받을지 결정.
     - CPU, 메모리와 같은 자원 사용을 조율.
   - **작업 스케줄링**
     - 클러스터 내에서 각 노드에 작업을 분배하고, 이를 효율적으로 스케줄링.
   - **오작동 감지 및 복구**
     - 작업 중 장애가 발생할 경우, 이를 감지하고 자동으로 작업을 재시작하거나 다른 노드에 작업을 재배치.

## **스파크의 리소스 매니저 종류**
   - **Standalone Mode**
     - 스파크에서 기본 제공되는 리소스 매니저로, 독립적인 클러스터 환경에서 자원을 관리한다.
     - 소규모 또는 테스트 환경에서 사용하기 적합.
   - **YARN (Yet Another Resource Negotiator)**
     - 하둡 에코시스템에서 가장 널리 사용되는 리소스 매니저.
     - YARN은 스파크 작업을 하둡 클러스터 내에서 관리하고, 자원을 동적으로 할당.
     - 다양한 클러스터 애플리케이션을 관리하는 데 유리하다.
   - **Mesos**
     - 더 복잡한 클러스터 관리 기능을 제공하는 리소스 매니저로, 다양한 종류의 애플리케이션을 실행할 수 있는 분산 시스템.
     - 스케줄링 정책과 다양한 기능을 지원하여 고도화된 배포 환경에 적합.
   - **Kubernetes**
     - 최근에 많은 인기를 얻은 컨테이너 오케스트레이션 플랫폼.
     - 스파크 애플리케이션을 쿠버네티스 클러스터에서 관리하며, 자원을 할당하고 컨테이너 기반으로 배포 가능.

## 그렇다면 어떻게 리소스 매니저를 선택해야할까?

지난 프로젝트에서 스파크 애플리케이션을 수행하면서 여러가지 시행착오를 겪었다. 우리는 하둡 hdfs를 사용했기 때문에 모든 서버에서 동작하는 hadoop이 있었고, 따라서 yarn을 리소스 매니저로 활용하는 것이 가능했다. 따라서 후보에 들어왔던 리소스 매니저는 **standalone**, **yarn**이었다. yarn을 사용하지 않았던 이유는 프로젝트 구성상의 문제로 담당자가 달라 환경 통합에 어려움이 있었기 때문이었다. 프로젝트 규모가 작기도 했고, 가장 오류가 나지 않았던 모드가 standalone이었기 때문에 최종적으로 선택한 리소스 매니저는 standalone이었다. 

공식적으로 글을 남겨 리소스 매니저를 선택하는 가이드를 남겨두려고 한다. 

### **리소스 매니저 선택 가이드**

|특징|리소스 매니저|
|-------|-----|
|소규모 클러스터|Standalone 모드|
|하둡 클러스터 활용| YARN|
|다양한 애플리케이션 통합| Mesos|
|컨테이너 기반의 대규모 확장| Kubernetes|


### 1. **Standalone Mode**
   **장점:**
   - 설치 및 설정이 매우 간단하고 독립적인 환경에서 동작.
   - 스파크와 완전히 통합되어 별도의 외부 시스템이 필요하지 않음.
   - 소규모 클러스터나 테스트 환경에서 사용하기 적합.

   **단점:**
   - **확장성 부족**: 대규모 클러스터에서는 자원 관리에 한계가 있음. 다른 매니저와 비교했을때, 여러 애플리케이션의 자원 할당을 비효율적으로 관리하는 면이 있음
   - 복잡한 워크로드나 다양한 애플리케이션을 다루기에 부적합.
   - 대규모 클러스터에서 많은 노드와 애플리케이션을 관리하기에는 부적합.
   - 다른 시스템과의 통합 기능은 부족함

   **사용 시나리오:**
   - **소규모 또는 테스트 환경**: 수십 개 정도의 노드를 사용하는 소규모 클러스터에서 성능을 테스트하거나, 가벼운 애플리케이션을 실행할 때 적합.
   - **개발 단계**: 리소스 관리의 복잡성을 최소화하여 빠르게 결과를 확인하고 싶을 때.

### 2. **YARN (Yet Another Resource Negotiator)**
   **장점:**
   - **하둡 에코시스템과 자연스러운 통합**: YARN은 하둡 기반의 클러스터에서 자연스럽게 통합되고 관리할 수 있음.
   - **자원 관리**: 스파크뿐만 아니라 하둡의 맵리듀스와 같은 다른 애플리케이션과 자원을 공유할 수 있음.
   - **안정성**: 대규모 데이터 클러스터에서 안정적인 자원 할당과 작업 스케줄링을 제공.
   
   **단점:**
   - **설정 복잡성**: Standalone에 비해 설정이 복잡할 수 있음.
   - **오버헤드**: yarn 자체가 복잡하고 다양한 기능을 제공하기 때문에 운영 오버헤드가 발생할 수 있음. 
   - **하둡 종속성**: YARN은 하둡 클러스터에 의존하기 때문에 하둡 에코시스템을 사용하지 않는다면 불필요한 오버헤드가 발생할 수 있음.

   **사용 시나리오:**
   - **대규모 데이터 처리**: 이미 하둡 클러스터를 사용 중인 환경에서는 스파크 작업을 YARN 위에서 실행하는 것이 매우 효율적.
   - **다중 워크로드 관리**: 클러스터에서 다양한 워크로드(예: 맵리듀스, 스파크 등)를 동시에 실행하는 경우 YARN을 사용하여 자원을 유연하게 분배할 수 있음.

### 3. **Mesos**
   **장점:**
   - **다양한 워크로드 지원**: 스파크 외에도 다양한 애플리케이션을 동시에 실행할 수 있음.
   - **고도화된 자원 스케줄링**: 자원 사용을 최적화하고, 다양한 스케줄링 정책을 사용할 수 있음.
   - **유연성**: 동적 자원 할당을 통해 클러스터의 자원을 효율적으로 사용할 수 있음.

   **단점:**
   - **복잡한 설정**: Mesos는 설정과 관리가 상당히 복잡할 수 있음.
   - **지원 부족**: YARN이나 Kubernetes에 비해 Mesos는 상대적으로 덜 사용되며, 커뮤니티 지원도 적음.

   **사용 시나리오:**
   - **복잡한 다중 애플리케이션 환경**: 다양한 종류의 애플리케이션을 한 클러스터에서 관리하려는 경우.
   - **스케줄링 유연성 필요**: 자원 스케줄링을 세밀하게 제어하고, 여러 정책을 적용하여 자원 활용을 극대화해야 하는 경우.

### 4. **Kubernetes**
   **장점:**
   - **컨테이너 기반**: 스파크 작업을 컨테이너로 실행하여 애플리케이션 격리 및 이식성을 제공.
   - **확장성**: 컨테이너 오케스트레이션 기능 덕분에 쉽게 클러스터를 확장할 수 있음.
   - **유연성**: 다양한 환경에서 컨테이너 기반으로 배포할 수 있기 때문에 클라우드나 온프레미스 환경에서 유연하게 사용 가능.

   **단점:**
   - **복잡한 설정**: 컨테이너 관리 및 오케스트레이션에 대한 경험이 필요함.
   - **초기 학습 곡선**: Kubernetes는 복잡한 시스템이므로 학습에 시간이 필요하고, 설정 및 관리가 다소 까다로울 수 있음.

   **사용 시나리오:**
   - **클라우드 기반 대규모 클러스터**: 스파크 작업을 대규모 클러스터에서 효율적으로 확장하고, 컨테이너 환경에서 애플리케이션을 쉽게 배포하고 관리하려는 경우.
   - **마이크로서비스 아키텍처**: Kubernetes는 컨테이너 기반의 마이크로서비스 아키텍처와 잘 맞으며, 여러 작업을 쉽게 관리할 수 있음.
   - **이식성 및 유연성 필요**: 다양한 클라우드 및 온프레미스 환경에서 스파크 작업을 이식성 있게 관리하고자 할 때.

---

### 5. **선택 시 고려할 사항**
   - **프로젝트의 규모**:
     - 소규모 개발이나 테스트 환경에서는 Standalone 모드를 고려할 수 있음.
     - 대규모 데이터 처리와 여러 작업을 동시에 실행해야 한다면 YARN 또는 Mesos가 적합.
     - 클라우드 기반의 확장성과 유연성을 원한다면 Kubernetes가 가장 적합.

   - **클러스터의 사용 목적**:
     - 스파크뿐만 아니라 다른 애플리케이션도 같은 클러스터에서 실행하는 경우 YARN이나 Mesos가 더 나은 선택일 수 있음.
     - 스파크만 사용한다면 Standalone 모드가 간단하고 효율적일 수 있음.

   - **컨테이너 사용 여부**:
     - 컨테이너화된 애플리케이션 관리가 필요하다면 Kubernetes가 적합하며, 이를 통해 클러스터 환경을 쉽게 배포하고 확장할 수 있음.

   - **환경 구성의 복잡성**:
     - Standalone 모드는 간단하지만 확장성은 제한적임.
     - YARN과 Kubernetes는 더 많은 기능을 제공하지만, 복잡한 설정과 관리를 요구함.