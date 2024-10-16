---
title: "Airflow 대그에서 파이썬 함수와 데코레이터"
excerpt: "데코레이터 사용하기"

categories:
  - Airflow
tags:
  - [Decorator, Python, Task]

permalink: /Airflow/decorator

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-09-29
---

# 데코레이터란?
데코레이터는 기존 함수를 감싸서 바깥에 추가 기능을 덧붙이는 파이썬 문법. 함수의 인자로 또 다른 함수를 전달하여, 함수의 실행 전후로 특정 작업을 처리하거나 함수를 수정할 수 있는 유용한 방법이다.

예를 들어 여러가지 함수가 있는데, 이 함수들의 실행 전과 후에 로그를 출력해야하는 상황이 있는 경우에 이에 대한 해결방법은 이런 경우들로 나누어 볼 수 있다. 

#### 해결방법
1. 함수 내에 로그 출력하는 명령 달아두기
```
def func():
   print('작업시작')
   ...
   print('작업끝')
```

2. 함수 호출 시작 전과 후에 출력 명령 달기 
```
print('작업시작')
func()
print('작업끝')
```

3. **데코레이터 활용하기**

공통으로 수정해야하는 로직이 있으면 그걸 데코레이터로 정의하고 기존 함수정의에는 데코레이터를 적용한다. 이런 방식으로 로그를 출력하는 데코레이터 함수를 만들고, 실행해야할 함수들에 붙여서 활용할 수 있다. 

```python
def log_decorator(func):
    def wrapper(*args, **kwargs):
        print(f'{func.__name__} 시작')
        result = func(*args, **kwargs)
        print(f'{func.__name__} 끝')
        return result
    return wrapper

@log_decorator
def sample_task():
    print("태스크 실행 중...")

sample_task()
```

출력하면 이런 식으로 로그가 뜬다. 
```
sample_task 시작
태스크 실행 중...
sample_task 끝
```

## Airflow에서의 데코레이터 활용

Airflow에서는 태스크 데코레이터를 지원하여 태스크를 정의할 때 코드 중복을 줄일 수 있다. `@task` 데코레이터를 사용하면 파이썬 함수가 Airflow 태스크로 변환된다. 별도로 `PythonOperator`를 정의할 필요 없이, 함수 위에 `@task` 데코레이터만 붙이면 자동으로 DAG 내 태스크로 등록된다. 

### Airflow 태스크 정의하기

```python
from airflow.decorators import dag, task
from airflow.utils.dates import days_ago

@dag(schedule_interval='@daily', start_date=days_ago(2), catchup=False)
def dag():

    @task
    def task_1():
        print("태스크 1 실행")

    @task
    def task_2():
        print("태스크 2 실행")

    task_1() >> task_2()

dag_instance = dag()
```

### 태스크간 출력을 입력으로 제공하기

한 태스크의 출력을 입력으로 받아서 명령을 수행하는 태스크를 구성할 수 있다. 

```python
from airflow.decorators import dag, task
from airflow.utils.dates import days_ago

@dag(schedule_interval='@daily', start_date=days_ago(2), catchup=False)
def data_pipeline():

    @task
    def extract_data():
        return {"name": "Airflow", "type": "Pipeline"}

    @task
    def process_data(data):
        print(f"데이터 처리 중: {data['name']}은 {data['type']}입니다.")

    data = extract_data()
    process_data(data)

dag_instance = data_pipeline()
```

이 코드에서 `extract_data` 태스크는 데이터를 반환하고, 그 데이터는 `process_data` 태스크로 전달된다. 