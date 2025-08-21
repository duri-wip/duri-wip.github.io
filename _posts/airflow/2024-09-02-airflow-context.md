---
layout: post
title: "Airflow에서 context와 args, kwargs의 활용"
excerpt: "Context 활용법"

category: airflow
tags:
  - [Decorator, Python, Context]

permalink: /airflow/context

toc: true
toc_sticky: true

date: 2024-09-02
last_modified_at: 2024-09-29
---

# Airflow에서 Context와 args, kwargs의 활용

Airflow에서 `args`,`kwargs`를 사용하면 파이썬 함수의 인자를 유연하게 다룰 수 있다. 이 개념과 함께 airflow의 context활용 방법을 함께 설명한다.

## args와 kwargs란?

### `args` 사용법

`*args`는 여러 개의 값을 튜플 형태로 받는다. 함수에서 인자의 개수가 미리 정해져 있지 않을 때 유용하다. 인덱스를 사용해 값을 꺼낼 수 있지만, 어떤 값이 어떤 인덱스에 위치하는지 명확하지 않은 단점이 있다.

```python
def regist(name, sex, *args):
    print(f'name: {name}\nsex: {sex}')
    address = args[0] if len(args) > 0 else None
    p_num = args[1] if len(args) > 1 else None
    print(f'address: {address}\nphone number: {p_num}')

# 함수 호출 예시
regist('Alice', 'Female', 'Seoul', '010-1234-5678')
```

위 예시에서 `name`과 `sex`는 필수 인자로 취급되고, `*args`는 추가적인 인자를 받는다. name, sex는 필수적으로 받아오는데 그 뒤의 인자들은 임의적으로 존재할 수 있는 경우에 인자가 2개 있으면 2개를 다 받아오고, 1개만 있으면 1개만 더 맏아오고, 없으면 없는대로 취급하는 유연한 처리가 가능하다.

### `kwargs` 사용법

`**kwargs`는 키-값 쌍의 형태로 여러 인자를 받는다. 딕셔너리처럼 값을 다룰 수 있어, 인자 이름을 지정해 값을 꺼낼 수 있는 장점이 있다.

```python
def regist(name, sex, **kwargs):
    print(f'name: {name}\nsex: {sex}')
    address = kwargs.get('address', None)
    p_num = kwargs.get('p_num', None)
    print(f'address: {address}\nphone number: {p_num}')

# 함수 호출 예시
regist('Alice', 'Female', address='Seoul', p_num='010-1234-5678')
```

`kwargs.get()`을 사용하면 키가 존재하지 않을 때 기본값을 설정할 수 있습니다.

### `args`와 `kwargs` 함께 사용하기

`args`와 `kwargs`는 함께 사용할 수 있다. 이때, `*args`가 먼저, `**kwargs`는 그 뒤에 오도록 사용한다.

```python
def sample_func(a, b, *args, **kwargs):
    print(f"a: {a}, b: {b}")
    print(f"args: {args}")
    print(f"kwargs: {kwargs}")

sample_func(1, 2, 3, 4, key1='value1', key2='value2')
```

## Airflow에서의 Context 활용

Airflow의 컨텍스트는 특정 태스크에 대한 실행 정보를 담고 있는 딕셔너리다. 이를 통해 현재 실행 중인 DAG, 태스크, 실행 시간 등 다양한 정보를 파라미터로 전달받을 수 있다. `@task` 데코레이터나 `PythonOperator`를 사용할 때, `context`를 함수에 전달하여 실행 흐름에 대한 정보를 활용할 수 있다.

### Context에 포함된 기본 항목

- `dag`: 현재 실행 중인 DAG 객체
- `dag_run`: 현재 실행 중인 DAGRun 객체
- `task`: 현재 실행 중인 태스크 객체
- `task_instance`: 태스크 인스턴스 객체
- `execution_date`: 태스크 실행 날짜 및 시간
- `ds`: 실행 날짜 (YYYY-MM-DD 형식)
- `ts`: 실행 시간 (YYYY-MM-DDTHH:MM:SS 형식)
- `prev_execution_date`: 이전 실행 날짜 및 시간
- `next_execution_date`: 다음 실행 날짜 및 시간
- `conf`: Airflow 전체 설정 정보 (configuration)
- `macros`: Airflow에서 사용할 수 있는 매크로 함수들

### Context 예시: 매크로 활용

DAG 내에서 태스크가 실행될 때, `context` 정보를 받아 현재 실행 중인 날짜와 이전, 다음 실행 날짜를 출력합니다.

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.utils.dates import days_ago
from datetime import timedelta

# 기본 DAG 설정
default_args = {
    'owner': 'airflow',
    'start_date': days_ago(1),
}

# 매크로를 사용하는 함수
def use_macros(**context):
    ds = context['ds']  # 현재 실행 날짜 (YYYY-MM-DD 형식)
    next_ds = context['next_execution_date']  # 다음 실행 날짜
    prev_ds = context['prev_execution_date']  # 이전 실행 날짜

    print(f"현재 실행 날짜: {ds}")
    print(f"다음 실행 날짜: {next_ds}")
    print(f"이전 실행 날짜: {prev_ds}")

# DAG 생성
with DAG(
    'macro_example_dag',
    default_args=default_args,
    schedule_interval=timedelta(days=1),  # 매일 실행
    catchup=False,
) as dag:

    # PythonOperator에서 매크로 활용
    macro_task = PythonOperator(
        task_id='use_macros_task',
        python_callable=use_macros,
        provide_context=True,  # context 정보를 함수에 전달
    )
```

`use_macros` 함수가 `context` 정보를 사용해 현재와 이전, 그리고 다음 실행 날짜를 출력한다. `provide_context=True` 옵션을 사용해 `PythonOperator`는 자동으로 해당 정보를 함수로 전달한다.
