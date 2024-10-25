---
title: "Spark 클러스터 구축하기2"
excerpt: "로컬에 설치하고 jupyter 붙이기"

categories:
  - spark
tags:
  - [Spark]

permalink: /spark/spark-cluster-2/

toc: true
toc_sticky: true

date: 2024-10-25
last_modified_at: 2024-10-25
---

## 1. 로컬 환경 구축
### 1. pyenv 설치

[설치하기](https://github.com/duri-wip/QuickStartKit/blob/main/pyenv.md)

3.11.9를 설치해준다.

### 2. java 설치
[설치하기](https://github.com/duri-wip/QuickStartKit/blob/main/java.md)

스파크 버전에 맞는 자바를 설치해야하는 것에 주의하자. 
나는 스파크 3.5.1을 사용할 것이므로 충돌하지 않는다고 알려진 자바 11버전 headless를 설치한다.(용량문제)

### 3. spark 설치
[설치하기](https://github.com/duri-wip/QuickStartKit/blob/main/spark.md)

### 4. jupyter lab 설치
```python
pip install jupyterlab
```
#### 실행
```bash
jupyter lab --ip=0.0.0.0 --port=8888 --no-browser
```
---

설정완료!

![](/assets/images/post_img/jupyter.png)
