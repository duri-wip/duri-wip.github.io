---
layout: post
title: "FastAPI Background Tasks"
excerpt: "FastAPI에서 백그라운드 작업 처리"
date: 2025-08-17
categories: [Development]
subcategory: Backend
tags: [backend, fastapi, background-tasks, async]
---

# background tasks

클라이언트로부터 요청을 받은 우 작업을 백그라운드에서 수행하도록 하는 도우미 클래스.
http 요청을 처리할때 작업이 오래 걸리거나 비동기적으로 처리해야하는 경우
클라이언트에게 즉시 응답을 보내고 작업을 백그라운드에서 처리하고자 하는 경우에 사용한다.

1. 엔드포인트에서 매개변수로 주입받아 사용하고 fastapi가 나머지를 처리하도록 하는 경우

```
import asyncio

from fastapi import APIRouter, BackgroundTasks

router = APIRouter()
async def perform_task(task_id: int):
    await asyncio.sleep(3)

@router.post("")
def create_task(task_id, background_tasks: BackgroundTasks):
    background_tasks.add_task(perform_task, task_id)
    return
```

1. 백그라운드로 수행되는 작업 정의
2. 라우터 함수에 BackgroundTasks 객체 주입
3. 백그라운드 작업 추가
4. 응답 반환

BackgroundTasks클래스는 starlette.background 모듈에서 제공한다. fastapi에 포함돼 있으므로 모듈에서 가져와 사용할 수 있다.
starlette.background 의 Background(s가 없음)이 아니다.

backgroundtask 객체는 dependable, callable하지 않아서 depends로 불러와서 사용할 수 없기 때문에 직접 주입해서 사용해야 한다.

# 셀러리

## 셀러리란?

유닉스 시스템 기반의 분산작업 큐로, 비동기 작업을 처리하고 관리하는데 사용된다.
주로 백그라운드 작업을 처리하거나 스케줄링하고 분산 환경에서 작업을 실행한다.
파이썬 기반의 메시징 시스템은 셀러리 외에도 레디스 큐, 드라마틱 휴이, aiohttp와 같은 것을 고려할 수 잇고, 카프카도 있다.

메시징 시스템의 특징

- 작업 큐
- 분산 환경
- 작업 처리
- 메시지 브로커

셀러리 메시징 시스템의 구성 요소

- 브로커
  작업을 관리하고 전달하는 중간 매개체
- 백엔드
  작업의 결과를 저장하고 추적하기 위한 저장소
- 워커
  브로커로부터 할당된 작업을 받아 실행하고 실행한 결과를 백엔드에 기록한다
- 태스크
  수행되는 개별 작업. 데코레이터로 구현할 수 있다.

브로커와 백엔드 역할을 하는 시스템으로서 레디스나 rabbitmq 등을 사용할 수 있다.

## 1. 환경 설정

fastapi에서 셀러리를 이용하기 위해 브로커는 래빗엠큐를 이용하고 저장소는 mysql을 이용한다

> pip install celery
> docker run -d --hostname myrabbit --name myrabbit -e RABBITMQ_DEFAULT_USER=root -e RABBITMQ_DEFAULT_PASS=test -p 5672:5672 -p 15672:15672 rabbitmq:3-management

- rabbitmq:3-management는 3.x 버전 중 관리 플러그인이 포함된 버전을 말한다. 웹 기반의 사용자 인터페이스를 제공한다.

## 태스크 수행

간단한 덧셈을 수행하는 함수를 셀러리에서 백그라운드로 실행하고 셀러리는 그 태스크 수행 결과를 데이터베이스에 저장한다.

### 태스크 정의

> example01.py

```
from common.messaging import celery

@celery.task
def add(x,y):
    return x+y
```

### 셀러리 객체를 생성

> common/messaging.py

```
from celery import Celery

celery = Celery(
    "fastapi-ca",
    broker=settings.celery_broker_url,
    backend=settings.celery_backend_url,
    broker_connection_retry_on_startup=True,
    include=["example01"]
)
```

### 셀러리 워커의 구동

워커를 여러개 생성하고 싶은 경우 새로운 창에서 각각 구동하면 된다

> celery -A common.messaging.celery worker -n worker1 --loglevel=info
> celery -A common.messaging.celery worker -n worker2 --loglevel=info

### 셀러리 워커에 태스크 전달

> > from example import add
> > task1 = add.delay(1,2)

흠...
