---
title: "도커파일의 context"
excerpt: "도커 빌드시 컨텍스트의 개념"

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
# Dockerfile의 Context란?

도커 빌드를 할 때, `COPY`나 `ADD` 명령어를 통해 파일을 복사할 때, Docker는 **빌드 컨텍스트(build context)** 안에 있는 파일만 접근할 수 있다. 이 컨텍스트는 `docker build` 명령어를 실행할 때 지정한 경로로, 해당 경로 내의 파일 및 디렉토리를 의미한다.

## 1. **Docker Build Context**

```bash
docker build -t your_image_name .
```

`docker build` 명령어는 기본적으로 현재 디렉토리 (`.`)를 빌드 컨텍스트로 사용한다. 이 경로 하위에 있는 파일들만 Docker가 접근할 수 있으며, Dockerfile에서 이를 참조하여 이미지 빌드를 수행한다.
위 명령어에서 `.`은 현재 디렉토리를 빌드 컨텍스트로 지정한다는 것으로, `COPY` 또는 `ADD` 명령어를 사용할 때, 이 경로 하위에 있는 파일들만 사용할 수 있다.

### **하위 경로 파일만 참조 가능**

도커는 기본적으로 빌드 컨텍스트 안에서만 파일을 찾을 수 있고, 외부에 있는 파일은 참조할 수 없다. 참조시 오류가 발생한다. 


### 상위 경로의 파일을 참조하려면?

#### 방법 1: 빌드 컨텍스트를 상위 디렉토리로 설정

Dockerfile과 상위 디렉토리의 파일을 모두 사용하고 싶다면, `docker build` 명령어를 실행할 때 빌드 컨텍스트를 상위 디렉토리로 설정할 수 있다.

```bash
docker build -t your_image_name -f subdir/Dockerfile ..
```

#### 방법 2: 빌드 전에 파일을 미리 복사

도커 빌드를 실행하기 전 필요한 파일을 하위 경로에 복사해두는 방법도 있다.
