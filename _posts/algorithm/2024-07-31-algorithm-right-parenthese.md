---
title: "올바른 괄호"
excerpt: "이어드림 코딩테스트 특강 2주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/right-parenthese

toc: true
toc_sticky: true

date: 2024-07-31
last_modified_at: 2024-07-31
---
![프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12909)

## 문제

올바르게 괄호가 닫혀있는지 판단하기

## 해결

```python
def solution(s):
    stack = []
    for p in s:
        if p == '(':
            stack.append(p)
        else:
            if not stack:
                return False
            stack.pop()
    return not stack
```

## 알게된 점
- 스택에 넣고 마지막에 넣은 값을 조건문에 넣어서 비교해서 빼는 방식으로 진행되는 것이 포인트.
- if not stack : 의 느낌을 기억합니다!

