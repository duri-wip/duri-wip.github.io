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

Dockerfile에서 기본적으로 **`COPY`** 또는 **`ADD`** 명령어를 사용할 때, Docker가 찾을 수 있는 파일은 **Docker build context** 안에 있어야 합니다. Docker build context는 `docker build` 명령어를 실행할 때 지정한 경로 내의 파일 및 디렉토리를 의미합니다.

### 1. **Docker Build Context**

`docker build` 명령어를 실행할 때 기본적으로 현재 디렉토리 (`.`)를 build context로 지정합니다. Docker는 이 context 내에서만 파일을 참조할 수 있으며, 하위 경로 또는 동일한 경로에 있는 파일을 찾습니다.

```bash
docker build -t your_image_name .
```

위 명령어에서 `.`은 현재 디렉토리를 build context로 지정하는 것이며, Dockerfile 내에서 이 경로 하위에 있는 파일만 `COPY` 또는 `ADD` 명령어로 복사할 수 있습니다.

### 2. **하위 경로의 파일만 참조 가능**

만약 Dockerfile에서 하위 경로가 아닌 **상위 경로** 또는 **다른 디렉토리**의 파일을 참조하려고 하면 Docker는 오류를 발생시킵니다. Docker는 build context 외부에 있는 파일에 접근할 수 없습니다.

예를 들어, 다음과 같은 구조가 있을 때:

```
/project
  ├── Dockerfile
  ├── src/
  │   └── main.py
  └── data/
      └── dataset.csv
```

이 경우, `COPY data/dataset.csv /app/data/`와 같이 build context 안에 있는 `data` 디렉토리의 파일은 문제없이 복사할 수 있습니다. 하지만 상위 디렉토리나 build context 외부에 있는 파일을 참조하려고 하면 Docker는 이를 허용하지 않습니다.

### 3. **해결 방법: 상위 디렉토리의 파일 복사**

상위 디렉토리나 다른 경로의 파일을 Docker 이미지에 포함하려면, 다음 두 가지 방법을 고려할 수 있습니다.

#### 1. **build context를 상위 디렉토리로 설정**

만약 Dockerfile과 상위 디렉토리의 파일을 모두 사용하고 싶다면, `docker build` 명령어를 실행할 때 build context를 상위 디렉토리로 설정할 수 있습니다. 이렇게 하면 Docker는 상위 경로에 있는 파일도 찾을 수 있습니다.

```bash
docker build -t your_image_name -f subdir/Dockerfile ..
```

여기서 `..`은 상위 디렉토리를 build context로 지정한 것입니다. 이렇게 하면 Docker는 상위 디렉토리와 그 하위 파일을 모두 참조할 수 있습니다.

#### 2. **필요한 파일을 사전에 복사**

또 다른 방법은 Docker 빌드를 실행하기 전에 필요한 파일을 Dockerfile이 있는 경로로 복사한 후, Dockerfile에서 복사된 파일을 참조하는 것입니다.

```bash
cp ../somefile ./somefile
docker build -t your_image_name .
```

### 요약
- Docker는 **build context** 내에서만 파일을 찾을 수 있습니다. 기본적으로 하위 경로의 파일만 접근 가능합니다.
- 상위 경로나 외부 파일을 사용하려면:
  1. **build context**를 상위 디렉토리로 설정하거나,
  2. 빌드 전에 필요한 파일을 Dockerfile이 있는 경로로 복사하는 방법을 사용할 수 있습니다.

이 방식을 통해 Docker가 상위 경로나 다른 디렉토리의 파일을 사용할 수 있도록 구성할 수 있습니다.