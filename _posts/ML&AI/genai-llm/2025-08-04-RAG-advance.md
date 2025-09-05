---
layout: post
title: "RAG 고급 기법 완전 정복"
excerpt: "HyDE, Multi-Query, RAG-Fusion, ReRank, Hybrid Search 등 고급 RAG 기법"
date: 2025-08-04
categories: [ML&AI]
subcategory: genai-llm

tags: [genai-llm, langchain, rag, hyde, rerank, hybrid-search]
---

# RAG에 관한 다양한 고급 기법

## 1. 검색 쿼리 관련 기법

### HyDE (Hypothetical Document Embeddings)

- **개념**: 질의에 대한 가상의 답변(컨텍스트 없이) 생성 → 이 답변과 유사한 문서 검색 쿼리 생성
- **목적**: 검색 쿼리의 품질 향상

#### 1. 직접 구현

```python
hypothetical_prompt = ChatPromptTemplate.from_template("""
    다음 질문에 한 문장으로 답하세요.
    질문 : {question}
    """)

hypothetical_chain = hypothetical_prompt | model | StrOutputParser()

hyde_rag_chain = {
    "question": RunnablePassthrough(),
    "context": hypothetical_chain | retriever,
} | prompt | model | StrOutputParser()

hyde_rag_chain.invoke("langchain의 개요를 알려줘")
```

#### 2. LangChain 모듈 활용

- **HypotheticalDocumentEmbedder** 사용

### Multi-Query Generation

- **개념**: 복수 검색 쿼리 생성으로 다양한 관점에서 검색

#### 1. 직접 구현

```python
from pydantic import BaseModel, Field

class QueryGenerate(BaseModel):
    queries: list[str] = Field(..., description="검색 쿼리 목록")

query_generate_prompt = ChatPromptTemplate.from_template("""\
    질문에 대해 벡터 데이터베이스에서 관련 문서를 검색하기 위한 3개의 서로 다른 검색 쿼리를 생성하세요.
    거리 기반 유사성 검색의 한계를 극복하기 위해 사용자의 질문에 대해 여러 관점을 제공하는 것이 목표입니다.

    질문 : {question}
    """)

query_generate_chain = (
    query_generate_prompt
    | model.with_structured_output(QueryGenerate)
    | (lambda x: x.queries)
)

multi_query_chain = {
    "question": RunnablePassthrough(),
    "context": query_generate_chain | retriever.map(),
} | prompt | model | StrOutputParser()

multi_query_chain.invoke("langchain의 개요를 알려줘")
```

**Note**: `map`은 LangChain 러너블이 제공하는 메서드 중 하나로, 원래 러너블의 인자와 반환값을 리스트화하는 메서드입니다.

#### 2. LangChain 모듈 활용

- **MultiQueryRetriever** 사용

## 2. 검색 후 기법: Reciprocal Rank Fusion (RRF)

여러 검색 결과의 순위를 융합해 정렬하는 기법입니다.

### RAG-Fusion

- **개념**: 여러 검색 쿼리를 생성하고 이를 결과를 정렬하기

```python
from langchain_core.documents import Document

def reciprocal_rank_fusion(
        retriever_outputs: list[list[Document]],
        k: int = 60,
) -> list[str]:
    # 각 문서의 콘텐츠(문자열)와 그 점수의 매핑을 저장하는 딕셔너리 준비
    content_score_mapping = {}

    # 검색 쿼리마다 반복
    for docs in retriever_outputs:
        # 검색 결과의 문서마다 반복
        for rank, doc in enumerate(docs):
            # 문서의 콘텐츠와 점수를 딕셔너리에 추가
            content = doc.page_content

            if content not in content_score_mapping:
                content_score_mapping[content] = 0

            # 점수 업데이트
            content_score_mapping[content] += 1 / (rank + k)

    # 점수에 따라 정렬된 문서 목록 반환
    sorted_contents = sorted(
        content_score_mapping.items(),
        key=lambda x: x[1],
        reverse=True,
    )
    return [content for content, _ in sorted_contents]

# 체인 구현
rag_fusion_chain = {
    "question": RunnablePassthrough(),
    "context": query_generate_chain | retriever.map() | reciprocal_rank_fusion,
} | prompt | model | StrOutputParser()

rag_fusion_chain.invoke("langchain의 개요를 알려줘")
```

### ReRank

- **개념**: 어떤 검색 쿼리에 대한 여러 검색 결과를 정렬하기
- **장점**: 리랭크용 머신러닝 모델을 사용해서 검색 결과를 정렬. 임베딩 벡터의 유사도 검색보다 계산비용이 높은 대신 랭킹 정확도가 높음

#### 1. Cohere 모델 사용

```python
from typing import Any
from langchain_cohere import CohereRerank
from langchain_core.documents import Document

def rerank(inp: dict[str, Any], top_n: int = 3) -> list[Document]:
    question = inp["question"]
    documents = inp["documents"]

    cohere_reranker = CohereRerank(model="rerank-multilingual-v3.0", top_n=top_n)

    return cohere_reranker.compress_documents(documents=documents, query=question)

rerank_chain = (
    {
        "question": RunnablePassthrough(),
        "context": retriever,
    }
    | RunnablePassthrough.assign(context=rerank)
    | prompt
    | model
    | StrOutputParser()
)

rerank_chain.invoke("langchain의 개요를 알려줘")
```

**Note**: RunnablePassthrough assign을 통해 {question, context}의 출력이 rerank함수의 인자 inp에 전달됩니다.

#### 2. LangChain 모듈 활용

- **ContextualCompressionRetriever** 사용

## 3. 복수의 리트리버를 사용하는 기법

### 라우트에 따라 다른 리트리버 사용하기

```python
from langchain_community.retrievers import TavilySearchAPIRetriever

# LangSmith에서 더 잘 확인하기 위한 설정
langchain_document_retriever = retriever.with_config({"run_name": "langchain_document_retriever"})
web_retriever = TavilySearchAPIRetriever(k=3).with_config({"run_name": "web_retriever"})

# 사용자 입력을 기반으로 LLM이 리트리버를 선택하는 체인
from enum import Enum

class Route(str, Enum):
    langchain_document = "langchain_document"
    web = "web"

class RouteOutput(BaseModel):
    route: Route

route_prompt = ChatPromptTemplate.from_template("""
    다음 질문에 대해 적절한 리트리버를 선택하세요.
    질문 : {question}
    """)

route_chain = (
    route_prompt
    | model.with_structured_output(RouteOutput)
    | (lambda x: x.route)
)

# 라우팅 결과를 고려해 검색하는 함수 구현
def routed_retriever(inp: dict[str, Any]) -> list[Document]:
    question = inp["question"]
    route = inp["route"]

    if route == Route.langchain_document:
        return langchain_document_retriever.invoke(question)
    elif route == Route.web:
        return web_retriever.invoke(question)

    raise ValueError(f"Invalid route: {route}")

# 라우트 체인과 라우트 결과를 고려해 검색하는 체인
routed_retriever_chain = (
    {
        "question": RunnablePassthrough(),
        "route": route_chain,
    }
    | RunnablePassthrough.assign(context=routed_retriever)
    | prompt
    | model
    | StrOutputParser()
)

routed_retriever_chain.invoke("langchain의 개요를 알려줘")
```

### 하이브리드 검색

RAG의 구현에서는 임베딩 벡터의 유사도 검색을 수행하는데, 범용적으로 만들어진 임베딩 모델에서는 학습데이터에 포함되지 않은 고유명사나 전문용어의 유사도 계산이 어렵습니다.

자연어 처리의 고전적인 기법으로 단어의 등장 빈도를 기반으로 텍스트의 유사도를 계산하는 **TF-IDF**, **BM25**(TF-IDF의 개선판) 등이 있습니다.

이런 알고리즘을 사용하면 공통 단어의 빈도가 높으면 벡터 유사도가 높아지기 때문에, 범용 임베딩 모델의 단점을 보완하기 쉽습니다.

```
        「 임베딩 모델에 의한 벡터화                  -- 밀집벡터(대부분 요소가 0이 아님)
TEXT ---
         ㄴ TF-IDF 또는 BM25를 사용한 벡터화         -- 희소벡터(0이 많음)
```

벡터 클라우드 서비스인 **PINECONE**에서도 밀집 벡터와 희소 벡터 검색을 조합한 하이브리드 검색을 제공하고 있습니다.

#### 1. 하이브리드 검색 구현: BM25 사용

```bash
pip install rank-bm25==0.2.2
```

```python
from langchain_community.retrievers import BM25Retriever

chroma_retriever = retriever.with_config({"run_name": "chroma_retriever"})

bm25_retriever = BM25Retriever.from_documents(documents).with_config({"run_name": "bm25_retriever"})

# 두 리트리버를 조합해 하이브리드 검색 체인 구현
hybrid_retriever_chain = (
    RunnableParallel({
        "chroma_documents": chroma_retriever,
        "bm25_documents": bm25_retriever,
    })
    | (lambda x: [x["chroma_documents"], x["bm25_documents"]])
    | reciprocal_rank_fusion
)

# 전체 RAG 체인 구현
rag_chain = ({
    "question": RunnablePassthrough(),
    "context": hybrid_retriever_chain,
} | prompt | model | StrOutputParser())

rag_chain.invoke("langchain의 개요를 알려줘")
```

#### 2. LangChain 모듈 활용

- **EnsembleRetriever** 사용

## 📚 추가로 알아볼 내용

### 1. 최신 RAG 기법들

**Contextual Retrieval (컨텍스트 보강 검색):**

```python
from langchain_core.prompts import ChatPromptTemplate

# 각 청크에 컨텍스트 정보 추가
context_prompt = ChatPromptTemplate.from_template("""
다음 문서 청크에 대해 전체 문서의 맥락을 고려하여
간단한 배경 설명을 추가해주세요.

전체 문서 주제: {document_topic}
청크 내용: {chunk_content}

배경 설명이 포함된 청크:
""")

def add_context_to_chunks(chunks, document_topic):
    contextual_chunks = []
    for chunk in chunks:
        context_added = context_prompt.format(
            document_topic=document_topic,
            chunk_content=chunk.page_content
        )
        # LLM으로 컨텍스트 추가된 청크 생성
        enhanced_chunk = model.invoke(context_added)
        contextual_chunks.append(enhanced_chunk)
    return contextual_chunks
```

**Parent Document Retriever (상위 문서 검색):**

```python
from langchain.retrievers import ParentDocumentRetriever
from langchain.storage import InMemoryStore

# 작은 청크로 검색하지만 큰 컨텍스트 반환
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)

store = InMemoryStore()

retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=store,
    child_splitter=child_splitter,
    parent_splitter=parent_splitter,
)
```

**Multi-Vector Retriever (다중 벡터 검색):**

```python
from langchain.retrievers.multi_vector import MultiVectorRetriever

# 하나의 문서에 대해 여러 표현 방식 생성
def create_multi_representations(doc):
    return {
        "summary": summarize_chain.invoke(doc),
        "keywords": keyword_chain.invoke(doc),
        "questions": question_gen_chain.invoke(doc),
        "original": doc.page_content
    }

multi_retriever = MultiVectorRetriever(
    vectorstore=vectorstore,
    docstore=store,
    id_key="doc_id"
)
```

### 2. 고급 평가 및 최적화

**A/B Testing for RAG:**

```python
import random
from dataclasses import dataclass
from typing import List

@dataclass
class RAGVariant:
    name: str
    retriever: any
    prompt: str

class RAGExperiment:
    def __init__(self, variants: List[RAGVariant]):
        self.variants = variants
        self.results = []

    def run_experiment(self, test_questions, sample_ratio=0.5):
        for question in test_questions:
            # 랜덤하게 variant 선택
            variant = random.choice(self.variants)

            if random.random() < sample_ratio:
                result = self.evaluate_variant(variant, question)
                self.results.append({
                    "variant": variant.name,
                    "question": question,
                    "result": result
                })

    def evaluate_variant(self, variant, question):
        # 실제 평가 로직
        context = variant.retriever.get_relevant_documents(question)
        answer = variant.prompt.format(context=context, question=question)
        return {"answer": answer, "context_count": len(context)}
```

**Dynamic Retrieval Optimization:**

```python
class AdaptiveRetriever:
    def __init__(self, retrievers, performance_tracker):
        self.retrievers = retrievers
        self.tracker = performance_tracker
        self.weights = {name: 1.0 for name in retrievers.keys()}

    def get_relevant_documents(self, query):
        # 성능 기반으로 retriever 선택
        best_retriever = self.select_best_retriever(query)
        docs = self.retrievers[best_retriever].get_relevant_documents(query)

        # 결과 품질 피드백 수집
        self.tracker.record_retrieval(best_retriever, query, docs)
        return docs

    def select_best_retriever(self, query):
        # 쿼리 유형 분석해서 최적 retriever 선택
        query_type = self.classify_query(query)
        return max(self.retrievers.keys(),
                  key=lambda r: self.weights[r] * self.get_type_affinity(r, query_type))
```

### 3. 고급 프롬프트 엔지니어링

**Self-RAG (자기 반성 RAG):**

```python
self_rag_prompt = ChatPromptTemplate.from_template("""
질문: {question}
검색된 정보: {context}

1단계: 검색된 정보가 질문에 관련이 있는지 평가하세요.
관련성 점수: [1-10]

2단계: 관련성이 7점 이상이면 답변을 생성하고,
그렇지 않으면 "추가 정보가 필요합니다"라고 응답하세요.

3단계: 생성한 답변이 검색된 정보에 기반하고 있는지 확인하세요.
근거: [검색 정보에서 인용]

최종 답변:
""")

def self_rag_chain(question):
    # 1차 검색
    initial_docs = retriever.get_relevant_documents(question)

    # 자기 평가
    evaluation = self_rag_prompt.invoke({
        "question": question,
        "context": format_docs(initial_docs)
    })

    # 추가 검색 필요시
    if "추가 정보가 필요합니다" in evaluation:
        # 쿼리 확장 또는 다른 검색 전략 시도
        expanded_query = expand_query(question)
        additional_docs = retriever.get_relevant_documents(expanded_query)
        # 재시도
        return self_rag_prompt.invoke({
            "question": question,
            "context": format_docs(initial_docs + additional_docs)
        })

    return evaluation
```

### 4. 고급 검색 기법

**Iterative Retrieval (반복 검색):**

```python
def iterative_retrieval(question, max_iterations=3):
    all_docs = []
    current_question = question

    for i in range(max_iterations):
        # 현재 질문으로 검색
        docs = retriever.get_relevant_documents(current_question)
        all_docs.extend(docs)

        # 검색 결과로 후속 질문 생성
        follow_up_prompt = ChatPromptTemplate.from_template("""
        원본 질문: {original_question}
        검색 결과: {search_results}

        이 검색 결과를 바탕으로, 원본 질문에 더 완전히 답하기 위해
        추가로 필요한 정보를 찾기 위한 검색 쿼리를 생성하세요.

        추가 검색 쿼리:
        """)

        follow_up = follow_up_prompt.invoke({
            "original_question": question,
            "search_results": format_docs(docs)
        })

        current_question = follow_up

        # 충분한 정보 수집 확인
        if self.is_sufficient_information(all_docs, question):
            break

    return all_docs
```

## 🔍 학습하면서 알게 된 점

### 1. HyDE의 실제 효과와 한계

**효과가 좋은 경우:**

- 전문 도메인 질문 (의료, 법률, 기술)
- 추상적 개념에 대한 질문
- 사용자 질문이 모호한 경우

**효과가 제한적인 경우:**

- 사실 확인 질문
- 매우 구체적인 데이터 검색
- 실시간 정보가 필요한 경우

```python
def should_use_hyde(question):
    """HyDE 사용 여부 결정"""
    indicators = {
        "추상적": ["원리", "개념", "이유", "방법"],
        "전문적": ["기술", "의료", "법률", "학술"],
        "불명확": ["?", "어떻게", "왜", "설명"]
    }

    for category, keywords in indicators.items():
        if any(keyword in question for keyword in keywords):
            return True, category

    return False, "구체적_사실_질문"
```

### 2. 멀티 쿼리의 최적 개수

실험 결과, **3-5개의 쿼리**가 최적:

```python
def optimal_multi_query_count(question_complexity):
    if question_complexity == "simple":
        return 2  # 원본 + 1개 변형
    elif question_complexity == "medium":
        return 3  # 원본 + 2개 변형
    else:  # complex
        return 5  # 원본 + 4개 변형
```

### 3. RRF 파라미터 k의 실제 영향

```python
def analyze_rrf_k_impact():
    """RRF k 값이 결과에 미치는 영향 분석"""
    k_values = [1, 10, 30, 60, 100]

    for k in k_values:
        print(f"\nk={k}:")
        print(f"상위권 문서 가중치: {1/(1+k):.3f}")
        print(f"하위권 문서 가중치 (rank=10): {1/(10+k):.3f}")
```

**결과:**

- k=1: 순위 차이 극대화 (상위 문서 압도적 우위)
- k=60: 균형잡힌 가중치 (기본값)
- k=100: 순위 차이 최소화 (모든 문서 비슷한 가중치)

### 4. 하이브리드 검색의 최적 비율

```python
def find_optimal_hybrid_ratio(dense_retriever, sparse_retriever, test_queries):
    """밀집/희소 검색의 최적 결합 비율 탐색"""
    ratios = [0.3, 0.5, 0.7]  # 밀집 벡터 비율

    best_ratio = 0.5
    best_score = 0

    for ratio in ratios:
        # 각 비율로 하이브리드 검색 수행
        hybrid_retriever = EnsembleRetriever(
            retrievers=[dense_retriever, sparse_retriever],
            weights=[ratio, 1-ratio]
        )

        # 평가 점수 계산
        score = evaluate_retriever(hybrid_retriever, test_queries)
        if score > best_score:
            best_score = score
            best_ratio = ratio

    return best_ratio
```

**일반적인 경험값:**

- 기술 문서: 밀집 70% + 희소 30%
- 뉴스/일반: 밀집 50% + 희소 50%
- FAQ: 밀집 40% + 희소 60%

### 5. ReRank 모델의 선택 기준

```python
rerank_models = {
    "cohere-rerank-multilingual-v3.0": {
        "언어": "다국어 (한국어 포함)",
        "성능": "높음",
        "비용": "높음",
        "용도": "프로덕션"
    },
    "bge-reranker-large": {
        "언어": "영어 중심",
        "성능": "중간",
        "비용": "무료",
        "용도": "실험/프로토타입"
    },
    "sentence-transformers/ms-marco-MiniLM-L-12-v2": {
        "언어": "영어",
        "성능": "중간",
        "비용": "무료",
        "용도": "빠른 프로토타입"
    }
}
```

### 6. 실제 프로덕션에서의 성능 최적화 경험

**청킹 전략 최적화:**

```python
def adaptive_chunking(document, content_type):
    """문서 유형에 따른 적응적 청킹"""
    strategies = {
        "code": {"chunk_size": 500, "separators": ["\n\nclass ", "\n\ndef ", "\n\n"]},
        "legal": {"chunk_size": 1200, "separators": ["\n\n", "\\n\\d+\\.", ". "]},
        "technical": {"chunk_size": 800, "separators": ["\n\n", "\n### ", "\n- "]}
    }

    strategy = strategies.get(content_type, {"chunk_size": 1000, "separators": ["\n\n", "\n", ". "]})

    splitter = RecursiveCharacterTextSplitter(
        chunk_size=strategy["chunk_size"],
        chunk_overlap=int(strategy["chunk_size"] * 0.1),  # 10% 겹침
        separators=strategy["separators"]
    )

    return splitter.split_documents([document])
```

**벡터 DB 성능 최적화:**

```python
def optimize_vector_db():
    """벡터 DB 성능 최적화 팁"""
    tips = {
        "인덱스_구성": "HNSW > IVF > Flat (성능 vs 정확도)",
        "배치_크기": "임베딩시 100-1000개 단위로 배치 처리",
        "메모리_관리": "주기적 인덱스 압축 및 정리",
        "쿼리_최적화": "top_k는 20 이하로 제한"
    }
    return tips
```

이러한 고급 기법들과 실전 경험을 통해 더욱 효과적인 RAG 시스템을 구축할 수 있습니다.
