---
layout: post
title: "프롬프트 엔지니어링 완전 정복"
excerpt: "CO-STAR, AUTOMAT 프레임워크와 고급 프롬프트 패턴 활용법"
date: 2025-08-06
categories: [ML&AI]
tags: [genai-llm, prompt-engineering, co-star, automat, chain-of-thought]
---

# 프롬프트 엔지니어링 고급 기법

## 목차

1. [프롬프트 프레임워크](#프롬프트-프레임워크)
   - [CO-STAR 프레임워크](#co-star-프레임워크)
   - [AUTOMAT 프레임워크](#automat-프레임워크)
2. [시스템 프롬프트와 템플릿](#시스템-프롬프트와-템플릿)
   - [LangChain 통합](#langchain-통합)
3. [고급 프롬프트 패턴](#고급-프롬프트-패턴)
   - [Chain-of-Thought 추론](#chain-of-thought-추론)
   - [Few-shot Learning](#few-shot-learning)

## Safety Filters (안전 필터)

[Google Cloud Vertex AI Safety Filters](https://cloud.google.com/vertex-ai/generative-ai/docs/multimodal/configure-safety-filters?hl=ko)

### 차단 사유

안전하지 않은 프롬프트에 대해서는 다음과 같은 응답이 제공됩니다:

- **PROHIBITED_CONTENT**: 금지된 콘텐츠(일반적으로 아동 성적 학대 콘텐츠)가 포함되어 신고되었으므로 프롬프트가 차단되었습니다.
- **BLOCKED_REASON_UNSPECIFIED**: 프롬프트 차단 이유가 지정되지 않았습니다.
- **OTHER**: 다른 모든 이유

### 유해 카테고리

콘텐츠 필터는 다음과 같은 유해 카테고리를 기준으로 콘텐츠를 평가합니다:

| 유해 카테고리 | 정의                                                                |
| ------------- | ------------------------------------------------------------------- |
| 증오심 표현   | ID 또는 보호 속성을 대상으로 하는 부정적이거나 유해한 댓글          |
| 괴롭힘        | 다른 사람을 대상으로 위협하거나 협박하거나 괴롭히거나 모욕하는 댓글 |
| 음란물        | 성행위 또는 기타 외설적인 콘텐츠에 대한 참조가 포함                 |
| 위험한 콘텐츠 | 유해한 상품, 서비스, 활동 홍보 및 이에 대한 액세스 지원             |

### 가드레일 종류

- **BLOCK_LOW_AND_ABOVE**: 확률 점수 또는 심각도 점수가 LOW, MEDIUM 또는 HIGH인 경우 차단
- **BLOCK_MEDIUM_AND_ABOVE**: 확률 점수 또는 심각도 점수가 MEDIUM 또는 HIGH인 경우 차단 (gemini-2.0-flash-001 및 이후 모델의 기본값)
- **BLOCK_ONLY_HIGH**: 확률 점수 또는 심각도 점수가 HIGH인 경우 차단
- **HARM_BLOCK_THRESHOLD_UNSPECIFIED**: 기본 기준점을 사용하여 차단
- **OFF**: 자동 대답 차단이 없으며 메타데이터가 반환되지 않음 (gemini-2.0-flash-001 및 이후 모델의 기본값)
- **BLOCK_NONE**: 자동 대답 차단을 삭제하고 반환된 점수로 자체 콘텐츠 가이드라인 구성 가능

이외에도 **인용필터**(기존 콘텐츠가 길게 복제될 가능성을 낮춤), **시민의식 필터**(정치 선거 및 후보자를 언급하거나 이와 관련된 프롬프트를 감지하고 차단)가 있습니다.

## 목적

- 프롬프트를 대충 써도 그 프롬프트를 바탕으로 프레임워크를 적용한 프롬프트를 생성하여 답변의 질을 높이도록 하는 것
- 다양한 형식으로 인풋을 받을 수 있도록 하는 것

참고: [Gemini LangChain Cheatsheet](https://www.philschmid.de/gemini-langchain-cheatsheet)

## 설정

```python
import os
from dotenv import load_dotenv

load_dotenv()

GOOGLE_API_KEY = os.getenv("GOOGLE_API_KEY")

DEFAULT_MODEL = "gemini-2.0-flash"
CREATIVE_MODEL = "gemini-2.0-flash"
REASONING_MODEL = "gemini-2.0-flash"

# 가드레일 (안전 필터)
SAFETY_FILTERS = {
    "HARM_CATEGORY_HARASSMENT": "OFF",
    "HARM_CATEGORY_HATE_SPEECH": "OFF",
    "HARM_CATEGORY_SEXUALLY_EXPLICIT": "OFF",
    "HARM_CATEGORY_DANGEROUS_CONTENT": "OFF"
}
```

## 1. 프롬프트 프레임워크

### CO-STAR 프레임워크

**CO-STAR**는 Context, Objective, Style, Tone, Audience, Response Format의 약자로, 포괄적인 프롬프트 설계를 위한 프레임워크입니다.

```python
COSTAR_TEMPLATE = """
## Context (맥락)
{context}

## Objective (목표)
{objective}

## Style (스타일)
{style}

## Tone (톤)
{tone}

## Audience (대상)
{audience}

## Response Format (응답 형식)
{response_format}
"""

costar_prompt = COSTAR_TEMPLATE.format(
    context="사용자가 복잡한 논리적 추론 문제를 해결하려고 합니다.",
    objective="단계별 사고 과정을 통해 정확한 답변을 도출하는 것",
    style="논리적이고 체계적인",
    tone="객관적이고 분석적인",
    audience="문제 해결을 원하는 사용자",
    response_format="""
    ## 문제 해결 과정

    ### 1단계: 문제 이해
    [문제의 핵심 요소 파악]

    ### 2단계: 정보 정리
    [주어진 정보와 필요한 정보 정리]

    ### 3단계: 해결 방안 수립
    [적용할 수 있는 공식이나 방법론]

    ### 4단계: 계산 과정
    [단계별 계산 과정]

    ### 5단계: 답변 검증
    [답변의 타당성 확인]

    ### 최종 답변
    [최종 결과]
    """
)
```

### AUTOMAT 프레임워크

**AUTOMAT**는 Act As, User Persona, Targeted Action, Output, Mode, Atypical Case, Topic Whitelist의 약자로, 더 구체적인 역할 정의에 초점을 맞춘 프레임워크입니다.

```python
AUTOMAT_TEMPLATE = """
## Act As (행동)
{act_as}

## User Persona (사용자 페르소나)
{user_persona}

## Targeted Action (목표 행동)
{targeted_action}

## Output (출력)
{output}

## Mode (모드)
{mode}

## Atypical Case (비정상적인 경우)
{atypical_case}

## Topic Whitelist (토픽 화이트리스트)
{topic_whitelist}
"""

automat_prompt = AUTOMAT_TEMPLATE.format(
    act_as="전문적인 코드 분석가",
    user_persona="개발자가 복잡한 코드의 동작을 이해하고 싶어합니다",
    targeted_action="코드의 실행 과정을 단계별로 추적하여 분석",
    output="코드 실행 흐름과 각 단계별 상태 변화",
    mode="코드 라인별 분석 → 변수 상태 추적 → 실행 흐름 시뮬레이션",
    atypical_case="빈 입력이나 잘못된 형식의 코드가 주어진 경우",
    topic_whitelist="프로그래밍, 코드 분석, 알고리즘"
)
```

## LangChain 통합

```python
from config import GOOGLE_API_KEY, DEFAULT_MODEL
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain.prompts import PromptTemplate

# LLM 클라이언트 초기화
llm_client = ChatGoogleGenerativeAI(
    google_api_key=GOOGLE_API_KEY,
    model=DEFAULT_MODEL,
    temperature=0,
    max_tokens=None,
    timeout=None,
    max_retries=3
)

# 프롬프트 템플릿 생성
template = """
    당신은 모바일 UI/UX 에 대한 QA 전문가입니다.

    다음 모바일 화면을 분석하여 {} 관점에서 평가해주세요:

    화면:
    {image}

    예외:

    출력 예시:
    """

prompt = PromptTemplate(
    input_variables=["code", "analysis_type"],
    template=template
)

### 체인(Chain) 활용
from langchain.chains import LLMChain

# 기본 체인 생성
chain = LLMChain(llm=llm_client, prompt=prompt)

# 체인 실행
result = chain.run({
    "analysis_type": "사용성",
    "image": "모바일 화면 이미지"
})
```

## 고급 프롬프트 패턴

### Chain-of-Thought 추론

#### 기본 CoT 예제

```python
def chain_of_thought_example():
    """Chain-of-Thought 추론 예제"""
    print("\n=== Chain-of-Thought 추론 예제 ===")

    # 기본 Chain-of-Thought 예제
    cot_prompt = """
    다음 문제를 단계별로 생각하면서 해결해주세요:

    문제: 한 상자에 사과 5개, 오렌지 3개가 있습니다.
    사과 2개와 오렌지 1개를 먹었다면, 남은 과일은 몇 개인가요?

    단계별 해결 과정:
    1) 처음 과일 개수: 사과 5개 + 오렌지 3개 = 총 8개
    2) 먹은 과일 개수: 사과 2개 + 오렌지 1개 = 총 3개
    3) 남은 과일 개수: 8개 - 3개 = 5개

    따라서 남은 과일은 5개입니다.

    이제 다음 문제를 같은 방식으로 해결해주세요:

    문제: 한 교실에 학생 30명이 있습니다.
    오늘 결석한 학생이 3명이고, 전학온 학생이 2명이라면,
    현재 교실에 있는 학생은 몇 명인가요?

    단계별 해결 과정:
    """

    model = genai.GenerativeModel(
        model_name=DEFAULT_MODEL,
        safety_settings=SAFETY_FILTERS
    )

    response = model.generate_content(cot_prompt)
    print(response.text)
```

#### 고급 CoT + 프레임워크 조합

```python
def advanced_chain_of_thought_example():
    """고급 Chain-of-Thought 예제 (프레임워크 활용)"""

    # CO-STAR 프레임워크와 Chain-of-Thought 조합
    costar_cot_prompt = COSTAR_TEMPLATE.format(
        context="사용자가 복잡한 논리적 추론 문제를 해결하려고 합니다.",
        objective="단계별 사고 과정을 통해 정확한 답변을 도출하는 것",
        style="논리적이고 체계적인",
        tone="객관적이고 분석적인",
        audience="문제 해결을 원하는 사용자",
        response_format="""
        ## 문제 해결 과정

        ### 1단계: 문제 이해
        [문제의 핵심 요소 파악]

        ### 2단계: 정보 정리
        [주어진 정보와 필요한 정보 정리]

        ### 3단계: 해결 방안 수립
        [적용할 수 있는 공식이나 방법론]

        ### 4단계: 계산 과정
        [단계별 계산 과정]

        ### 5단계: 답변 검증
        [답변의 타당성 확인]

        ### 최종 답변
        [최종 결과]
        """
    )

    complex_problem = """
    문제:
    A 회사는 월 매출이 100만원이고, 매월 10%씩 증가합니다.
    B 회사는 월 매출이 80만원이고, 매월 15%씩 증가합니다.
    몇 개월 후에 B 회사의 매출이 A 회사를 추월할까요?
    """

    cot_instruction = """
    다음 단계별로 해결해주세요:
    1. 각 회사의 매출 증가 패턴 분석
    2. 수학적 모델링 (지수 함수)
    3. 방정식 설정 및 해결
    4. 결과 검증
    """

    full_prompt = f"{costar_cot_prompt}\n{cot_instruction}\n\n{complex_problem}"
```

### Few-shot Learning 예제

````python
def few_shot_learning_example():
    """Few-shot Learning 예제"""

    few_shot_prompt = """
    다음은 감정 분석 예제입니다:

    예시 1:
    입력: "오늘 날씨가 정말 좋네요!"
    출력: 긍정적 (95% 신뢰도)
    키워드: 좋네요

    예시 2:
    입력: "교통이 너무 복잡해서 짜증나요."
    출력: 부정적 (90% 신뢰도)
    키워드: 복잡해서, 짜증나요

    예시 3:
    입력: "오늘 점심은 샐러드를 먹었습니다."
    출력: 중립적 (85% 신뢰도)
    키워드: 점심, 샐러드

    이제 다음 텍스트를 같은 형식으로 분석해주세요:

    입력: "새로 산 책이 정말 흥미로워서 밤새 읽었어요!"
    출력:
    """

## 📚 추가로 알아볼 내용

### 1. 고급 프롬프트 패턴들

**Tree of Thoughts (ToT):**
```python
tot_prompt = ChatPromptTemplate.from_template("""
문제: {problem}

다음 단계로 이 문제를 해결하겠습니다:

1단계: 3가지 다른 접근 방법 생각하기
접근법 A: [첫 번째 방법]
접근법 B: [두 번째 방법]
접근법 C: [세 번째 방법]

2단계: 각 접근법의 장단점 평가
접근법 A 평가: [장점/단점]
접근법 B 평가: [장점/단점]
접근법 C 평가: [장점/단점]

3단계: 최적 접근법 선택 및 실행
선택한 방법: [이유와 함께]
실행 과정: [단계별 진행]

최종 답변:
""")
````

**Role-Based Prompting:**

```python
def create_expert_prompt(domain, task, context=""):
    expert_roles = {
        "medical": "당신은 20년 경력의 의사입니다.",
        "legal": "당신은 법무법인의 시니어 변호사입니다.",
        "technical": "당신은 소프트웨어 아키텍트입니다.",
        "creative": "당신은 수상 경력이 있는 크리에이티브 디렉터입니다."
    }

    return ChatPromptTemplate.from_template(f"""
    {expert_roles.get(domain, "당신은 해당 분야의 전문가입니다.")}

    배경: {context}

    과제: {task}

    전문가적 관점에서 다음을 고려하여 답변해주세요:
    1. 업계 모범 사례
    2. 잠재적 위험 요소
    3. 실용적 구현 방안
    4. 대안적 접근법

    답변:
    """)
```

**Persona-Driven Prompting:**

```python
personas = {
    "beginner": {
        "characteristics": "프로그래밍을 처음 배우는 학생",
        "communication_style": "간단하고 이해하기 쉬운 언어 사용",
        "needs": "기본 개념과 실습 예제"
    },
    "expert": {
        "characteristics": "10년 이상의 개발 경험",
        "communication_style": "기술적 용어와 고급 패턴 활용",
        "needs": "최적화와 아키텍처 관점"
    },
    "manager": {
        "characteristics": "기술 팀을 이끄는 관리자",
        "communication_style": "비즈니스 가치와 연결된 설명",
        "needs": "ROI와 팀 효율성 중심"
    }
}

def adapt_prompt_for_persona(base_prompt, persona_type):
    persona = personas.get(persona_type, personas["beginner"])

    return ChatPromptTemplate.from_template(f"""
    대상: {persona["characteristics"]}
    소통 방식: {persona["communication_style"]}
    중점 사항: {persona["needs"]}

    {base_prompt}

    위의 대상에게 맞춰 적절한 수준과 방식으로 답변해주세요.
    """)
```

### 2. 동적 프롬프트 생성

**Context-Aware Prompting:**

```python
class DynamicPromptGenerator:
    def __init__(self):
        self.context_analyzers = {
            "complexity": self._analyze_complexity,
            "domain": self._detect_domain,
            "intent": self._classify_intent
        }

    def generate_adaptive_prompt(self, user_input, context=None):
        # 사용자 입력 분석
        analysis = {}
        for analyzer_name, analyzer_func in self.context_analyzers.items():
            analysis[analyzer_name] = analyzer_func(user_input, context)

        # 분석 결과에 따른 프롬프트 구성
        prompt_components = self._select_prompt_components(analysis)

        return self._build_prompt(prompt_components, user_input)

    def _analyze_complexity(self, text, context):
        complexity_indicators = {
            "simple": ["뭐야", "설명", "간단히"],
            "medium": ["어떻게", "방법", "과정"],
            "complex": ["분석", "비교", "평가", "최적화"]
        }

        for level, indicators in complexity_indicators.items():
            if any(indicator in text for indicator in indicators):
                return level
        return "medium"

    def _build_prompt(self, components, user_input):
        return ChatPromptTemplate.from_template(f"""
        {components['system_instruction']}

        {components['context_setting']}

        사용자 질문: {user_input}

        {components['output_format']}
        """)
```

### 3. 프롬프트 최적화 기법

**Prompt Optimization through Iteration:**

```python
class PromptOptimizer:
    def __init__(self, base_prompt, evaluation_criteria):
        self.base_prompt = base_prompt
        self.criteria = evaluation_criteria
        self.optimization_history = []

    def optimize_prompt(self, test_cases, iterations=5):
        current_prompt = self.base_prompt
        best_score = 0

        for i in range(iterations):
            # 현재 프롬프트로 테스트
            scores = self._evaluate_prompt(current_prompt, test_cases)
            avg_score = sum(scores) / len(scores)

            print(f"Iteration {i+1}: Average Score = {avg_score:.3f}")

            if avg_score > best_score:
                best_score = avg_score
                best_prompt = current_prompt

            # 프롬프트 개선
            current_prompt = self._improve_prompt(current_prompt, scores, test_cases)

            self.optimization_history.append({
                'iteration': i+1,
                'prompt': current_prompt,
                'score': avg_score
            })

        return best_prompt, best_score

    def _improve_prompt(self, prompt, scores, test_cases):
        # 낮은 점수를 받은 테스트 케이스 분석
        low_performers = [(case, score) for case, score in zip(test_cases, scores) if score < 0.7]

        if not low_performers:
            return prompt

        # 개선 제안 생성
        improvement_prompt = ChatPromptTemplate.from_template("""
        현재 프롬프트: {current_prompt}

        문제가 있는 테스트 케이스들:
        {problem_cases}

        이 프롬프트를 개선하여 더 나은 결과를 얻을 수 있도록 수정해주세요.
        개선 포인트를 명확히 하고 수정된 프롬프트를 제공해주세요.
        """)

        # LLM을 사용한 자동 개선
        improved = improvement_prompt.format(
            current_prompt=prompt.template,
            problem_cases=str(low_performers[:3])  # 상위 3개만
        )

        # 실제로는 LLM 호출하여 개선된 프롬프트 받음
        return prompt  # 단순화된 반환
```

### 4. 멀티모달 프롬프트 엔지니어링

**Vision-Language Prompting:**

```python
def create_vision_prompt(task_type, image_context=""):
    vision_prompts = {
        "analysis": """
        이미지를 자세히 분석하고 다음 항목들을 설명해주세요:
        1. 주요 객체와 그 위치
        2. 색상과 조명 상태
        3. 전체적인 분위기나 맥락
        4. 특이사항이나 주목할 점

        {image_context}
        """,
        "comparison": """
        제공된 이미지들을 비교 분석하여 다음을 설명해주세요:
        1. 유사점과 차이점
        2. 각각의 특징
        3. 품질이나 조건 비교
        4. 추천 사항 (해당되는 경우)

        {image_context}
        """,
        "troubleshooting": """
        이미지에서 문제나 이상 징후를 찾아 다음과 같이 분석해주세요:
        1. 발견된 문제점
        2. 가능한 원인
        3. 해결 방안
        4. 예방 조치

        {image_context}
        """
    }

    return vision_prompts.get(task_type, vision_prompts["analysis"]).format(
        image_context=image_context
    )
```

## 🔍 학습하면서 알게 된 점

### 1. 프롬프트 길이와 성능의 관계

```python
def analyze_prompt_length_impact():
    """프롬프트 길이가 성능에 미치는 영향 분석"""
    length_categories = {
        "short": {"range": "50-150 tokens", "pros": "빠름, 비용 효율", "cons": "컨텍스트 부족"},
        "medium": {"range": "150-500 tokens", "pros": "균형잡힌 성능", "cons": "중간 정도 비용"},
        "long": {"range": "500-1000+ tokens", "pros": "풍부한 컨텍스트", "cons": "느림, 고비용"}
    }

    # 실제 경험상 최적점
    optimal_ranges = {
        "qa": "200-400 tokens",
        "analysis": "300-600 tokens",
        "creative": "150-800 tokens",
        "coding": "100-300 tokens"
    }

    return length_categories, optimal_ranges
```

### 2. 문화적 컨텍스트와 언어 모델

```python
cultural_adaptations = {
    "korean": {
        "honorifics": "존댓말 사용이 성능에 영향",
        "context_style": "고맥락 문화 - 암시적 정보 선호",
        "examples": "한국 문화에 맞는 예시 사용 필요"
    },
    "western": {
        "directness": "직접적이고 명확한 지시 선호",
        "context_style": "저맥락 문화 - 명시적 정보 선호",
        "examples": "서구 문화권 예시 효과적"
    }
}

def localize_prompt(prompt, culture="korean"):
    """문화적 맥락에 맞는 프롬프트 조정"""
    adaptations = cultural_adaptations.get(culture, cultural_adaptations["korean"])

    if culture == "korean":
        # 존댓말 추가, 예의 표현 포함
        return f"안녕하세요. {prompt} 정중히 답변 부탁드립니다."
    else:
        return f"Please {prompt.lower()}"
```

### 3. Safety Filter와 프롬프트 설계

```python
def design_safety_aware_prompts():
    """안전 필터를 고려한 프롬프트 설계"""

    safe_patterns = {
        "constructive": ["도움이 되는", "건설적인", "유익한"],
        "educational": ["학습을 위한", "교육적인", "이해를 돕는"],
        "professional": ["전문적인", "업무에 도움이 되는"]
    }

    avoid_patterns = {
        "harmful": ["해로운", "위험한", "부정적인"],
        "biased": ["편견이 있는", "차별적인"],
        "inappropriate": ["부적절한", "선정적인"]
    }

    return {
        "recommendation": "긍정적이고 교육적인 표현 사용",
        "safe_patterns": safe_patterns,
        "avoid_patterns": avoid_patterns
    }
```

### 4. 토큰 효율성 최적화

```python
def optimize_token_usage():
    """토큰 사용량 최적화 전략"""

    strategies = {
        "abbreviations": {
            "before": "Please provide a comprehensive analysis",
            "after": "Analyze:",
            "savings": "60% token reduction"
        },
        "structured_format": {
            "before": "나열하여 설명해주세요",
            "after": "Format: 1. ... 2. ... 3. ...",
            "savings": "30% token reduction"
        },
        "context_pruning": {
            "technique": "불필요한 예시와 설명 제거",
            "savings": "40% token reduction"
        }
    }

    return strategies

# 실제 적용 예시
def create_token_efficient_prompt(verbose_prompt):
    """장황한 프롬프트를 효율적으로 압축"""

    # 핵심 지시사항 추출
    core_instructions = extract_core_instructions(verbose_prompt)

    # 간결한 형태로 재구성
    efficient_prompt = ChatPromptTemplate.from_template("""
    Task: {task}
    Context: {context}
    Output: {output_format}
    """)

    return efficient_prompt
```

### 5. 실시간 프롬프트 적응

```python
class AdaptivePromptSystem:
    def __init__(self):
        self.performance_history = {}
        self.adaptation_rules = self._load_adaptation_rules()

    def adapt_prompt_realtime(self, base_prompt, user_feedback, context):
        """실시간 사용자 피드백 기반 프롬프트 적응"""

        # 사용자 피드백 분석
        feedback_analysis = self._analyze_feedback(user_feedback)

        # 컨텍스트 기반 조정
        context_adjustments = self._get_context_adjustments(context)

        # 프롬프트 수정
        adapted_prompt = self._apply_adaptations(
            base_prompt,
            feedback_analysis,
            context_adjustments
        )

        # 성능 기록 업데이트
        self._update_performance_history(adapted_prompt, user_feedback)

        return adapted_prompt

    def _analyze_feedback(self, feedback):
        """피드백 패턴 분석"""
        patterns = {
            "too_verbose": ["너무 길어", "간단히", "요약"],
            "too_simple": ["더 자세히", "구체적으로", "예시"],
            "off_topic": ["관련 없는", "다른 얘기", "주제 벗어남"]
        }

        for pattern_type, indicators in patterns.items():
            if any(indicator in feedback for indicator in indicators):
                return pattern_type

        return "satisfactory"
```

### 6. 프롬프트 버전 관리

```python
class PromptVersionManager:
    def __init__(self):
        self.versions = {}
        self.performance_metrics = {}

    def create_version(self, prompt_id, prompt_content, metadata=None):
        """프롬프트 버전 생성"""
        version_id = f"{prompt_id}_v{len(self.versions.get(prompt_id, [])) + 1}"

        if prompt_id not in self.versions:
            self.versions[prompt_id] = []

        version_data = {
            "id": version_id,
            "content": prompt_content,
            "created_at": datetime.now(),
            "metadata": metadata or {},
            "status": "active"
        }

        self.versions[prompt_id].append(version_data)
        return version_id

    def rollback_to_version(self, prompt_id, version_number):
        """특정 버전으로 롤백"""
        versions = self.versions.get(prompt_id, [])

        if version_number <= len(versions):
            target_version = versions[version_number - 1]
            target_version["status"] = "active"

            # 다른 버전들 비활성화
            for v in versions:
                if v != target_version:
                    v["status"] = "inactive"

            return target_version

        raise ValueError(f"Version {version_number} not found")

    def compare_versions(self, prompt_id, version_a, version_b, test_cases):
        """버전 간 성능 비교"""
        results = {}

        for version_num in [version_a, version_b]:
            version = self.get_version(prompt_id, version_num)
            scores = self._evaluate_version(version, test_cases)
            results[f"v{version_num}"] = {
                "average_score": sum(scores) / len(scores),
                "scores": scores
            }

        return results
```

이러한 고급 프롬프트 엔지니어링 기법들을 통해 더욱 효과적이고 적응적인 AI 시스템을 구축할 수 있습니다.

```

```
