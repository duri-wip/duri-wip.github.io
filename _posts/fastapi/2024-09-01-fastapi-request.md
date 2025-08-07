---
title: "FastAPI 기초: 요청과 응답 처리"
excerpt: "FastAPI에서 HTTP 요청과 응답을 처리하는 방법에 대해 알아본다"

category:  fastapi
tags:
  - [fastapi, request, response, web]

permalink: /fastapi/request/

toc: true
toc_sticky: true

date: 2025-05-06
last_modified_at: 2025-05-06
---

# FastAPI 소개

FastAPI는 현대적인 Python 웹 프레임워크이다. 다음과 같은 특징을 가지고 있다:

- RESTful API 구축에 최적화되어 있다
- Pydantic을 통한 타입 힌트 기반 자동 문서화를 지원한다
- 비동기 프로그래밍을 완벽하게 지원한다
- Gunicorn과 ASGI를 통한 프로덕션 수준의 구동이 가능하다

## 프로덕션 환경에서의 구동

FastAPI는 개발 환경에서는 `uvicorn` 명령어로 간단히 실행할 수 있지만, 실제 서비스 환경에서는 Gunicorn과 함께 사용하여 안정적인 운영이 가능하다.

```bash
# 개발 환경 실행
uvicorn main:app --reload

# 프로덕션 환경 실행
gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker
```

위 예시에서:

- `-w 4`: 4개의 워커 프로세스를 생성한다
- `-k uvicorn.workers.UvicornWorker`: Uvicorn 워커를 사용하여 ASGI 지원을 활성화한다

이렇게 구성하면 다음과 같은 이점이 있다:

1. **로드 밸런싱**: 여러 워커 프로세스가 요청을 분산 처리한다
2. **고가용성**: 한 워커가 실패해도 다른 워커가 서비스를 계속한다
3. **성능 최적화**: CPU 코어를 최대한 활용하여 처리량을 높인다

## 주요 장점

1. **표준 기반 설계**

   - OpenAPI와 JSON Schema 기반으로 통합이 용이하다
   - 자동 문서화 (Swagger UI)를 지원한다
   - VS Code, PyCharm 등 주요 IDE를 지원한다

2. **개발 편의성**

   - 기본값 검증이 자동화되어 있다
   - 내장된 인증 및 보안 기능을 제공한다
   - 의존성 주입 시스템을 갖추고 있다
   - 플러그인 확장이 가능하다
   - 100% 테스트 가능한 구현을 지원한다

3. **성능과 확장성**
   - Starlette 기반의 ASGI 프레임워크이다
   - 비동기 처리를 지원한다
   - 높은 성능과 확장성을 제공한다

## FastAPI vs Django

Django는 풍부한 내장 기능을 제공하지만, 때로는 불필요한 기능까지 포함되어 애플리케이션이 무거워질 수 있다. 반면 FastAPI는 필요한 기능만 선택적으로 사용할 수 있어 더 가볍고 유연하다.

# ASGI (Asynchronous Server Gateway Interface)

## ASGI란?

ASGI는 WSGI의 비동기 버전으로, Python 웹 애플리케이션과 웹 서버 간의 통신을 위한 인터페이스이다. 비동기 통신을 지원하여 더 효율적인 웹 애플리케이션 개발이 가능하다.

## ASGI 서버

- Uvicorn은 ASGI 서버의 대표적인 구현체이다
- 비동기 처리에 최적화된 인터페이스를 제공한다
- 높은 성능과 확장성을 보장한다

# URL 구조 이해하기

## URL vs URI

URL(Uniform Resource Locator)은 URI(Uniform Resource Identifier)의 하위 개념으로, 리소스의 위치를 나타낸다.

## URL 구성 요소

1. **스킴(Scheme)**

   - 프로토콜을 지정한다 (http://, https://)
   - 하이퍼텍스트 전송 규약을 사용한다

2. **도메인(Domain)**

   - 웹사이트의 주소를 나타낸다
   - 예: example.com

3. **포트(Port)**

   - 서비스 접근 포인트를 지정한다
   - 기본값: HTTP(80), HTTPS(443)

4. **경로(Path)**

   - 리소스의 위치를 나타낸다
   - 예: /users/profile

5. **파라미터(Parameters)**

   - 쿼리 파라미터: ?key=value
   - 패스 파라미터: /users/{id}
   - 쿠키 파라미터: 브라우저 저장 데이터
   - 헤더 파라미터: HTTP 헤더 정보

6. **앵커(Anchor)**
   - 페이지 내 특정 위치를 지정한다
   - 예: #section1
