---
title: "야근지수"
excerpt: "Programmers level 2"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스]

permalink: /algorithm/working-late
toc: true
toc_sticky: true

date: 2024-11-07
last_modified_at: 2024-11-07
---

## 문제

남은 일의 작업량을 제곱해서 야근 지수를 구할때, 야근 지수가 최소화 되도록 일하여 최소화된 값을 반환하기.

## 풀이
```python
def solution(n, works):
    if sum(works) <= n:
        return 0

    while n > 0:
        idx = works.index(max(works))
        works[idx] -= 1
        n -= 1
    
    def cal_p(n):
        return n ** 2
    
    return sum(map(cal_p, works))
```

### heap을 사용한 경우

```python
import heapq

def solution(n, works):
    if sum(works) <= n:
        return 0

    # 최대 힙 생성 (음수 사용)
    works = [-work for work in works]
    heapq.heapify(works)

    while n > 0:
        max_work = heapq.heappop(works)
        if max_work == 0:
            break  # 작업량이 0인 경우 중단
        heapq.heappush(works, max_work + 1)  # 작업량 감소
        n -= 1

    # 야근 지수 계산
    return sum([work ** 2 for work in works])
```

## 알게된 점
- 첫번째 방식으로 풀었을 때 효율성이 낮아서 힙을 활용했다. 
- 힙 : 이진트리자료구조로서 부모노드가 자식노드보다 크거나 작아야 하는 특징을 가지는 자료구조. 우선순위큐를 구현하는데 사용된다.