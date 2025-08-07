---
title: "태스크에 SLA 사용하기"
excerpt: "airflow 성능 최적화"

category:  airflow
taxanomy:
  - Airflow

tags:
  - [Dag, Python, sla]

permalink: /airflow/sla/

toc: true
toc_sticky: true

date: 2024-09-11
last_modified_at: 2024-09-29
---
## SLA 및 성능 모니터링

### SLA(SLA Miss)란?

SLA(Service Level Agreement)는 각 태스크가 지정된 시간 내에 완료되어야 하는 시간을 정의합니다. Airflow에서 SLA를 설정하면, 특정 태스크가 SLA 내에 완료되지 못했을 때 'SLA Miss' 이벤트가 트리거됩니다. 이를 통해 지연된 태스크를 모니터링하고 적절한 대응을 할 수 있습니다.

SLA는 `timedelta`로 정의하며, SLA를 위반한 경우 알림을 보낼 수 있습니다.

```python
from datetime import timedelta
from airflow import DAG
from airflow.operators.bash import BashOperator

default_args = {
    'start_date': datetime(2024, 10, 1),
}

with DAG('sla_example_dag', default_args=default_args, schedule_interval='@daily', catchup=False) as dag:

    task_1 = BashOperator(
        task_id='task_1',
        bash_command='sleep 5',
        sla=timedelta(seconds=3),  # SLA 3초 설정
    )
```

위 코드에서는 `task_1`이 3초 이내에 완료되지 않으면 SLA Miss가 발생합니다. Airflow 웹 UI에서 SLA Miss를 확인할 수 있으며, 알림을 설정하여 즉각 대응할 수 있습니다.

### 성능 모니터링

Airflow는 태스크 및 DAG의 성능을 모니터링할 수 있는 다양한 방법을 제공합니다. Airflow 웹 UI에서 태스크 실행 시간, 성공/실패 여부, 재시도 여부 등을 실시간으로 확인할 수 있습니다. 또한 로그를 통해 각 태스크의 상세 실행 내역을 확인할 수 있습니다.

성능 최적화를 위해 주기적으로 DAG의 성능을 모니터링하고, 병목 구간을 파악하여 병렬 처리, 자원 할당 등을 조정하는 것이 중요합니다.

