---
title: "튜플"
excerpt: "Programmers level 2"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/tuple

toc: true
toc_sticky: true

date: 2024-11-06
last_modified_at: 2024-11-06
---

## 문제

중복되는 원소가 없는 튜플이 있고
{2,1,3,4}

이걸 토대로 부분집합을 만들어서 제공한다.
{{2}, {2, 1}, {2, 1, 3}, {2, 1, 3, 4}}

그러면 이 부분집합을 보고 원래의 튜플을 추론한다. 이때 원소에는 순서가 있다.

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
