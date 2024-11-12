---
title: "피보나치"
excerpt: "이어드림 코딩테스트 특강 3주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/fibonacci

toc: true
toc_sticky: true

date: 2024-08-07
last_modified_at: 2024-08-07
---
## 문제

n 번째 피보나치 수 구하기

## 해결

```python
def fibo(n):
    if n == 1 or n == 2:
        return 1
    
    return fibo(n-1) + fibo(n-2)
```
## 알게된 점

- 중첩함수 : 함수 안에 함수가 있고, 마치 안에 있는 함수 자체를 객체로 쓰는 것 처럼 함수값을 리턴하는 함수를 말한다. 
- 이전 두개의 수를 저장해 재귀적으로 계산하는 방식은 다른 알고리즘에 활용되므로 염두에 두자.