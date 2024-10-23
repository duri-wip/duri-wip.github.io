---
title: "Spark 클러스터 구축하기"
excerpt: "GCP를 활용하여"

categories:
  - spark
tags:
  - [Spark]

permalink: /spark/spark-cluster/

toc: true
toc_sticky: true

date: 2024-10-22
last_modified_at: 2024-10-22
---
# GCP SSH 연결하기

1. 로컬에서 SSH 키 생성  
`ssh-keygen -t rsa -f ~/.ssh/gcp -C username`  
gcp에서 유저이름을 못찾아 고생했던 적이 여러번 있어서 아예 옵션을 붙여 `username`을 명시적으로 설정해준다.

2. GCP VM 인스턴스 > 메타데이터에 SSH 공개 키 등록

3. SSH 연결  
`ssh -i ~/.ssh/gcp username@<external_ip_here>`
ssh키 생성시 만든 username을 사용한다.

# 클러스터 만들기

1. 방화벽 규칙 설정  
GCP Console에서 VPC 네트워크 > 방화벽 규칙으로 이동한 후, 필요한 포트를 열기 위한 규칙을 생성.  

- 트래픽: 인그레스
- 대상: 클러스터의 모든 노드에 동일한 태그 할당 후 설정
- 소스 IP 범위: 클러스터 내부 통신의 경우 10.0.0.0/8
- 프로토콜 및 포트: TCP 22, 7077, 8080, 8081, 4040, 6066 등

2. 내부 통신 설정  
- `/etc/hosts` 파일 설정  
- `~/.ssh/authorized_keys` 파일 설정  

**주의**  
SSH 키 이름이 `id_rsa`가 아니면 SSH 접속 시 문제가 발생할 수 있음.  
에러:  
```
ssh spark01
The authenticity of host 'spark01 (10.178.0.8)' can't be established.
,,,@spark01: Permission denied (publickey).
```
키 이름이 다른 경우, `~/.ssh/config` 파일을 설정해야 함.

# spark 클러스터 구축하기

## **1. 환경 준비**

- **Docker 설치**: [Docker 공식 문서](https://docs.docker.com/engine/install/ubuntu/)
- **환경설정**: [docker201](https://github.com/duri-wip/Docker201/blob/main/00.Installation.md)

## **2. Docker Compose 파일 작성**

스파크 클러스터에서 pyspark를 사용할 것인데, jupyter notebook을 함께 설치해 실험환경을 구축한다.

  ```yaml
version: '3'
services:
  spark-master:
    image: bitnami/spark:latest
    container_name: spark-master
    environment:
      - SPARK_MODE=master
    ports:
      - "8080:8080"  # Spark Master 웹 UI
      - "7077:7077"  # Spark Master 포트

  spark-worker-1:
    image: bitnami/spark:latest
    container_name: spark-worker-1
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark-master:7077
    depends_on:
      - spark-master

  spark-worker-2:
    image: bitnami/spark:latest
    container_name: spark-worker-2
    environment:
      - SPARK_MODE=worker
      - SPARK_MASTER_URL=spark://spark-master:7077
    depends_on:
      - spark-master

  jupyter:
    image: jupyter/pyspark-notebook:latest
    container_name: jupyter
    ports:
      - "8888:8888"  # Jupyter Notebook 포트
    volumes:
      - ./notebooks:/home/jovyan/work
    environment:
      - SPARK_MASTER=spark://spark-master:7077
    depends_on:
      - spark-master
  ```

...작성중...

git도 함께 설치하기
