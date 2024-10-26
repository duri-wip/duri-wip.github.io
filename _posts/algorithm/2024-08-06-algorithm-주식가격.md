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

## 문제 파악

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요.

### 제한사항

- prices의 각 가격은 1 이상 10,000 이하인 자연수입니다.
- prices의 길이는 2 이상 100,000 이하입니다.

### 입출력 예

| prices | return |
| --- | --- |
| [1, 2, 3, 2, 3] | [4, 3, 1, 1, 0] |

### 입출력 예 설명

- 1초 시점의 ₩1은 끝까지 가격이 떨어지지 않았습니다.
- 2초 시점의 ₩2은 끝까지 가격이 떨어지지 않았습니다.
- 3초 시점의 ₩3은 1초뒤에 가격이 떨어집니다. 따라서 1초간 가격이 떨어지지 않은 것으로 봅니다.
- 4초 시점의 ₩2은 1초간 가격이 떨어지지 않았습니다.
- 5초 시점의 ₩3은 0초간 가격이 떨어지지 않았습니다.

## 접근 방법

스택에 쌓기

## 코드 구현

### 시간초과가 나야하는 코드(완전탐색) - O(n^2)

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

### 스택 이용 - O(n)

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
