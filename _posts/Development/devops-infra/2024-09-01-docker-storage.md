---
layout: post
title: "도커 스토리지의 구조와 관리법"
excerpt: "Docker Storage"
date: 2024-09-01
categories: [Development]
tags: [devops-infra, docker, storage, volumes]
---
---

## 컨테이너 데이터를 보존하는 방법

컨테이너 데이터는 컨테이너 레이어에 생성되므로 컨테이너 삭제시 함께 삭제된다.
컨테이너의 데이터는 레이어에 생성되므로 여러개의 컨테이너가 데이터를 공유하도록 하는 방법이 없다.

### 해결하기

1. 호스트 디렉토리에 데이터를 작성하고 컨테이너 디렉토리로 볼륨 마운트하기

```
touch /webdata/index.html
docker run --name web -d -v /webdata:/usr/share/nginx/html nginx
```

- -d : background mode
- -v [host path here...]:[container mount path here...]
- -v [host path here...]:[container mount path here...]:[읽기모드]

2. 도커 볼륨을 활용하기

```
docker run -d --name db -v dbdata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=pass mysql:8
```

- mysql 8버전의 이미지를 패스워드 = pass 옵션으로 실행한다
- 이름은 db고
- 볼륨은 /dbdata의 이름으로 생성해서 저장한다.
- 그러면 컨테이너 안에서 /var/lib/mysql에 생성되는 db 가 컨테이너가 삭제되어도 호스트에 /var/lib/docker/volumes/dbdata/\_data경로 안에 저장된다

### 도커 볼륨 관리하기

```
docker volume ls
docker volume create webdata
docker volume rm webdata
docker volume prune
```

- 볼륨 목록 확인하기
- 볼륨 생성하기
- 볼륨 삭제하기
- 사용하지 않는 모든 스토리지 공간 삭제하기
