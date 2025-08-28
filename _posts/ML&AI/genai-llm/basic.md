---
title: "LangChain 기본 사용법"
excerpt: "LangChain 패키지 그룹 소개와 OpenAI 프레임워크 활용"
category: genai
tags: 
  - [LangChain, OpenAI, ChatGPT, study]
study_name: "LangChain 마스터"
study_status: "completed"
study_description: "LangChain을 활용한 AI 애플리케이션 개발 학습"
toc: true
toc_sticky: true
date: 2024-03-05
last_modified_at: 2024-03-05
---

# langchain

목차
1. 패키지 그룹 소개
2. openai 프레임워크에서 싱글턴 질의 보내기
3. 대화내용 함께 보내기
4. 프롬프트 템플릿 사용하기
5. message holder 사용하기
6. 멀티모달 기능을 이용하여 이미지 입력하기

## langchain 패키지 그룹 소개

1. langchain-core
추상화와 LCEL을 제공한다.

2. partners
언어 모델 통합 클래스를 제공함. partners에서 제공되지 않는 모델은 community 패키지에서 제공된다.

3. langchain, langchain-text-splitters, langchain-experimental
청킹 기능, 다양한 실험 기능을 제공한다. 

### 설치

```python
!pip install langchain-core langchain-openai pydantic
```

## open ai 프레임워크 사용 
### 1. 싱글턴 질의 보내기

```python
from langchain_openai import OpenAI

model = OpenAI(model = "gpt-3.5-turbo-instruct", temperature = 0.0, api_key = "api_key")
output = model.invoke("Hello, world!")
print(output)
```

### 2. 대화 내용 함께 보내기
**ChatOpenAI vs OpenAI 비교**
1. OpenAI는 단순 텍스트 입력만 가능하지만, ChatOpenAI는 대화 내용을 함께 보낼 수 있음
2. OpenAI는 단일 질의에 적합하고, ChatOpenAI는 대화형 상호작용에 적합함
3. OpenAI는 gpt-3.5-turbo-instruct 같은 명령어 기반 모델을, ChatOpenAI는 gpt-4 같은 채팅 기반 모델을 사용
4. ChatOpenAI는 SystemMessage로 AI의 역할을 지정할 수 있음

```python
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model = "gpt-4o-mini", temperature = 0.0, api_key = "api_key")

messages = [
    SystemMessage(content = "당신은 사용자에게 음식의 레시피를 알려주는 요리사입니다. 조리 순서와 방법을 알려주세요."),
    HumanMessage(content = "치즈 요리를 먹고 싶습니다."),
    AIMessage(content = "어떤 치즈 요리를 좋아하시나요?"),
    HumanMessage(content = "치즈 퐁듀 레시피를 알려주세요.")
]

ai_message = model.invoke(messages)
print(ai_message.content)
```

### 3. chat prompt template 사용하기

```python
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant that can answer questions."),
    ("human", "{input}")
])

prompt_value = prompt.invoke({"input": "What is the capital of France?"})
print(prompt_value)
```

### 4. messageholder
여러 메시지가 들어가는 플레이스 홀더를 넣기

```python
from langchain_core.messages import HumanMessage, AIMessage
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant that can answer questions."),
    MessagesPlaceholder("chat_history", optional=True),
    ("human", "{input}")
])

prompt_value = prompt.invoke({
    "chat_history": [
        HumanMessage(content = "What is the capital of France?"),
        AIMessage(content = "The capital of France is Paris.")
    ],
    "input": "What is the capital of Germany?"
})

print(prompt_value)
```

### 멀티모달 기능

```python
from langchain_core.prompts import ChatPromptTemplate
import base64

def encode_image_to_base64(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode('utf-8')

prompt = ChatPromptTemplate.from_messages([
    (
        "user",
        [
            {"type": "text", "text": "{input}"},
            {"type": "image_url", "image_url": {"url": "{url_input}"}},
            {"type": "image_url", "image_url": {"url": "data:image/jpeg;base64,{local_image}"}}
        ]
    )
])

# URL 이미지 사용
url_prompt_value = prompt.invoke({
    "input": "What is the capital of France?", 
    "url_input": "https://i.imgur.com/ZcQmbtY.jpg",
    "local_image": ""
})
print("URL 이미지 결과:")
print(url_prompt_value)

# 로컬 이미지 사용
local_image_path = "path/to/your/local/image.jpg"
base64_image = encode_image_to_base64(local_image_path)
local_prompt_value = prompt.invoke({
    "input": "What is the capital of France?",
    "url_input": "",
    "local_image": base64_image
})
print("\n로컬 이미지 결과:")
print(local_prompt_value)
```

## 📚 추가로 알아볼 내용

### 1. LangChain 아키텍처 심화 이해

**핵심 컴포넌트들의 상호작용:**
- **Models**: LLM, Chat Models, Text Embedding Models
- **Prompts**: Prompt Templates, Example Selectors, Output Parsers
- **Memory**: Conversation Buffer, Summary Memory, Vector Store Memory
- **Chains**: LLM Chain, Sequential Chain, Router Chain
- **Agents**: Tool-using AI agents that can reason and act
- **Callbacks**: 실행 과정 모니터링 및 로깅

### 2. 메시지 타입별 활용 전략

```python
# Function Message 활용 (Tool Calling)
from langchain_core.messages import FunctionMessage

messages = [
    SystemMessage(content="You are a helpful assistant with access to tools"),
    HumanMessage(content="What's the weather like?"),
    FunctionMessage(content="{'temperature': 22, 'condition': 'sunny'}", name="get_weather")
]
```

**주요 메시지 타입:**
- `SystemMessage`: AI의 역할과 행동 지침 설정
- `HumanMessage`: 사용자 입력
- `AIMessage`: AI 응답 (tool_calls 포함 가능)
- `FunctionMessage`: 함수 실행 결과 (deprecated, ToolMessage 사용 권장)
- `ToolMessage`: Tool 실행 결과

### 3. Temperature 설정의 실제 영향

```python
# 창의적 작업 (temperature=0.7-1.0)
creative_model = ChatOpenAI(temperature=0.9)  # 스토리텔링, 브레인스토밍

# 일관된 답변 (temperature=0.0-0.3)  
factual_model = ChatOpenAI(temperature=0.0)   # 사실 확인, 코드 생성

# 균형잡힌 작업 (temperature=0.3-0.7)
balanced_model = ChatOpenAI(temperature=0.5)  # 일반적인 대화
```

## 🔍 학습하면서 알게 된 점

### 1. OpenAI vs ChatOpenAI의 실제 차이점

**OpenAI 클래스:**
- Completion API 기반 (GPT-3.5-turbo-instruct)
- 단순 텍스트 in/out
- 레거시 모델 지원
- 프롬프트 엔지니어링이 더 중요

**ChatOpenAI 클래스:**
- Chat Completions API 기반 (GPT-4, GPT-3.5-turbo)
- 구조화된 대화 형식
- Function Calling 지원
- 시스템 메시지로 역할 정의 가능

### 2. MessagesPlaceholder의 강력함

```python
# 동적으로 대화 히스토리 길이 조절
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    MessagesPlaceholder("chat_history", optional=True),  # 없어도 됨
    ("human", "{input}")
])

# 빈 히스토리도 처리 가능
result = prompt.invoke({"input": "Hello"})  # chat_history 없어도 작동
```

### 3. 멀티모달 처리 시 주의사항

**이미지 크기 제한:**
- GPT-4V: 이미지당 20MB, base64 인코딩 시 더 커짐
- 해상도: 최대 2048x2048 권장

**비용 최적화:**
```python
# 이미지 크기 최적화 함수
from PIL import Image

def optimize_image_for_llm(image_path, max_size=(1024, 1024)):
    with Image.open(image_path) as img:
        img.thumbnail(max_size, Image.Resampling.LANCZOS)
        # 메모리에서 base64로 인코딩
        import io
        buffer = io.BytesIO()
        img.save(buffer, format='JPEG', quality=85)
        return base64.b64encode(buffer.getvalue()).decode()
```

### 4. API 키 관리 Best Practices

```python
# 환경변수 사용 (권장)
import os
api_key = os.getenv("OPENAI_API_KEY")

# .env 파일 사용
from dotenv import load_dotenv
load_dotenv()

# 키 검증
if not api_key:
    raise ValueError("OpenAI API key not found")

# 프록시 설정 (기업 환경)
model = ChatOpenAI(
    api_key=api_key,
    openai_proxy="http://proxy.company.com:8080"
)
```

### 5. 실제 프로덕션에서의 고려사항

**에러 핸들링:**
```python
from langchain_core.exceptions import LangChainException
import time

def retry_llm_call(model, messages, max_retries=3):
    for attempt in range(max_retries):
        try:
            return model.invoke(messages)
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)  # Exponential backoff
```

**비용 모니터링:**
- Token 계산기 활용
- 요청 로깅
- Rate limiting 구현