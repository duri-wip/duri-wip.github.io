---
layout: post
title: "LangChain Output Parser 완전 정복"
excerpt: "구조화된 출력을 위한 Output Parser 활용법"
date: 2025-07-29
categories: [ML&AI]
tags: [genai-llm, langchain, output-parser, pydantic]
---

# 아웃풋 형식 지정하기

## Output Parser 개념

특정 형식으로 출력하도록 지정하는 프롬프트 작성과 응답 텍스트의 변환 기능을 제공합니다. PydanticOutputParser는 langchain의 output parser 중 하나로 LLM 출력을 파이썬 객체로 변환합니다.

### Pydantic Output Parser 사용법

```python
# 출력하게 할 형식을 pydantic 모델로 지정
from pydantic import BaseModel, Field

class City(BaseModel):
    country: str = Field(description = "The country the city is in")
    city: str = Field(description = "The city name")


# pydantic 모델을 사용하는 프롬프트 작성
from langchain_core.output_parsers import PydanticOutputParser

output_parser = PydanticOutputParser(pydantic_object = City)

format_instructions = output_parser.get_format_instructions()
print(format_instructions)

from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system",
     "사용자가 입력한 국가의 수도를 출력해주세요.",
     "{format_instructions}"),
     ("human", "{input}")
])

prompt_value = prompt.partial(
    format_instructions = format_instructions
)
prompt.invoke({"input": "France"})
print(prompt_value.messages[0].content)
print(prompt_value.messages[1].content)
```

### String Output Parser 사용법

LLM의 출력을 텍스트로 변환하는데 사용됩니다. LCEL과 연관됩니다.

```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.messages import AIMessage, HumanMessage

output_parser = StrOutputParser()

ai_message = AIMessage(content = "The capital of France is Paris.")
ai_message = output_parser.invoke(ai_message)
print(ai_message)
```

## 📚 추가로 알아볼 내용

### 1. 고급 Output Parser 종류

**JsonOutputParser:**

```python
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class Recipe(BaseModel):
    name: str = Field(description="요리 이름")
    ingredients: list[str] = Field(description="재료 목록")
    steps: list[str] = Field(description="조리 과정")

parser = JsonOutputParser(pydantic_object=Recipe)

# 프롬프트에 포맷 지시사항 자동 추가
prompt = ChatPromptTemplate.from_template(
    "사용자 질문: {query}\n{format_instructions}"
)
```

**CommaSeparatedListOutputParser:**

```python
from langchain_core.output_parsers import CommaSeparatedListOutputParser

output_parser = CommaSeparatedListOutputParser()
format_instructions = output_parser.get_format_instructions()
# 결과: "Your response should be a list of comma separated values, eg: `foo, bar, baz`"
```

**DatetimeOutputParser:**

```python
from langchain_core.output_parsers import DatetimeOutputParser

output_parser = DatetimeOutputParser()
# ISO 8601 형식의 datetime 객체로 변환
```

### 2. Custom Output Parser 만들기

```python
from langchain_core.output_parsers import BaseOutputParser
from langchain_core.exceptions import OutputParserException
from typing import List
import re

class EmailListParser(BaseOutputParser[List[str]]):
    """이메일 주소 목록을 파싱하는 커스텀 파서"""

    def parse(self, text: str) -> List[str]:
        # 이메일 패턴 정규식
        email_pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'
        emails = re.findall(email_pattern, text)

        if not emails:
            raise OutputParserException(f"No valid emails found in: {text}")

        return emails

    def get_format_instructions(self) -> str:
        return "응답에는 유효한 이메일 주소들을 포함해주세요."

# 사용 예시
email_parser = EmailListParser()
```

### 3. Output Parser와 Pydantic 고급 활용

```python
from pydantic import BaseModel, Field, validator
from typing import Optional, List
from enum import Enum

class Priority(str, Enum):
    HIGH = "high"
    MEDIUM = "medium"
    LOW = "low"

class Task(BaseModel):
    title: str = Field(description="작업 제목")
    description: str = Field(description="작업 설명")
    priority: Priority = Field(description="작업 우선순위")
    due_date: Optional[str] = Field(description="마감일 (YYYY-MM-DD)")
    tags: List[str] = Field(description="작업 태그 목록")

    @validator('due_date')
    def validate_date_format(cls, v):
        if v is not None:
            import datetime
            try:
                datetime.datetime.strptime(v, '%Y-%m-%d')
            except ValueError:
                raise ValueError('날짜는 YYYY-MM-DD 형식이어야 합니다')
        return v
```

## 🔍 학습하면서 알게 된 점

### 1. PydanticOutputParser vs JsonOutputParser의 차이

**PydanticOutputParser:**

- Pydantic 모델 기반 검증
- 타입 안전성 보장
- 복잡한 중첩 구조 지원
- 자동 필드 검증

**JsonOutputParser:**

- 단순 JSON 파싱
- Pydantic 모델 선택적 사용
- 더 빠른 처리 속도
- 유연한 구조

### 2. Format Instructions의 중요성

```python
# 잘못된 예시
prompt = ChatPromptTemplate.from_template("날씨 정보를 JSON으로 주세요")

# 올바른 예시
prompt = ChatPromptTemplate.from_template(
    "날씨 정보를 다음 형식으로 주세요:\n{format_instructions}\n질문: {query}"
)
prompt = prompt.partial(format_instructions=parser.get_format_instructions())
```

**핵심:** LLM은 정확한 형식 지시사항이 있을 때 더 일관된 결과를 출력합니다.

### 3. 파싱 에러 처리 전략

```python
from langchain_core.output_parsers import OutputFixingParser
from langchain_openai import ChatOpenAI

# 자동 수정 파서
fixing_parser = OutputFixingParser.from_llm(
    parser=PydanticOutputParser(pydantic_object=Recipe),
    llm=ChatOpenAI(temperature=0)
)

# 재시도 파서
from langchain_core.output_parsers import RetryOutputParser

retry_parser = RetryOutputParser.from_llm(
    parser=PydanticOutputParser(pydantic_object=Recipe),
    llm=ChatOpenAI(temperature=0)
)

try:
    result = retry_parser.parse_with_prompt(malformed_output, original_prompt)
except Exception as e:
    # 최종 실패 처리
    print(f"파싱 실패: {e}")
```

### 4. 성능 최적화 팁

**1. 간단한 구조 선호:**

```python
# 복잡한 중첩보다는 평면적 구조
class SimpleTask(BaseModel):
    title: str
    priority: str
    completed: bool = False
```

**2. 선택적 필드 활용:**

```python
class FlexibleResponse(BaseModel):
    required_field: str
    optional_field: Optional[str] = None  # LLM이 생략 가능
```

**3. Enum 활용으로 값 제한:**

```python
class Status(str, Enum):
    PENDING = "pending"
    IN_PROGRESS = "in_progress"
    COMPLETED = "completed"
```

### 5. 실제 프로덕션 환경에서의 주의사항

**토큰 비용 고려:**

- 복잡한 format_instructions는 토큰을 많이 소모
- 자주 사용되는 파서는 캐싱 고려

**에러 복구:**

```python
def robust_parse(parser, text, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return parser.parse(text)
        except OutputParserException as e:
            if attempt == max_attempts - 1:
                # 마지막 시도 실패시 기본값 또는 부분 파싱 결과 반환
                return extract_partial_data(text)
            # 다음 시도를 위한 텍스트 전처리
            text = preprocess_for_retry(text)
```

**로깅과 모니터링:**

- 파싱 실패율 추적
- 자주 실패하는 패턴 분석
- LLM 응답 품질 개선을 위한 프롬프트 조정
