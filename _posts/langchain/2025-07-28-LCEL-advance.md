---
title: "LCEL 고급 활용법"
excerpt: "LangChain Expression Language의 심화 기능과 활용"
category: genai
tags:
  - [LangChain, LCEL, Runnable, Chain, study]
study_name: "LangChain 마스터"
study_status: "completed"
study_description: "LangChain을 활용한 AI 애플리케이션 개발 학습"
toc: true
toc_sticky: true
date: 2025-07-28
last_modified_at: 2025-08-01
---

# LCEL의 작동 원리

LCEL에서는 왜 프롬프트, 모델, 아웃풋 파서를 `|`(체인)으로 연결해서 실행할 수 있을까?
모든 클래스가 langchain의 **Runnable** 클래스를 상속받고 있기 때문입니다.

Runnable을 연결하면 **RunnableSequence**가 됩니다. 이 시퀀스도 Runnable의 일종입니다.

RunnableSequence를 invoke하면 runnable이 순서대로 invoke됩니다.

## Runnable의 실행 방법

Runnable의 실행 방법으로는 3가지가 있습니다:

- **invoke**: 동기 실행
- **stream**: 스트리밍 실행
- **batch**: 배치 실행

비동기로 실행할 수 있는 방법으로도 3가지가 제공됩니다:

- **ainvoke**: 비동기 실행
- **astream**: 비동기 스트리밍
- **abatch**: 비동기 배치

### Stream 예제

```python
chain = prompt | model | output_parser

for chunk in chain.stream({"input": "..."}):
    print(chunk, end = "", flush = True)
```

### Batch 예제

```python
chain = prompt | model | output_parser

output = chain.batch([{"input":"..."}, {"input":"..."}])
print(output)
```

## LCEL 내부 구현

LCEL은 어떻게 구현되었길래 체인으로 모델과 프롬프트를 연결할 수 있을까요?
`__or__`과 `__ror__`가 이렇게 구현되어 있습니다:

```python
def __or__(self,
    other: Union[Runnable[Any, Other],
                Callable[[Any], Other]],
                Callable[Iterator[Any], Iterator[Other]],
                Mapping[str, Union[Runnable[Any,Other]], Callable[[Any],Other],Any]) -> RunnableSerializable[Input, Other]:
        return RunnableSequence(first=self, last=coerce_to_runnable(other))

def __ror__(self,
    other: Union[Runnable[Other, Any],
                Callable[[Other], Any]],
                Callable[Iterator[Other], Iterator[Any]],
                Mapping[str, Union[Runnable[Other,Any]], Callable[[Other],Any],Any]) -> RunnableSerializable[Other, Output]:
        return RunnableSequence(first=coerce_to_runnable(other), last=self)
```

이 연산자들을 통해 체인을 오버로드할 수 있습니다.

각각의 러너블을 실행(호출)하는 방법이 다르면, 이 컴포넌트들을 연결하는 처리를 개별적으로 구현해야하는데, LCEL을 사용하면 모든 컴포넌트를 **통일된 인터페이스**로 호출할 수 있게 됩니다.

## 다양한 러너블 연결하기

### 1. 두개의 체인 연결하기

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model = "gpt-4o-mini", temperature = 0)
output_parser = StrOutputParser()

## 체인 1 : 답변을 단계적으로 생성하기
cot_prompt = ChatPromptTemplate.from_messages([
    ("system", "사용자의 질문에 단계적으로 답변하세요"),
    ("user", "{input}"),
])

cot_chain = cot_prompt | model | output_parser

## 체인 2: 결론 추출하기
summary_prompt = ChatPromptTemplate.from_messages([
    ("system", "단계적으로 생각한 답변에서 결론을 추출하세요"),
    ("user", "{text}"),
])

summary_chain = summary_prompt | model | output_parser

## 체인 3: 체인 연결하기
chain = cot_chain | summary_chain

## 체인 실행하기
output = chain.invoke({"input": "오늘 날씨가 어때?"})
print(output)
```

**주의**: 입력 타입과 출력 타입의 일관성을 유지해야 합니다!

## 임의의 함수를 러너블로 만들기: RunnableLambda

어떤 변환을 적용하거나 추가 처리를 하는 함수를 러너블로 만들면 체인간 변환을 수행하는 기능을 구현할 수 있습니다.

### 1. 기본 구현 방식

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnableLambda
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_messages([
    ("system", "사용자의 질문에 단계적으로 답변하세요"),
    ("user", "{input}"),
])
model = ChatOpenAI(model = "gpt-4o-mini", temperature = 0)
output_parser = StrOutputParser()

# 임의의 함수를 러너블로 만들기
def add_prefix(text: str) -> str:
    return "결론: " + text

add_prefix_runnable = RunnableLambda(add_prefix)

# 체인 구성
chain = prompt | model | output_parser | add_prefix_runnable

# 체인 실행
output = chain.invoke({"input": "오늘 날씨가 어때?"})
print(output)
```

### 2. 데코레이터를 사용한 구현 방식

```python
from langchain_core.runnables import chain

@chain
def add_prefix(text: str) -> str:
    return "결론: " + text

chain = prompt | model | output_parser | add_prefix

output = chain.invoke({"input": "오늘 날씨가 어때?"})
print(output)
```

### 3. 암시적으로 생성하기

```python
def add_prefix(text: str) -> str:
    return "결론: " + text

chain = prompt | model | output_parser | add_prefix

output = chain.invoke({"input": "오늘 날씨가 어때?"})
print(output)
```

## Stream 처리 시 주의사항

사용자 함수를 사용할때 stream으로 호출하면 점진적으로 출력되지 않고 처리가 완전히 끝난 시점에 한꺼번에 출력됩니다. 사용자 정의함수는 입력을 한꺼번에 처리하도록 되어있기 때문입니다.

점진적으로 처리하기 위해 제너레이터 함수를 사용해야 합니다:

```python
def upper(input: Iterator[str]) -> Iterator[str]:
    for text in input:
        yield text.upper()
```

## 여러 러너블을 병렬로 연결하기

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model = "gpt-4o-mini", temperature = 0)
output_parser = StrOutputParser()

pros_prompt = ChatPromptTemplate.from_messages([
    ("system", "사용자의 진술에서 장점을 찾아서 긍정적으로 답변하세요."),
    ("user", "{input}"),
])

pros_chain = pros_prompt | model | output_parser

cons_prompt = ChatPromptTemplate.from_messages([
    ("system", "사용자의 진술에서 단점을 찾아서 부정적으로 답변하세요."),
    ("user", "{input}"),
])

cons_chain = cons_prompt | model | output_parser

## 병렬 체인 구성
chain = RunnableParallel(
    {"장점": pros_chain},
    {"단점": cons_chain},
)

## 병렬 체인 실행
output = chain.invoke({"input": "오늘 날씨가 어때?"})
print(output)

# 요약 체인 구성
summary_prompt = ChatPromptTemplate.from_messages([
    ("system", "장점과 단점을 요약하세요."),
    ("user", "{장점} {단점}"),
])

summary_chain = (RunnableParallel(
    {"장점": pros_chain},
    {"단점": cons_chain},
) | summary_prompt | model | output_parser
)

## 암시적으로 병렬 체인 구성하기
summary_chain2 = (
    {"장점": pros_chain, "단점": cons_chain} | summary_prompt | model | output_parser
)
```

## Item Getter 사용

딕셔너리에서 값을 추출하는 함수를 만들 수 있습니다:

```python
from operator import itemgetter

topic_getter = itemgetter("장점")
topic = topic_getter({"장점":"맛있다."})
print(topic)
# > 맛있다.

# 체인 예시
synt_prompt = ChatPromptTemplate.from_messages([
    ("system", "사용자의 진술에서 {topic}에 대한 장점과 단점을 찾아서 긍정적으로 답변하세요."),
    ("user", "장점 :{장점}\n단점 :{단점}"),
])

synt_chain = {
    "장점": pros_chain,
    "단점": cons_chain,
    "topic": itemgetter("topic"),
} | synt_prompt | model | output_parser

output = synt_chain.invoke({"topic": "회사를 다니고 있습니다."})
```

## RunnablePassthrough 활용

### RAG 처리 구현

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_template('''
                                           다음 문맥만을 고려하여 질문에 대답하세요.
                                           문맥: "{context}"
                                           질문: "{question}"
                                           ''')

model = ChatOpenAI(model = "gpt-4o-mini", temperature = 0)

from langchain_community.retrievers import TravilySearchAPIRetriever

retriever = TravilySearchAPIRetriever(
    api_key = os.getenv("TRAVILY_API_KEY"),
    max_results = 2,
    k=3)

from langchain_core.runnables import RunnablePassthrough

chain = (
    {"context": retriever, "question": RunnablePassthrough()} | prompt | model | output_parser
)

output = chain.invoke({"question": "오늘 날씨가 어때?"})
print(output)
```

### 러너블 체인에 값 추가하기: assign

```python
import pprint

chain = {
    "question": RunnablePassthrough(),
    "context": retriever,
} | RunnablePassthrough.assign(
    answer=prompt | model | StrOutputParser()
)

output = chain.invoke({"question": "오늘 날씨가 어때?"})
pprint(output)

## assign은 인스턴스 메서드로도 제공됩니다
chain = RunnableParallel({
    "question": RunnablePassthrough(),
    "context": retriever,
}).assign(answer=prompt | model | StrOutputParser())

output = chain.invoke({"question": "오늘 날씨가 어때?"})
pprint(output)
```

### Pick으로 일부 값만 선택

```python
chain = RunnableParallel({
    "question": RunnablePassthrough(),
    "context": retriever,
}).assign(answer=prompt | model | StrOutputParser()
          ).pick("context", "answer")

output = chain.invoke({"question": "오늘 날씨가 어때?"})
pprint(output)
```

## 중간값 출력: astream_events

```python
chain = (
    {"context": retriever, "question": RunnablePassthrough()} | prompt | model | StrOutputParser()
)

async for event in chain.astream_events("서울의 현재 날씨는?", version = "v2"):
    event_kind = event["event"]
    if event_kind == "on_retriever_end":
        print("검색 결과:")
        documents = event["data"]["output"]
        for document in documents:
            print(document)

    elif event_kind == "on_parser_start":
        print("출력 파서 시작")

    elif event_kind == "on_parser_stream":
        chunk = event["data"]["chunk"]
        print(chunk, end = "", flush = True)
```

## Chat History 관리

```python
from langchain_community.chat_message_histories import SQLChatMessageHistory

def respond(session_id: str, human_message: str) -> str:
    chat_message_history = SQLChatMessageHistory(
        session_id = session_id,
        connection = "sqlite:///sqlite.db",
    )
    messages = chat_message_history.get_messages()

    ai_message = chain.invoke({
        "chat_history": messages,
        "human_message": human_message,
    })

    chat_message_history.add_user_message(human_message)
    chat_message_history.add_ai_message(ai_message)

    return ai_message

# 호출
from uuid import uuid4

session_id = uuid4().hex
output1 = respond(session_id=session_id, human_message="안녕하세요. 제 이름은 나뭇잎입니다.")
print(output1)

output2 = respond(session_id=session_id, human_message="내 이름이 뭘까?")
print(output2)
```

## 지원하는 데이터베이스

SQLite 말고 다른 데이터베이스의 통합도 제공합니다:

- 인메모리
- SQLAlchemy가 지원하는 각종 관계형 데이터베이스
- Redis
- DynamoDB
- CosmosDB
- Momento
  등

## 메모리

대화 이력에 대한 고급 처리를 구현하고 싶은 경우 메모리를 사용합니다. 예를 들어 최근 k개의 대화 이력만 포함하고 싶은 경우.
ConversationBufferWindowMemory나 ConversationSummaryMemory를 해당 컴포넌트로 사용할 수 있습니다.

## LangServe

LangChain의 러너블을 간편하게 REST API로 만들기 위한 패키지입니다.

## 📚 추가로 알아볼 내용

### 1. 고급 Runnable 패턴들

**RunnableGenerator (스트리밍 생성기):**

```python
from langchain_core.runnables import RunnableGenerator

def streaming_splitter(input: Iterator[str]) -> Iterator[str]:
    buffer = ""
    for chunk in input:
        buffer += chunk
        # 문장 단위로 분할하여 yield
        while '. ' in buffer:
            sentence, buffer = buffer.split('. ', 1)
            yield sentence + '. '

    if buffer:
        yield buffer

streaming_chain = model | RunnableGenerator(streaming_splitter)
```

**RunnableRetry (재시도 로직):**

```python
from langchain_core.runnables import RunnableRetry

retry_chain = RunnableRetry(
    bound=model,
    max_attempt_number=3,
    wait_exponential_jitter=True,
    stop_after_delay=30
)
```

**RunnableWithMessageHistory (대화 이력 관리):**

```python
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import SQLChatMessageHistory

def get_session_history(session_id: str):
    return SQLChatMessageHistory(session_id, "sqlite:///memory.db")

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_session_history,
    input_messages_key="input",
    history_messages_key="chat_history",
)
```

### 2. 복잡한 라우팅 전략

**Multi-Modal Routing:**

```python
from typing import Literal

class RouteInput(BaseModel):
    query: str
    data_type: Literal["text", "image", "code"] = "text"

def route_by_type(input: RouteInput):
    if input.data_type == "image":
        return vision_chain
    elif input.data_type == "code":
        return code_analysis_chain
    else:
        return general_chain

routing_chain = RunnableLambda(route_by_type)
```

**Semantic Routing:**

```python
from langchain_community.utils.math import cosine_similarity

def semantic_router(query: str, route_embeddings: dict):
    query_embedding = embeddings.embed_query(query)

    similarities = {}
    for route, embedding in route_embeddings.items():
        sim = cosine_similarity([query_embedding], [embedding])[0][0]
        similarities[route] = sim

    best_route = max(similarities, key=similarities.get)
    return route_chains[best_route]
```

### 3. 고급 병렬 처리 패턴

**조건부 병렬 실행:**

```python
def conditional_parallel(input_data):
    base_tasks = {"summary": summary_chain}

    # 조건에 따라 추가 작업 결정
    if len(input_data.get("text", "")) > 1000:
        base_tasks["keywords"] = keyword_chain

    if input_data.get("analyze_sentiment", False):
        base_tasks["sentiment"] = sentiment_chain

    return RunnableParallel(base_tasks).invoke(input_data)
```

**단계적 병렬 처리:**

```python
# Phase 1: 기본 분석
phase1 = RunnableParallel({
    "entities": entity_extraction_chain,
    "classification": classification_chain
})

# Phase 2: Phase 1 결과를 기반으로 한 고급 분석
def phase2_router(phase1_results):
    tasks = {}

    if phase1_results["classification"] == "technical":
        tasks["tech_analysis"] = technical_chain

    if len(phase1_results["entities"]) > 5:
        tasks["entity_relations"] = relation_chain

    return RunnableParallel(tasks).invoke(phase1_results)

two_phase_chain = phase1 | RunnableLambda(phase2_router)
```

### 4. 메모리 최적화 고급 기법

**청크별 스트림 처리:**

```python
def chunked_processing(large_input: str, chunk_size: int = 1000):
    """대용량 텍스트를 청크별로 스트림 처리"""
    chunks = [large_input[i:i+chunk_size]
              for i in range(0, len(large_input), chunk_size)]

    for chunk in chunks:
        yield chain.stream({"input": chunk})

# 사용법
for chunk_result in chunked_processing(huge_document):
    for token in chunk_result:
        print(token, end="", flush=True)
```

**메모리 사용량 모니터링:**

```python
import psutil
import os

def memory_monitor(func):
    def wrapper(*args, **kwargs):
        process = psutil.Process(os.getpid())
        initial_memory = process.memory_info().rss / 1024 / 1024  # MB

        result = func(*args, **kwargs)

        final_memory = process.memory_info().rss / 1024 / 1024
        print(f"Memory usage: {final_memory - initial_memory:.2f} MB")

        return result
    return wrapper

@memory_monitor
def process_large_chain(input_data):
    return chain.invoke(input_data)
```

## 🔍 학습하면서 알게 된 점

### 1. astream_events의 실제 활용 방법

```python
async def detailed_streaming_analysis():
    events = []
    async for event in chain.astream_events("분석할 텍스트", version="v2"):
        event_type = event["event"]

        if event_type == "on_chat_model_start":
            print("🚀 LLM 호출 시작")
            events.append(("llm_start", event["data"]))

        elif event_type == "on_chat_model_stream":
            token = event["data"]["chunk"].content
            print(token, end="", flush=True)

        elif event_type == "on_retriever_end":
            docs = event["data"]["output"]
            print(f"\n📚 검색된 문서 수: {len(docs)}")

        elif event_type == "on_chain_end":
            print("\n✅ 체인 완료")

    return events
```

**핵심 인사이트:**

- `astream_events`는 디버깅과 사용자 경험 개선에 핵심적
- 각 컴포넌트의 실행 시간과 결과를 실시간 추적 가능
- 사용자에게 진행 상황을 시각적으로 보여줄 수 있음

### 2. RunnablePassthrough의 숨겨진 활용법

```python
# 1. 조건부 데이터 전달
def conditional_passthrough(data):
    if data.get("debug"):
        print(f"Debug info: {data}")
    return data

debug_chain = RunnableLambda(conditional_passthrough) | main_chain

# 2. 데이터 변환과 원본 보존
transform_and_keep = RunnableParallel({
    "original": RunnablePassthrough(),
    "processed": preprocessing_chain,
    "metadata": RunnableLambda(lambda x: {"timestamp": time.time()})
})
```

### 3. assign()과 pick()의 고급 활용

```python
# 복잡한 데이터 플로우 관리
complex_chain = (
    RunnableParallel({
        "user_input": RunnablePassthrough(),
        "context": retriever,
    })
    .assign(
        # 컨텍스트 기반 사전 분석
        preliminary_analysis=lambda x: analyze_context(x["context"])
    )
    .assign(
        # 사전 분석 기반 프롬프트 선택
        selected_prompt=lambda x: select_prompt(x["preliminary_analysis"]),
        # 메타데이터 추가
        processing_metadata=lambda x: {
            "context_length": sum(len(doc.page_content) for doc in x["context"]),
            "analysis_type": x["preliminary_analysis"]["type"]
        }
    )
    .assign(
        # 최종 답변 생성
        answer=lambda x: x["selected_prompt"].format_prompt(
            context=format_context(x["context"]),
            question=x["user_input"]
        ) | model | StrOutputParser()
    )
    .pick("user_input", "answer", "processing_metadata")  # 필요한 것만 선택
)
```

### 4. 에러 복구 전략의 실제 구현

```python
class GracefulDegradationChain:
    def __init__(self, primary_chain, fallback_chain, simple_fallback):
        self.primary = primary_chain
        self.fallback = fallback_chain
        self.simple = simple_fallback

    def invoke(self, input_data):
        # 1차 시도: 주요 체인
        try:
            return self.primary.invoke(input_data)
        except Exception as e1:
            print(f"Primary chain failed: {e1}")

            # 2차 시도: 폴백 체인
            try:
                return self.fallback.invoke(input_data)
            except Exception as e2:
                print(f"Fallback chain failed: {e2}")

                # 3차 시도: 간단한 응답
                try:
                    return self.simple.invoke(input_data)
                except Exception as e3:
                    # 최종 안전장치
                    return {
                        "answer": "죄송합니다. 일시적인 오류가 발생했습니다.",
                        "error": True,
                        "original_errors": [str(e1), str(e2), str(e3)]
                    }

# 사용법
resilient_chain = GracefulDegradationChain(
    primary_chain=complex_rag_chain,
    fallback_chain=simple_qa_chain,
    simple_fallback=RunnableLambda(lambda x: {"answer": "기본 응답을 제공합니다."})
)
```

### 5. 성능 최적화의 실전 경험

**배치 처리 최적화:**

```python
def optimized_batch_processing(inputs, batch_size=10):
    """메모리 효율적인 배치 처리"""
    results = []

    for i in range(0, len(inputs), batch_size):
        batch = inputs[i:i+batch_size]

        # 배치별 처리
        batch_results = chain.batch(batch)
        results.extend(batch_results)

        # 메모리 정리 (선택적)
        if i % 100 == 0:  # 100개 배치마다
            import gc
            gc.collect()

    return results
```

**캐싱 전략:**

```python
import hashlib
from functools import wraps

def smart_cache(ttl=3600):
    cache = {}

    def decorator(func):
        @wraps(func)
        def wrapper(input_data):
            # 입력 해시 생성
            input_str = str(sorted(input_data.items()) if isinstance(input_data, dict) else input_data)
            cache_key = hashlib.md5(input_str.encode()).hexdigest()

            # 캐시 확인
            if cache_key in cache:
                result, timestamp = cache[cache_key]
                if time.time() - timestamp < ttl:
                    return result

            # 새로 계산
            result = func(input_data)
            cache[cache_key] = (result, time.time())

            # 캐시 크기 관리 (LRU 방식)
            if len(cache) > 1000:
                oldest_key = min(cache, key=lambda k: cache[k][1])
                del cache[oldest_key]

            return result
        return wrapper
    return decorator

@smart_cache(ttl=1800)
def cached_chain_invoke(input_data):
    return expensive_chain.invoke(input_data)
```

### 6. 프로덕션 환경에서의 교훈

**모니터링 통합:**

```python
from datetime import datetime
import json

class ProductionChainWrapper:
    def __init__(self, chain, logger=None):
        self.chain = chain
        self.logger = logger

    def invoke(self, input_data):
        start_time = datetime.now()

        try:
            result = self.chain.invoke(input_data)

            # 성공 로그
            duration = (datetime.now() - start_time).total_seconds()
            self._log_success(input_data, result, duration)

            return result

        except Exception as e:
            duration = (datetime.now() - start_time).total_seconds()
            self._log_error(input_data, e, duration)
            raise

    def _log_success(self, input_data, result, duration):
        log_data = {
            "timestamp": datetime.now().isoformat(),
            "status": "success",
            "duration": duration,
            "input_tokens": self._estimate_tokens(str(input_data)),
            "output_tokens": self._estimate_tokens(str(result))
        }

        if self.logger:
            self.logger.info(json.dumps(log_data))

    def _estimate_tokens(self, text):
        # 간단한 토큰 추정 (실제로는 tiktoken 사용 권장)
        return len(text.split()) * 1.3
```

이러한 고급 기법들은 실제 프로덕션 환경에서 안정적이고 효율적인 LangChain 애플리케이션 구축에 필수적입니다.
