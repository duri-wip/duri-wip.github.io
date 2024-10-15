---
title: "자연어 처리 및 lstm 모델을 바탕으로 플레이리스트 추천하기 : plytube"
permalink: /projects/v2x/
layout: single

date: 2024-08-06
last_modified_at: 2024-10-15

show_date: true
comments: true
share: true
---

# About
## 프로젝트 개요
- 실시간으로 생성되는 대용량 데이터를 저장하고, 데이터 레이크를 기반으로 한 분석 환경을 제공하기 위한 파이프라인 구축

## 프로젝트 규모 및 일정
- 팀원 : 5명
- 기간 : 07.23 ~ 08.06

## 역할
• 파이프라인 설계
• Spark 클러스터 구축
• Data Lake 설계 
• 일일 데이터 대시보드 구축

## Architecture
<img src="/assets/images/project_img/yeardream/v2x_architecture.png" alt="v2x_architecture" width="100%">

### 개요
1. Airflow DAG 구성
   - T-data API에서 차량 위치 데이터를 수집하고, Kakao API를 사용해 수집된 위치의 주소 정보를 획득.
   - 비동기 구성을 통해 실시간 대용량 데이터 수집 및 파이프라인 작업 지원.

2. Kafka 클러스터 및 데이터 전송
   - 브로커 3개로 구성된 Kafka 클러스터를 구축.
   - Airflow에서 받은 데이터를 Kafka 토픽으로 전송하고  Spark 클러스터로 데이터를 consume해 실시간 처리.

3. Spark 스트림 애플리케이션
   - Spark로 실시간 데이터를 처리하기 위한 스트림 애플리케이션 생성.
   - 처리된 데이터를 Parquet 형식으로 변환해 저장.

4. 데이터 저장 및 고가용성 구성
   - Kafka 스트림 데이터를 PostgreSQL과 Hadoop HDFS에 저장.
   - PostgreSQL: Streaming replication 방식을 사용해 master-slave 이중화 구성.
   - Hadoop HDFS: 2대의 네임노드, 3대의 저널노드와 데이터 노드로 고가용성 유지.

5. 데이터 웨어하우스 및 분석팀 지원
   - Hadoop HDFS에 저장된 데이터는 데이터 분석을 위한 데이터 웨어하우스에 활용.
   - Parquet 파일을 CSV 파일로 변환해 일자별 데이터 레이크를 구축하고 간단한 일자별 대시보드를 제작하여 시각화.

6. 모니터링 시스템 구축
   - Prometheus로 PostgreSQL을 모니터링하기 위해 JMX Exporter를 설치해 Prometheus 형식으로 메트릭 변환.
   - Grafana 대시보드를 통해 엔지니어링 팀이 파이프라인 상태를 실시간으로 모니터링 가능.

## Trouble Shooting

1. 파이프라인 설계
- 실시간으로 수집되는 데이터의 크기와 설계한 아키텍처가 견딜 수 있는 부하가 어느 정도인지에 대한 경험이 부족
 ✓  해결 방법 : Kafka를 데이터 처리의 중심에 두고, 분산 처리를 위한 Kafka 클러스터(브로커 3개)를 구성. 스파크도 클러스터로 구성해 분산 병렬 처리를 하도록 함. 

2. Spark 클러스터 구축
- 경량화와 고가용성의 트레이드 오프 관계에 있어서 스파크 클러스터의 master 결정의 문제. 
 ✓  해결방법 : 하둡 에코시스템 내에 분산 병렬 구조를 설계하고 구축. 아키텍처 상에 있는 다른 툴과의 충돌 관계와, 실시간 대용량이라는 데이터의 특성을 고려하여 결정하였음.

3. 데이터 레이크 설계 및 분석 환경 구축 
- 데이터 분석팀에 제공되는 데이터 레이크의 활용성 확장을 위해 분석 환경을 구축해야할 필요
 ✓  해결방법 : 수집되는 데이터의 로그성 성격과 파퀘 파일로 축적되는 HDFS의 상황을 고려해 데이터 레이크를 설계. Parquet 파일을 CSV 형식으로 변환하여 일별 배치로 제공