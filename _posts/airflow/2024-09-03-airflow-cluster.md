---
layout: post
title: "Airflow 클러스터 구성하기"
excerpt: "airflow cluster"

category: airflow
tags:
  - [Decorator]

permalink: /airflow/airflow-cluster/

toc: true
toc_sticky: true

date: 2024-09-03
last_modified_at: 2024-09-29
---

# Airflow 클러스터 구성하기

## 1. 환경 요구사항

- python
- postgres DB
- redis
- celeryExecutor 를 지원하는 airflow

## 2. 환경 구성

### Pyenv

1. 필요한 패키지 설치하기

```
sudo apt update
sudo apt install -y make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncurses5-dev libncursesw5-dev xz-utils tk-dev \
libffi-dev liblzma-dev python3-openssl git
```

2. github에서 클론으로 pyenv 설치하기

```
curl https://pyenv.run | bash
```

3. shell 파일 수정

```
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```

4. 가상환경 생성
   > python 3.11.9 -> airflow-env

### PostgreSQL: metaDB

1. 설치하기

```
sudo apt update
sudo apt install -y postgresql postgresql-contrib
```

2. 설치 확인

> sudo systemctl status postgresql.service

3. 새로운 데이터베이스와 유저 설정

> database 명 : airflow_db

> user 명 : user name

> 권한 : grant all

### Redis

1. 설치하기

```
sudo apt install redis-server
```

### Airflow

1. pip 로 airflow와 celeryexecuter 설치하기

```
pip install apache-airflow[celery,postgres,redis]==2.*
```

2. 초기화

```
airflow db init
```

초기화를 해야 airflow.cfg파일이 구성된다.

3. 사용자 생성

```
airflow users create --username admin --firstname FIRST --lastname LAST --role Admin --email admin@example.com
```

4. meta DB와 연결 설정

[core]
executor = CeleryExecutor

[database]
sql_alchemy_conn = postgresql+psycopg2://'user':'password'@localhost/airflow_db

[celery]
broker_url = redis://localhost:6379/0
result_backend = db+postgresql://'user':'password'@localhost/airflow_db

5. 각 노드에서 워커, 스케줄러, 웹서버 시작(웹서버는 마스터에서만 띄운다)
   > airflow webserver --port 8080

> airflow scheduler

> airflow celery worker
