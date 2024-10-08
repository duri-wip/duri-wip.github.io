---
title: "도커 스토리지의 구조와 관리법"
excerpt: "Docker Storage"

categories:
  - docker
tags:
  - [Docker]

permalink: /docker/docker-storage/

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-10-01
---
cicd 과정에서 어려웠던 점

1. 코드 변경 -> 모델 빌드 과정에서 워크 플로우 운영상의 문제
   자원할당 문제
   처음에는 자원을 할당하지 않고 우녕
   out of memory 에러로 학습 실패
   docker Stats 확인하여 제한

2. 네트워크 연결 및 서버 의존성의 문제
   mlflow 서버와 학습 컨테이너 간의 연결이 제대로 되지 않는 문제.
   네트워크 브리지와 depends on 설정으로 해결
   브리지를 설정해서 같은 네트워크를 공유하도록 함
   디팬드온 설정으로 컨테이너 간 연결이 가능하도록 해서 mlflow 서버가 아티팩트를 저장하는 버킷에 학습 아티팩트가 저장되고 db에도 저장되도록 설정함

컨테이너 내부에서 curl 명령어로 확인
docker inspect 로 네트워크 여결 확인

3. github actions 의 s3 접근 권한 문제
   서버는 컨테이너로 올리고학습 자체는 python 가상환경으로 수행하려고할 때 s3 버킷에 접근할 수 없다는 문제가 잇엇음
   s3 버킷에 접근하려면 github actions에서도 aws credential을 가지고 잇어야하는데 환경상 iam 권한만을 가지고 잇엇고 버킷 접근을 위한 키를 받기는 어려웟음
   컨테이너 환경에서 학습하는 과정으로 운영
