---
title: "Airflow에서의 Xcom"
excerpt: "어떨때 xcom을 써야할까?"

categories: [airflow]
tags: [dag, python, xcom]

permalink: /airflow/dag-xcom/

toc: true
toc_sticky: true

date: 2024-09-10
last_modified_at: 2024-09-29
---

# XCom을 활용한 Task 간 데이터 교환

Airflow의 XCom(다른 Task 간 교환 가능한 값)은 태스크 간에 데이터를 전달하는 수단이다. XCom은 Task가 실행 중 생성한 데이터를 다른 Task에서 가져와 사용할 수 있게 한다. 기본적으로 작은 데이터를 전달할 때 사용되며, 큰 데이터는 별도 스토리지를 사용해 저장하는 것이 권장된다. 1GB이상의 데이터의 경우에는 별도 스토리지를 사용하는 것이 일반적이다.

## xcom을 사용하는 방법

### 1. task 데코레이터를 활용하며 파이썬 함수의 return 값을 활용하기.

task 데코레이터 활용시 함수 입출력 관계만으로 데이터 전달이 수행되고 선후행 관계가 정해진다.
xcom_push, xcom_pull 과 같은 메서드를 사용하지 않아도 내부적으로 xcom이 사용되어 전달되는 구조이다.

```python
@task(task_id='xcom_push_by_return')
def xcom_push_by_return(**kwargs):
    transaction_value = 'status Good'
return transaction_value

@task(task_id='xcom_pull_by_return')
def xcom_pull_by_return(status, **kwargs):
    print(status)

xcom_pull_by_return(xcom_push_by_return())
```

### 2. ti 객체를 이용한 방법 : xcom_push, xcom_pull

\*\*kwargs에 존재하는 ti(task_instance) 객체 활용하면, xcom_push, xcom_pull과 같은 메서드를 사용해서 명시적으로 값을 넣거나 뺄 수 있다.

```python
@task(task_id='python_xcom_push_task1')
def xcom_push1(**kwargs):
    ti = kwargs['ti']
    ti.xcom_push(key="result1", value="value_1")
    ti.xcom_push(key="result2", value=[1,2,3])
@task(task_id='python_xcom_pull_task')
def xcom_pull(**kwargs):
    ti = kwargs['ti']
    value1 = ti.xcom_pull(key="result1")
    value2 = ti.xcom_pull(key="result2", task_ids='python_xcom_push_task1')
```

### 3. 두가지 방법을 혼용하기

```Python
@task(task_id='xcom_push_by_return')
def xcom_push_return(**kwargs):
    transaction_value = 'status Good'
return transaction_value

@task(task_id='xcom_pull_by_return')
def xcom_pull_return_by_method(**kwargs):
    ti = kwargs['ti']
    pull_value = ti.xcom_pull(key='return_value',
                              task_ids='xcom_push_by_return')

    print(pull_value)

xcom_push_return()>>xcom_pull_return_by_method()
```

**xcom_push_return** 에서 return하는 값은 자동으로 xcom에 key='return_value', task_ids=task_id로 저장된다.

**xcom_pull_return_by_method**에서는 ti 객체를 이용하여 명시적으로 xcom을 pull한다.

## xcom을 사용하지 말아야 하는 경우

Airflow에서 xcom은 기본적으로 메타 데이터 베이스인 SQLite에 저장된다. 간단한 로컬 환경에서 사용하기에는 적합하지만, 다중 사용자나 다중 프로세스 환경에서는 권장되지 않으며, 분산 실행에는 적합하지 않다. SQLite는 단일 파일 기반 데이터베이스이기 때문에 병렬성이나 성능에 한계가 있다. 큰 데이터를 xcom으로 저장하는 경우에는 다음과 같은 문제가 생길 수 있다.

### 1. **데이터베이스 부하 및 성능 저하**

큰 데이터를 자주 쓰거나 읽게 되면, 데이터베이스 I/O 부하가 커져 전체 DAG 실행 속도가 느려질 수 있다. 특히 다중 태스크가 병렬로 실행되면서 대량의 데이터를 XCom에 주고받는다면, 데이터베이스 쿼리 속도가 현저히 저하될 수 있다.

### 2. **메모리 및 저장 공간 문제**

대부분의 메타데이터 데이터베이스는 실시간으로 많은 양의 데이터를 처리하도록 설계되어 있지 않기 때문에, 큰 데이터를 저장하는 것은 비효율적이다. 성능 문제가 발생할 수 있다.

### 3. **XCom 데이터 직렬화 문제**

XCom을 통해 전달되는 데이터는 직렬화(Serialization)되어 데이터베이스에 저장된다. 하지만 큰 데이터를 직렬화하고 다시 역직렬화(Deserialization)하는 과정은 상당한 시간과 리소스를 소모한다. 특히 비정형 대용량 데이터(대규모 JSON 파일, 이미지, 비디오 등)를 직렬화하고 역직렬화하는 과정은 성능에 큰 영향을 미친다. 이 과정에서 CPU와 메모리 자원이 과도하게 사용될 수 있다.

### 4. **가시성 및 관리의 어려움**

웹 UI에서 XCom 데이터를 확인하거나 관리하는 것이 어려워질 수 있다. XCom은 디버깅 목적으로도 사용되기 때문에, 작은 데이터만 저장하는 것이 XCom 사용 시 더 직관적이고 관리하기 쉽다.
