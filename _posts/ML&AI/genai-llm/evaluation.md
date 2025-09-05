---
title: "RAG 시스템 평가 완전 정복"
excerpt: "RAGAS를 활용한 RAG 시스템 성능 평가 방법"
category: genai
tags: 
  - [LangChain, RAG, Evaluation, RAGAS, study]
study_name: "LangChain 마스터"
study_status: "completed"
study_description: "LangChain을 활용한 AI 애플리케이션 개발 학습"
toc: true
toc_sticky: true
date: 2024-03-05
last_modified_at: 2024-03-05
---

# RAG 시스템 평가

RAG 시스템의 평가는 **오프라인 평가**와 **온라인 평가**가 있습니다. 온라인 평가는 사용자 피드백에 의한 피드백 루프를 타는 내용이므로 오프라인 평가에 대해서만 다룹니다.

## 1. RAGAS (RAG Assessment)

RAGAS는 RAG 시스템을 평가하기 위한 프레임워크로, 다양한 평가 메트릭을 제공합니다.

### 검색 관련 메트릭

#### 1. 콘텍스트 정밀도 (Context Precision)
- **정의**: 질문과 기대하는 답변을 고려해 실제 검색 결과 중 유용하다고 LLM이 추론하는 비율
- **측정**: 검색된 문서 중 실제로 답변에 도움이 되는 문서의 비율

#### 2. 콘텍스트 재현율 (Context Recall)  
- **정의**: 기대하는 답변을 여러 문장으로 나눴을 때 실제 검색 결과로 설명할 수 있는 비율
- **측정**: 정답에 포함된 정보 중 검색된 문서에서 찾을 수 있는 정보의 비율

#### 3. 콘텍스트 엔티티 재현율 (Context Entity Recall)
- **정의**: 기대하는 답변에 포함된 엔티티(개체) 중 실제 검색 결과에 포함된 비율
- **측정**: 정답의 핵심 엔티티 중 검색 문서에서 발견되는 엔티티의 비율

### 생성 관련 메트릭

#### 1. 답변 관련성 (Answer Relevancy)
- **정의**: 실제 답변이 질문과 얼마나 관련되는지 측정
- **계산**: 실제 답변에서 LLM으로 추론한 질문과 원래 질문의 임베딩 벡터 코사인 유사도 평균값

#### 2. 충실성 (Faithfulness)
- **정의**: 실제 답변에 포함된 주장 중 실제 검색 결과와 일관된 비율
- **측정**: 생성된 답변이 검색된 문서의 내용과 얼마나 일치하는지 평가

### 검색 + 생성 통합 메트릭

#### 1. 답변 유사성 (Answer Similarity)
- **정의**: 실제 답변과 기대하는 답변의 임베딩 벡터 코사인 유사도
- **측정**: 생성된 답변과 정답 사이의 의미적 유사도

#### 2. 답변 정확성 (Answer Correctness)
- **정의**: 실제 답변과 기대하는 답변의 사실적 유사성과 의미적 유사성의 가중 평균
- **측정**: 정확성과 완전성을 모두 고려한 종합적 평가 지표

이러한 메트릭들을 통해 RAG 시스템의 검색 품질과 생성 품질을 종합적으로 평가할 수 있습니다.

## 📚 추가로 알아볼 내용

### 1. 고급 평가 메트릭

**Custom Evaluation Metrics:**
```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

# 도메인별 맞춤 평가
class CustomRAGEvaluator:
    def __init__(self, domain="general"):
        self.domain = domain
        self.evaluator_llm = ChatOpenAI(temperature=0)
    
    def evaluate_domain_relevance(self, question, answer, context):
        prompt = ChatPromptTemplate.from_template("""
        도메인: {domain}
        질문: {question}
        답변: {answer}
        컨텍스트: {context}
        
        이 답변이 {domain} 도메인에서 얼마나 적절한지 1-10점으로 평가하고 이유를 설명하세요.
        """)
        
        evaluation = self.evaluator_llm.invoke(prompt.format(
            domain=self.domain,
            question=question,
            answer=answer,
            context=context
        ))
        
        return self._extract_score(evaluation.content)
```

**Retrieval Quality Metrics:**
```python
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

class RetrievalQualityEvaluator:
    def __init__(self, embeddings_model):
        self.embeddings = embeddings_model
    
    def calculate_mrr(self, queries, retrieved_docs, relevant_docs):
        """Mean Reciprocal Rank 계산"""
        mrr_scores = []
        
        for query, retrieved, relevant in zip(queries, retrieved_docs, relevant_docs):
            for rank, doc in enumerate(retrieved, 1):
                if doc.metadata.get('id') in relevant:
                    mrr_scores.append(1.0 / rank)
                    break
            else:
                mrr_scores.append(0.0)
        
        return np.mean(mrr_scores)
    
    def calculate_ndcg(self, queries, retrieved_docs, relevance_scores, k=10):
        """Normalized Discounted Cumulative Gain 계산"""
        ndcg_scores = []
        
        for query_idx, retrieved in enumerate(retrieved_docs):
            dcg = 0
            idcg = 0
            
            # 실제 순서의 DCG 계산
            for i, doc in enumerate(retrieved[:k]):
                relevance = relevance_scores[query_idx].get(doc.metadata['id'], 0)
                dcg += (2**relevance - 1) / np.log2(i + 2)
            
            # 이상적 순서의 IDCG 계산
            ideal_relevances = sorted(relevance_scores[query_idx].values(), reverse=True)[:k]
            for i, rel in enumerate(ideal_relevances):
                idcg += (2**rel - 1) / np.log2(i + 2)
            
            ndcg_scores.append(dcg / idcg if idcg > 0 else 0)
        
        return np.mean(ndcg_scores)
```

### 2. 자동화된 평가 파이프라인

**Continuous Evaluation:**
```python
import schedule
import time
from datetime import datetime

class ContinuousRAGEvaluator:
    def __init__(self, rag_chain, test_dataset, evaluators):
        self.rag_chain = rag_chain
        self.test_dataset = test_dataset
        self.evaluators = evaluators
        self.results_history = []
    
    def run_evaluation(self):
        """정기 평가 실행"""
        print(f"Starting evaluation at {datetime.now()}")
        
        results = {
            'timestamp': datetime.now(),
            'metrics': {}
        }
        
        # 각 평가자별로 메트릭 계산
        for evaluator_name, evaluator in self.evaluators.items():
            try:
                metric_score = evaluator.evaluate(self.rag_chain, self.test_dataset)
                results['metrics'][evaluator_name] = metric_score
                print(f"{evaluator_name}: {metric_score:.3f}")
            except Exception as e:
                print(f"Error in {evaluator_name}: {e}")
                results['metrics'][evaluator_name] = None
        
        self.results_history.append(results)
        self._check_performance_degradation()
    
    def _check_performance_degradation(self, threshold=0.1):
        """성능 저하 감지"""
        if len(self.results_history) < 2:
            return
        
        current = self.results_history[-1]['metrics']
        previous = self.results_history[-2]['metrics']
        
        for metric_name, current_score in current.items():
            if current_score is None or previous.get(metric_name) is None:
                continue
            
            degradation = previous[metric_name] - current_score
            if degradation > threshold:
                self._alert_performance_issue(metric_name, degradation)
    
    def schedule_evaluations(self, interval_hours=24):
        """정기 평가 스케줄링"""
        schedule.every(interval_hours).hours.do(self.run_evaluation)
        
        while True:
            schedule.run_pending()
            time.sleep(3600)  # 1시간마다 체크
```

### 3. 벤치마크 데이터셋 활용

**Popular RAG Benchmarks:**
```python
# MS MARCO 데이터셋 활용
from datasets import load_dataset

class BenchmarkEvaluator:
    def __init__(self):
        self.benchmarks = {
            'ms_marco': self._load_ms_marco(),
            'natural_questions': self._load_nq(),
            'hotpot_qa': self._load_hotpot()
        }
    
    def _load_ms_marco(self):
        dataset = load_dataset("ms_marco", "v1.1", split="validation")
        return [{
            'question': item['query'],
            'expected_answer': item['answers'][0] if item['answers'] else '',
            'context': item['passages']
        } for item in dataset.select(range(100))]
    
    def evaluate_on_benchmark(self, rag_chain, benchmark_name):
        """벤치마크 데이터셋으로 평가"""
        if benchmark_name not in self.benchmarks:
            raise ValueError(f"Unknown benchmark: {benchmark_name}")
        
        benchmark_data = self.benchmarks[benchmark_name]
        scores = []
        
        for item in benchmark_data:
            try:
                answer = rag_chain.invoke({"question": item['question']})
                score = self._calculate_similarity(answer, item['expected_answer'])
                scores.append(score)
            except Exception as e:
                print(f"Error processing question: {e}")
                scores.append(0.0)
        
        return {
            'benchmark': benchmark_name,
            'average_score': np.mean(scores),
            'total_questions': len(benchmark_data),
            'successful_answers': len([s for s in scores if s > 0])
        }
```

## 🔍 학습하면서 알게 된 점

### 1. RAGAS 메트릭의 실제 해석

**Context Precision의 함정:**
```python
# Context Precision이 높다고 항상 좋은 것은 아님
def analyze_context_precision():
    scenarios = {
        "높은_정밀도_좋은_경우": {
            "description": "관련성 높은 문서만 검색",
            "precision": 0.9,
            "context_quality": "excellent"
        },
        "높은_정밀도_나쁜_경우": {
            "description": "검색된 문서 수가 너무 적음",
            "precision": 0.9,
            "context_quality": "insufficient"
        }
    }
    return scenarios
```

**실제 경험:**
- Context Precision 0.8+ : 우수
- Context Recall 0.7+ : 양호 
- Answer Relevancy 0.8+ : 우수
- Faithfulness 0.9+ : 필수 (hallucination 방지)

### 2. 메트릭 간 트레이드오프 관계

```python
tradeoffs = {
    "정밀도_vs_재현율": {
        "high_precision": "관련 문서만 검색, 정보 부족 위험",
        "high_recall": "모든 관련 정보 포함, 노이즈 증가"
    },
    "충실성_vs_관련성": {
        "high_faithfulness": "검색 문서에만 의존, 창의적 답변 제한",
        "high_relevancy": "질문에 최적화, 사실 왜곡 위험"
    }
}
```

### 3. 도메인별 평가 기준 차이

**의료/법률 도메인:**
- Faithfulness > 0.95 (사실 정확성 중요)
- Context Precision > 0.9 (전문성 요구)

**창작/마케팅 도메인:**
- Answer Relevancy > 0.85 (사용자 만족도 중요)
- Faithfulness > 0.7 (창의성 허용)

### 4. 평가 자동화의 실제 구현

**A/B 테스트 통합:**
```python
class RAGABTestEvaluator:
    def __init__(self, variant_a, variant_b):
        self.variant_a = variant_a
        self.variant_b = variant_b
        self.results = {'a': [], 'b': []}
    
    def run_split_test(self, questions, split_ratio=0.5):
        import random
        
        for question in questions:
            variant = 'a' if random.random() < split_ratio else 'b'
            chain = self.variant_a if variant == 'a' else self.variant_b
            
            try:
                answer = chain.invoke({"question": question})
                # 사용자 피드백 수집 (실제 환경에서)
                feedback_score = self._collect_user_feedback(question, answer)
                self.results[variant].append(feedback_score)
            except Exception as e:
                print(f"Error in variant {variant}: {e}")
    
    def analyze_results(self):
        from scipy import stats
        
        scores_a = self.results['a']
        scores_b = self.results['b']
        
        # 통계적 유의성 검정
        t_stat, p_value = stats.ttest_ind(scores_a, scores_b)
        
        return {
            'variant_a_mean': np.mean(scores_a),
            'variant_b_mean': np.mean(scores_b),
            'p_value': p_value,
            'significant': p_value < 0.05
        }
```

### 5. 실전 평가 시스템 구축 경험

**모니터링 대시보드:**
```python
import streamlit as st
import plotly.express as px

def create_rag_dashboard(evaluation_results):
    st.title("RAG System Performance Dashboard")
    
    # 메트릭 트렌드
    metrics_df = pd.DataFrame(evaluation_results)
    fig = px.line(metrics_df, x='timestamp', y=['faithfulness', 'answer_relevancy'], 
                  title='Performance Trends Over Time')
    st.plotly_chart(fig)
    
    # 성능 분포
    col1, col2 = st.columns(2)
    with col1:
        st.metric("Average Faithfulness", f"{metrics_df['faithfulness'].mean():.3f}")
    with col2:
        st.metric("Average Answer Relevancy", f"{metrics_df['answer_relevancy'].mean():.3f}")
```

**실시간 성능 알림:**
```python
def setup_performance_alerts():
    thresholds = {
        'faithfulness': 0.8,
        'answer_relevancy': 0.75,
        'context_precision': 0.7
    }
    
    # Slack, 이메일 등으로 알림
    def alert_performance_drop(metric, current_score, threshold):
        message = f"⚠️ {metric} dropped to {current_score:.3f} (threshold: {threshold})"
        send_slack_notification(message)
```

이러한 포괄적인 평가 시스템을 통해 RAG 애플리케이션의 품질을 지속적으로 모니터링하고 개선할 수 있습니다.