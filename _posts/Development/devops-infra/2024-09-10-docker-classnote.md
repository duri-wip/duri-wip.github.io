---
layout: post
title: "Docker 통합 학습 노트"
excerpt: "Docker 기초부터 고급까지 통합 정리"
date: 2024-09-10
categories: [Development]
tags: [devops-infra, docker, container, infrastructure]
---

다음은 기존 Docker 요약에 추가적으로 보완할 내용을 포함한 통합 요약본입니다. **추가된 내용은 "보완된 내용"**으로 표시했습니다.

---

### **Docker 통합 요약**

#### **1. 하드웨어 구조 이해**
   - **IAC**(Infrastructure as Code): 하드웨어에 대한 이해를 기반으로 시스템을 최적화
   - 목표: 소프트웨어가 하드웨어와 시스템에 맞춰 안정적으로 작동하도록 구현
   - 소프트웨어는 기본 시스템에 맞게 안정적이고 효과적으로 작동해야 함

#### **2. Docker 정의**
   - **Docker**: 컨테이너를 사용하여 애플리케이션을 배포하고 관리하는 소프트웨어 플랫폼
   - **컨테이너**: 독립적이고 가벼운 가상화 기술로, 애플리케이션을 프로세스 단위로 실행
   - Docker는 애플리케이션을 패키징하여 표준화된 환경에서 실행할 수 있도록 지원

#### **3. Docker를 사용하는 이유**
   - **표준화와 규격화**를 통한 애플리케이션 배포와 실행의 용이성
   - **컨테이너 기술**을 통해 동일한 환경에서 애플리케이션을 개발, 테스트, 배포 가능
   - **Google**: 20억 개 이상의 컨테이너를 운영 중

#### **4. Docker 설치**
   - **설치 명령어**:
     - `sudo apt update`: 패키지 업데이트
     - `sudo apt-get install docker-ce docker-ce-cli containerd.io`: Docker 설치
     - `sudo docker version`: Docker 설치 완료 및 버전 확인

#### **5. Docker 앱 개발 과정**
   - 애플리케이션 구현 → 컨테이너에 배포 → Nginx 웹 서버 설정
   - Dockerfile 작성 후 이미지 생성 (`docker build`)
   - 이미지 실행: `docker run`으로 컨테이너 생성
   - 테스트 후 Docker Hub와 같은 레지스트리에 이미지를 등록 (`docker push`)

#### **6. Docker CLI 명령어**
   - **이미지 관리**:
     - `docker pull`: 이미지 다운로드
     - `docker run`: 이미지를 기반으로 컨테이너 실행
     - `docker ps`: 실행 중인 컨테이너 목록 확인
     - `docker image rm`: 이미지 삭제
   - **컨테이너 관리**:
     - 컨테이너는 이미지를 복제하여 실행
     - 컨테이너가 종료되면 스냅샷만 남고, 프로세스는 사라짐
     - `docker ps`로 실행 상태 확인 가능
   - **이미지 관리 및 삭제**:
     - `docker image history`: 이미지의 히스토리 확인
     - `docker rmi`: 이미지 삭제 명령어

#### **7. 멀티컨테이너 서비스**
   - 예: WordPress + MySQL 2계층 애플리케이션
   - 여러 컨테이너를 관리하기 위해 Docker Compose 사용
   - YAML 파일을 사용하여 여러 서비스를 정의 (`docker-compose.yml`)
   - `docker compose up` 명령어로 서비스를 실행하고, 문제가 발생하면 `docker compose down`으로 종료
   - Docker Compose의 주요 장점: 코드의 재사용성 및 서비스 관리의 용이성
   - **Docker Compose YAML 예시**:
   ```yaml
   version: '3.9'
   services:
     mydb:
       image: mysql:5.7
       container_name: mysql_app
       volumes:
         - mydb_data:/var/lib/mysql
       restart: always
       ports:
         - "8031:3306"
       networks:
         - backend-net
       environment:
         MYSQL_ROOT_PASSWORD: wordpress
         MYSQL_DATABASE: wordpress
         MYSQL_USER: wordpress
         MYSQL_PASSWORD: wordpress

     myweb:
       depends_on:
         - mydb
       image: wordpress:latest
       container_name: wordpress_app
       ports:
         - "8032:80"
       networks:
         - frontend-net
         - backend-net
       volumes:
         - myweb_data:/var/www/html
         - ${PWD}/myweb-log:/var/log
       restart: always
       environment:
         WORDPRESS_DB_HOST: mydb:3306
         WORDPRESS_DB_USER: wordpress
         WORDPRESS_DB_PASSWORD: wordpress
         WORDPRESS_DB_NAME: wordpress

   networks:
     frontend-net: {}
     backend-net: {}

   volumes:
     mydb_data: {}
     myweb_data: {}
   ```

#### **8. Docker 이미지 및 Dockerfile**
   - **Dockerfile**: 컨테이너 환경을 정의하는 스크립트 파일
   - 주요 명령어:
     - `FROM`: 베이스 이미지 설정
     - `RUN`: 실행할 명령어
     - `COPY`, `ADD`: 파일 복사
     - `EXPOSE`: 포트 노출 설정
     - `CMD`: 컨테이너 실행 시 기본으로 실행할 명령어
   - Docker 이미지를 빌드하여 Dockerfile을 기반으로 환경을 구성하고 이미지를 생성 (`docker build`)

#### **9. Docker Compose 및 YAML**
   - **Docker Compose**: 여러 컨테이너를 YAML 파일로 정의하여 서비스 관리
   - `docker-compose.yml` 파일을 통해 여러 컨테이너 서비스를 하나의 애플리케이션으로 실행 가능
   - `docker-compose` 명령어를 사용하여 빠르게 멀티컨테이너 애플리케이션을 실행

#### **10. Docker와 Kubernetes**
   - **Kubernetes**: 컨테이너 오케스트레이션 도구로, Docker와 함께 사용
   - Kubernetes는 자체적인 컨테이너 런타임을 제공하지 않으며, **containerd**, **CRI-O** 등 외부 런타임을 사용하여 컨테이너를 관리
   - **Docker Swarm**: Docker 자체에서 제공하는 기본 오케스트레이션 도구
   - **(보완된 내용)**: Kubernetes는 자동화된 배포, 확장, 그리고 컨테이너의 관리를 돕는 강력한 도구로, 수많은 컨테이너를 동시에 관리하고 클러스터로 확장할 수 있는 기능을 제공. 그러나 **Kubernetes와 Docker의 차이점**은, Kubernetes는 여러 노드에 걸친 클러스터 환경에서 복잡한 애플리케이션의 관리에 적합하고, Docker Swarm은 더 작은 스케일의 프로젝트에 적합한 경량 솔루션임.

#### **11. 컨테이너 네트워크 및 볼륨 관리**
   - **컨테이너 네트워크 설정**:
     - `docker run`에서 `-p` 옵션을 통해 호스트와 컨테이너 포트를 연결
     - `docker inspect` 명령어를 사용하여 컨테이너 내부 포트 확인
   - **볼륨 관리**:
     - 호스트와 컨테이너 간 데이터 공유를 위해 Docker Volume 사용 (`docker volume`)
     - 볼륨을 사용하면 컨테이너 간 데이터 공유 및 데이터 지속성 보장 가능
   - **(보완된 내용)**: **Docker 네트워크 유형**은 크게 세 가지로 나뉨:
     - **브리지 네트워크**: 기본 네트워크로, 컨테이너 간 통신이 가능한 독립적 네트워크 제공
     - **호스트 네트워크**: 컨테이너가 호스트 시스템의 네트워크와 동일한 네트워크 스택을 사용함
     - **오버레이 네트워크**: 여러 호스트에 걸쳐 있는 컨테이너 간 통신을 위해 사용

#### **12. 컨테이너 모니터링**
   - **cAdvisor**: Google에서 제공하는 실시간 컨테이너 모니터링 도구
   - 컨테이너 CPU, 메모리, 네트워크 트래픽을 실시간으로 모니터링 가능
   - **(보완된 내용)**: **Prometheus**와 **Grafana**는 컨테이너 모니터링을 위한 추가적인 도구로, Prometheus는 메트릭 수집 및 저장에 특화되어 있으며, Grafana는 수집된 데이터를 시각화하여 효율적인 모니터링 환경을 제공

#### **13. Docker와 Git 연동**
   - GitHub에서 Docker 프로젝트를 클론하여 Dockerfile을 빌드
   - 컨테이너를 실행하고 테스트 후 변경 사항을 GitHub에 푸시

#### **14. 실습: Python 및 Node.js 환경에서 코드 테스트**
   - Python 컨테이너를 실행하여 로또 프로그램 테스트
   - Node.js 컨테이너에서 코드 테스트
   - Docker 컨테이너 네트워크를 사용하여 여러 애플리케이션 간 상호작용 테스트

---

**보완된 내용**으로 Kubernetes와 Docker Swarm의 차이점, 네트워크 유형, 추가적인 컨테이너 모니터링 도구를 추가하였습니다. 이 통합된 요약본은 Docker에 대한 전반적인 내용을 다루고 있으며, 추가 질문이나 수정이 필요하면 언제든지 알려주세요!