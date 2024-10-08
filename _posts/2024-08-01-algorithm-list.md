---
title: "도커 스토리지의 구조와 관리법"
excerpt: "Docker Storage"

categories:
  - docker
tags:
  - [Docker]

permalink: /docker/docker-storage/

toc: true
toc_sticky: true

date: 2024-09-01
last_modified_at: 2024-10-01
---
## 개념

배열은 인덱스와 값을 일대일 대응해 관리하는 자료구조. 모든 공간은 인덱스와 대응하므로 한번에 원하는 값에 접근할 수 있다.

### 리스트 컴프리핸션을 사용하기

```
arr = [0 for _ in range(6)]
```

### pop

팝 할 인덱스를 인수로 받아 삭제하고 삭제한 데이터의 값을 반환

### remove

데이터 자체를 삭제. 인수로 받은 값이 처음 등장하는 위치의 데이터를 삭제한다.

## 문제

### 5-03 두개 뽑아서 더하기

### 5-04 모의고사

### 5-05 행렬의 곱셈

### 5-06 실패율

### 5-07 방문길이
