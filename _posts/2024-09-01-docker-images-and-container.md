---
title: "Images and Container"
excerpt: "이미지와 컨테이너의 개념과 사용법"

categories:
  - docker
tags:
  - [Docker]

permalink: /docker/docker-container-and-images/

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-10-01
---

- 0.  YAML? : yaml 파일 사용법
- 1.
- 2.  Storage : 도커 스토리지의 구조와 관리법
- 3.  Network : 도커 네트워크의 구조

## Docker container life cycle

image + rw container layer --> process 동작 ----------------------> stop -------> container layer 삭제

컨테이너 생성(create) -> 시작(start) -> 동작확인(curl IP_Address) -> 중지(stop) -> 삭제(rm)

## Docker 이미지의 구조

### Docker 이미지 pull

hub.docker.com에서 이미지 검색하기

```
docker search nginx
```

검색된 이미지 다운로드받기

```
docker pull nginx
docker pull nginx:1.22
```

### 클라우드 레포지토리에서 이미지 pull

docker hub 말고도 클라우드 레포지토리에서 이미지를 검색하고 다운로드 받을 수 있다.

- AWS 갤러리[https://gallery.ecr.aws]에서 이미지를 다운로드 받는법 : 검색해서 이미지 이름 복사한다음 붙여넣기

```
docker pull public.ecr.aws/nginx/nginx:1.27.1-alpine3.20-perl
```

### 이미지 목록 확인하기

```
docker image ls
docker images
```

또는 `/var/lib/docker/overlay2` 에서 다운로드받은 이미지를 확인할 수 있다.

### 이미지 히스토리, 세부정보 확인하기

```
docker history nginx
docker inspect nginx
```

- 이미지가 실행되면 어떤 순서대로 어떤 커맨드가 실행되는지
- 이미지가 어떤 레이어로 구성되어 있는지

### 이미지 삭제

```
docker rmi nginx
docker rmi public.ecr.aws/nginx/nginx:1.27.1-alpine3.20-perl
```

## Docker 컨테이너 생성 및 실행

다운받은 이미지로 컨테이너 생성하기

```
docker create --name web nginx
```

- --name : 컨테이너에 이름 붙이기
  생성한 컨테이너 시작하기

```
docker start web
```

실행중인 컨테이너 세부 정보 출력

```
docker inspect web
```

컨테이너 종료

```
docker stop web
```

컨테이너 삭제

```
docker rm web
```

pull, create, start를 한번에 동작하기

```
docker run -d --name web nginx
```

### 컨테이너 정보 확인하기

```
docker ps
docker ps -a
```

### 컨테이너 내부에서 명령어를 실행하기

```
docker exec -it nginx /bin/bash
```

- -i : nginx컨테이너의 내부에서
- -t : 터미널로
- /bin/bash : 에 접근하겠습니다.

### 컨테이너 외부에서 내부로 파일 붙여넣기

```
docker cp index.html web:/usr/share/nginx/html
```

## logs 확인

```
docker logs web
docker top web
docker diff web
```

- web 컨테이너의 실행 로그 확인
- web 컨테이너의 프로세스 목록보기
- web 컨테이너 내부파일 변경 이력 확인

## 컨테이너 저장과 배포

컨테이너를 아카이브로 저장해서 배포하도록 함
Docker save : 이미지를 아카이브 파일로 저장
Docker load : 아카이브 파일을 이미지로 로드
Docker export : 이미지 내보내기
Docker import : 이미지 가져오기
