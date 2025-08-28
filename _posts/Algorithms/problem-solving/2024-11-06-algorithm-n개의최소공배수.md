---
title: "n개의 최소공배수"
excerpt: "Programmers level 2"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스]

permalink: /algorithm/least-common-multiple

toc: true
toc_sticky: true

date: 2024-11-06
last_modified_at: 2024-11-06
---

## 문제

n개 수의 초소공배수. 즉 n개의 배수들 중 공통이 되는 가장 작은 숫자 구하기

## 풀이

```python
from itertools import combinations
def solution(arr):
    def gcp(a,b):
        while b != 0:
            a, b = b, a % b
        return a

    def lcm(a,b):
        return a*b // (gcp(a,b))

    # 첫번째와 두번째를 하고 나머지와 다시계산하는 식으로 접근한다!

    def lcm_recursive(numbers):
        if len(numbers) == 1:
            return numbers[0]
        else:
            first_res = lcm(numbers[0], numbers[1])
            return lcm_recursive([first_res] + numbers[2:])
    return lcm_recursive(arr)
```

## 알게된 점

- 최소공배수를 구하는 법 = (두수의 곱) / (두수의 최대공약수)
- 최대공약수를 구하는 법 = 두 수 a, b에서 a를 b로 나눈 나머지를 r이라고 할때 나머지가 0이 될때의 b값.
- 재귀를 자꾸 생각해보자~ 잡아먹으면서 진행하기
