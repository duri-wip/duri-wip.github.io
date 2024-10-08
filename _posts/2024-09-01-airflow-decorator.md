---
title: "Airflow 대그에서 파이썬 함수와 데코레이터"
excerpt: "데코레이터 사용하기"

categories:
  - airflow
tags:
  - [Decorator]

permalink: /python/how-to-implement-permutation/

toc: true
toc_sticky: true

date: 2024-09-27
last_modified_at: 2024-09-29
---

데코레이터란?
원래의 함수를 감싸서 바깥에 추가 기능을 덧붙이는 방법.
함수의 인자로 함수를 전달하는 방법을 말한다.
def outer_func(inner_func):
def inner_func(a,b):
c = a+b
return inner_func
사용방법
단순 호출해서 사용하는 함수가 있고, 이 함수의 실행 전과 후에 로그를 출력해야하는 일이 있음
해결방법

1. def func():
   print('작업시작')
   ...
   print('작업끝')
2. print('작업시작')
   func()
   print('작업끝')
3. 데코레이터 활용
   공통으로 수정해야하는 로직이 있으면 그걸 데코레이터로 정의하고 기존 함수정의에는 데코레이터를 적용한다.

그래서!
파이썬 함수를 만들고 @task 오퍼레이터만 붙여주면 함수를 만든걸로 태스크를 만들 필요가 없다

args와 kwargs
함수를 정의할때 optional 성격의 변수가 올 수 있는 경우에는?
일일히 정의하기 어려우므로 args 또는 kwargs를 사용한다

args
args 로 들어온 값은 튜플로 저장되고, 값을 꺼낼때 인덱스를 이용해서 꺼낸다.
튜플이므로 몇번째 인덱스가 어떤 파라미터 값인지 알수는 없다는 단점이 존재한다.

def regist(name, sex, \*args):
print(f'name: {name}\nsex: {sex}')
address = args[0] if len(args) >0 else None
p_num = args[1] if len(args) > 1 else None
print(f'address: {address}\nphone number: {p_num}')

kwargs
딕셔너리 형태로 값을 받아준다

def regist(name, sex, \*\*kwargs):
print(f'name: {name}\nsex: {sex}')
address = kwargs.get('address') or None
p_num = kwargs.get('p_num') or None
print(f'address: {address}\nphone number: {p_num}')

함께 사용하기

args 가 앞에 오고 kwargs가 뒤에오면 상관없다

context

특정 task에 대한 정보를 담은 파이썬 딕셔너리

1. Context 정보에 포함되는 기본 항목들

dag: 현재 실행 중인 DAG 객체
dag_run: 현재 실행 중인 DAGRun 객체
task: 현재 실행 중인 태스크 객체
task_instance: 태스크 인스턴스 객체
execution_date: 태스크 실행 날짜 및 시간
ds: 실행 날짜(포맷: YYYY-MM-DD)
ts: 실행 시간(포맷: YYYY-MM-DDTHH:MM:SS)
prev_execution_date: 이전 실행 날짜 및 시간
next_execution_date: 다음 실행 날짜 및 시간
conf: Airflow의 전체 설정 (configuration)
macros: Airflow에서 사용할 수 있는 매크로 함수
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.utils.dates import days_ago
from datetime import datetime, timedelta

# 기본 DAG 설정

default_args = {
'owner': 'airflow',
'start_date': days_ago(1),
}

# 매크로를 사용하는 함수

def use_macros(\*\*context):
ds = context['ds'] # 현재 실행 날짜 (YYYY-MM-DD 형식)
next_ds = context['next_execution_date'] # 다음 실행 날짜
prev_ds = context['prev_execution_date'] # 이전 실행 날짜

    print(f"Current execution date: {ds}")
    print(f"Next execution date: {next_ds}")
    print(f"Previous execution date: {prev_ds}")

# DAG 생성

with DAG(
'macro_example_dag',
default_args=default_args,
schedule_interval=timedelta(days=1), # 매일 실행
) as dag:

    # PythonOperator에서 매크로 활용
    macro_task = PythonOperator(
        task_id='use_macros_task',
        python_callable=use_macros,
        provide_context=True,  # context 정보를 함수에 전달
    )
