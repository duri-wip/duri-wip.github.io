---
layout: post
title: "Images and Container"
excerpt: "이미지와 컨테이너의 개념과 사용법"
date: 2024-09-01
categories: [Development]
subcategory: devops-infra
tags: [devops-infra, docker, images, container]
---

---

## 도커 컨테이너 라이프사이클

![](/assets/images/post_img/container-lifecycle.png)

1. **컨테이너 생성 (create)**: 이미지를 기반으로 컨테이너 생성
2. **컨테이너 시작 (start)**: 생성된 컨테이너를 실행
3. **컨테이너 동작 확인 (curl IP_Address)**: 컨테이너의 동작 상태 확인
4. **컨테이너 중지 (stop)**: 실행 중인 컨테이너를 중지
5. **컨테이너 삭제 (rm)**: 중지된 컨테이너를 삭제

# Docker 이미지 가져오기

1. **이미지 검색**

```
docker search nginx
```

2. **이미지 다운로드**

```
docker pull nginx
docker pull nginx:1.22
```

### 클라우드 레포지토리에서 이미지 다운로드

도커 허브 외에도 AWS ECR 같은 클라우드 레포지토리에서 이미지를 다운로드할 수 있다.

```
docker pull public.ecr.aws/nginx/nginx:1.27.1-alpine3.20-perl
```

### 이미지 목록 확인

```
docker image ls
docker images
```

또는 `/var/lib/docker/overlay2` 경로에서 이미지를 확인할 수 있다.

### 이미지 히스토리 및 세부 정보 확인

이미지의 실행 히스토리와 구성 정보를 확인할 수 있다.

```
docker history nginx
docker inspect nginx
```

- 이미지가 어떻게 구성되어 있는지, 어떤 레이어가 있는지 확인할 수 있다.

### 이미지 삭제

```
docker rmi nginx
docker rmi public.ecr.aws/nginx/nginx:1.27.1-alpine3.20-perl
```

# 도커 컨테이너 생성 및 실행

## 컨테이너 생성

다운로드한 이미지를 기반으로 컨테이너를 생성하기

```
docker create --name web nginx
```

- `--name`: 컨테이너에 이름을 붙입니다.

## 컨테이너 시작 및 동작 확인

생성한 컨테이너 시작하기

```
docker start web
docker inspect web
```

## 컨테이너 중지 및 삭제

실행 중인 컨테이너를 중지하고 삭제하기

```
docker stop web
docker rm web
```

## 컨테이너 실행과 동시에 생성

이미지를 pull하고 컨테이너를 생성, 실행하는 작업을 한 번에 하기

```
docker run -d --name web nginx
```

# 컨테이너 정보 확인

실행 중인 컨테이너 목록을 확인하기

```
docker ps
docker ps -a
```

# 컨테이너 내부에서 명령어 실행

```
docker exec -it nginx /bin/bash
```

- `-i`: 상호작용 모드
- `-t`: 터미널 모드
- `/bin/bash`: 컨테이너 내부로 진입

## 외부에서 컨테이너로 파일 복사

로컬 파일을 컨테이너 내부로 복사하

```
docker cp index.html web:/usr/share/nginx/html
```

## 로그 확인 및 상태 정보

컨테이너의 로그를 확인하거나 상태 정보를 조회하기

```
docker logs web
docker top web
docker diff web
```

- `docker logs`: 실행 로그 확인
- `docker top`: 프로세스 목록 확인
- `docker diff`: 컨테이너 내부의 파일 변경 이력 확인

## 컨테이너 저장과 배포

- **Docker save**: 이미지를 아카이브 파일로 저장
- **Docker load**: 아카이브 파일을 이미지로 로드
- **Docker export**: 컨테이너를 이미지로 내보내기
- **Docker import**: 외부에서 가져온 이미지를 컨테이너로 가져오기
