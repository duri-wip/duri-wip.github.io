---
title: "튜플보고 원래의 집합 추론하기"
excerpt: "Programmers level 2"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스]

permalink: /algorithm/find-original

toc: true
toc_sticky: true

date: 2024-11-04
last_modified_at: 2024-11-04
---

## 문제

중복되는 원소가 없는 튜플이 있고 이걸 토대로 부분집합을 만들어서 줬을때, 원래의 튜플 추론하기.

## 풀이

```python
def solution(s):
    # 파싱
    ## 문자열에서 집합형태 제거하고 분리하기
    s = s[2:-2].replace('},{','-')
    s = s.split('-')

    ## 숫자형으로 변환하고 셋으로 만들기
    new_s = [set(map(int, x.split(','))) for x in s]

    ## 길이에 따라 오름차순 정렬하기
    new_s.sort(key=len)

    # 새로이 추가된 원소 찾아서 반환하기
    result = []
    for subset in new_s:
        number = (list(subset-set(result))[0])
        result.append(number)

    return result
```

## 알게된 점

- set에는 -를 쓸 수 있다!!
