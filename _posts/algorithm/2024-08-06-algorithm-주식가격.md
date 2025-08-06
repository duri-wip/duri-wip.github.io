---
title: "주식가격"
excerpt: "이어드림 코딩테스트 특강 3주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/stack-prices

toc: true
toc_sticky: true

date: 2024-08-06
last modified_at: 2024-08-06
---
[프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42584)

## 문제

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 반환하기

## 해결

### 이중 반복문 사용하기
```python
def solution(prices):
    n = len(prices)
    answer = [0]*n
    for i in range(n):
        flag = False
        for j in range(i+1, n):
            if prices[i] > prices[j]: 
                answer[i] = j-i
                flag = True
                break
        if not flag: answer[i] = n-i-1
                
    return answer
```

### 스택을 사용하기

```python
def solution(prices):
    n = len(prices)
    answer = [0] * n
    stack = []
    
    for i in range(n):
        while stack and prices[i] < prices[stack[-1]]:
            j = stack.pop()
            answer[j] = i-j
        stack.append(i)    
    
    while stack:
        i = stack.pop()
        answer[i] = n-1 - i
                
    return answer
```

