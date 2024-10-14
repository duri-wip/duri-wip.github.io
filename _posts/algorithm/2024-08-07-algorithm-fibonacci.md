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
### 기본 형태 재귀

```python
def fibo(n):
    # basecase
    if n == 1 or n == 2:
        return 1
    
    # recursive call
    return fibo(n-1) + fibo(n-2)

print(fibo(8))
```

### 중첩함수?

함수 안에 함수가 있고, 마치 안에 있는 함수 자체를 객체로 쓰는 것 처럼 함수값을 리턴하는 함수를 말한다. 

### list에 저장하기

```python
def fibo(n):
    fibo_list = [1] * (n)
    
    def recur(n):
        # basecase
        if n == 1 or n == 2:
            return 1
            
        # recursive call
        fibo_list[n-1] = recur(n-1) + recur(n-2)
        return fibo_list[n-1]
    
    recur(n)
    return fibo_list

print(fibo(8))
```