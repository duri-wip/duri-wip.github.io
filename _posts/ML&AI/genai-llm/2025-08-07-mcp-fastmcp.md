---
layout: post
title: "MCP 서버 구축 방식 비교"
excerpt: "MCP 서버를 독립적으로 구축하는 방식과 내부에 구축하는 방식 비교"
date: 2025-08-07
categories: [ML&AI]
subcategory: genai-llm

tags: [genai-llm, mcp, server-architecture, microservices]
---

# mcp 서버

mcp 서버를 따로 구축하는 것과 llm 서버 내부에 구축하는 방식의 비교

1. **HTTP 네트워크 통신을 위한 별도 MCP 서버 구축**

장점:

- 서비스 분리로 인한 높은 확장성과 유연성
- 도구들을 여러 LLM 서버에서 재사용 가능
- 도구 서버 업데이트/배포가 독립적으로 가능
- 리소스 분산으로 각 서버의 부하 관리 용이
- REST API로 다양한 클라이언트에서 접근 가능

단점:

- 네트워크 통신으로 인한 지연시간 발생
- 네트워크 장애 시 서비스 중단 가능성
- 인프라 관리 복잡도 증가 (두 개의 서버 관리)
- 보안 설정 필요 (인증, 인가, CORS 등)
- 네트워크 대역폭 사용

2. **LLM 서버 내 MCP stdio 모드로 통합**

장점:

- 매우 빠른 응답 시간 (프로세스 간 직접 통신)
- 네트워크 장애와 무관한 안정성
- 보안 설정 불필요 (외부 노출 없음)
- 단순한 인프라 구조
- 리소스 효율성 (하나의 서버로 통합)
- 배포/관리가 단순

단점:

- 도구 재사용이 어려움 (다른 서버에서 사용 불가)
- 서비스 확장이 제한적
- LLM 서버와 도구가 강하게 결합됨
- 도구 업데이트 시 전체 서버 재배포 필요
- LLM 서버의 부하가 증가할 수 있음

선택 기준:

1. **별도 HTTP 서버가 좋은 경우**:

   - 여러 LLM 서버에서 도구를 공유해야 할 때
   - 도구들을 자주 업데이트해야 할 때
   - 확장성이 중요한 대규모 시스템
   - 외부 서비스에서도 도구 접근이 필요할 때

2. **stdio 모드 통합이 좋은 경우**:
   - 단일 LLM 서버만 사용할 때
   - 빠른 응답 시간이 중요할 때
   - 간단한 시스템 구조를 원할 때
   - 보안이 매우 중요한 경우
   - 개발/테스트 환경

[아키텍처 구성도]

                                    [MCP 도구 서버]

[LLM 서버] FastAPI + FastMCP
| |
| HTTP 요청/응답 |
| ---------------------------------->|
| |
| 1. /tools (GET) |--- [도구 1] 문서 검색
| 2. /run/{tool} (POST) |--- [도구 2] 번역
| |--- [도구 3] 계산
| |
| |--- [DB/저장소]
| |
[다른 클라이언트] |--- [외부 API]
| HTTP 요청/응답 |
| ---------------------------------->|

FastMCP를 위한 별도 HTTP 서버 아키텍처를 설명드리겠습니다:

```
[아키텍처 구성도]

                                    [MCP 도구 서버]
[LLM 서버]                         FastAPI + FastMCP
   |                                     |
   |         HTTP 요청/응답              |
   | ---------------------------------->|
   |                                    |
   |          1. /tools (GET)           |--- [도구 1] 문서 검색
   |          2. /run/{tool} (POST)     |--- [도구 2] 번역
   |                                    |--- [도구 3] 계산
   |                                    |
   |                                    |--- [DB/저장소]
   |                                    |
[다른 클라이언트]                       |--- [외부 API]
   |          HTTP 요청/응답            |
   | ---------------------------------->|

```

**1. MCP 도구 서버 구현**

```python
# mcp_server/app.py
from fastapi import FastAPI, HTTPException
from fastmcp import FastMCP
from typing import Dict, Any

app = FastAPI()
mcp = FastMCP(name="Tool Server", version="1.0.0")

# 도구 구현
@mcp.tool
def search_docs(query: str) -> Dict[str, Any]:
    """문서 검색 도구"""
    try:
        # 검색 로직 구현
        results = perform_search(query)
        return {"results": results}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@mcp.tool
def translate(text: str, target_lang: str) -> Dict[str, str]:
    """번역 도구"""
    try:
        translated = perform_translation(text, target_lang)
        return {"translated_text": translated}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

# FastMCP 라우터 통합
app.include_router(mcp.router)

# 추가 엔드포인트
@app.get("/health")
def health_check():
    return {"status": "healthy"}
```

**2. LLM 서버에서의 도구 사용**

```python
# llm_server/tool_client.py
import requests
from typing import Dict, Any

class MCPToolClient:
    def __init__(self, base_url: str):
        self.base_url = base_url
        self.tools = self._fetch_tools()

    def _fetch_tools(self) -> Dict[str, Any]:
        """사용 가능한 도구 목록 조회"""
        response = requests.get(f"{self.base_url}/tools")
        return response.json()

    def run_tool(self, tool_name: str, parameters: Dict[str, Any]) -> Dict[str, Any]:
        """도구 실행"""
        response = requests.post(
            f"{self.base_url}/run/{tool_name}",
            json=parameters
        )
        return response.json()

# llm_server/llm_service.py
from .tool_client import MCPToolClient

class LLMService:
    def __init__(self):
        self.tool_client = MCPToolClient("http://mcp-server:3000")

    async def process_query(self, user_query: str):
        # LLM이 도구 사용이 필요하다고 판단하면
        if needs_tool(user_query):
            tool_result = self.tool_client.run_tool(
                "search_docs",
                {"query": user_query}
            )
            # 도구 결과를 LLM에 제공
            enhanced_response = self.generate_response(user_query, tool_result)
            return enhanced_response
```

**3. 배포 구성 (Docker Compose)**

```yaml
# docker-compose.yml
version: "3.8"

services:
  mcp-server:
    build: ./mcp-server
    ports:
      - "3000:3000"
    environment:
      - ELASTICSEARCH_URL=http://elasticsearch:9200
    depends_on:
      - elasticsearch

  llm-server:
    build: ./llm-server
    ports:
      - "8000:8000"
    environment:
      - MCP_SERVER_URL=http://mcp-server:3000
    depends_on:
      - mcp-server

  elasticsearch:
    image: elasticsearch:8.x
    ports:
      - "9200:9200"
```

**4. 보안 설정**

```python
# mcp_server/security.py
from fastapi import Security, HTTPException
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

async def verify_api_key(api_key: str = Security(api_key_header)):
    if api_key != VALID_API_KEY:
        raise HTTPException(status_code=403, detail="Invalid API key")
    return api_key

# app.py에 보안 적용
@app.post("/run/{tool_name}")
async def run_tool(
    tool_name: str,
    parameters: Dict[str, Any],
    api_key: str = Security(verify_api_key)
):
    # 도구 실행 로직
```

이 아키텍처의 특징:

1. **확장성**

   - 도구 서버와 LLM 서버가 독립적으로 스케일링 가능
   - 새로운 도구 추가가 용이

2. **모니터링/로깅**

   - 각 서버의 상태와 성능을 독립적으로 모니터링
   - 도구 사용 통계 수집 용이

3. **장애 격리**

   - 도구 서버 장애가 LLM 서버에 직접적인 영향을 주지 않음
   - 장애 복구가 독립적으로 가능

4. **보안**
   - API 키 기반 인증
   - 도구별 접근 제어 가능
   - 네트워크 수준의 보안 설정 가능

mcp를 쓰면 이런게 좋구나>

- 타입 검증
- 필수 타입 등에 대한 메서드 제공
- 통일된 인터페이스 제공
