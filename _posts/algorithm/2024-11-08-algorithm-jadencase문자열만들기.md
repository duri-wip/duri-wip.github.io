---
title: "Jaden Case"
excerpt: "Programmers level 2"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스]

permalink: /algorithm/jaden-case
toc: true
toc_sticky: true

date: 2024-11-08
last_modified_at: 2024-11-08
---
## 문제

모든 단어의 첫 문자가 대문자이고 이외의 알파벳은 소문자인 문자열이다. 문자열 s가 주어졌을때 s를 jaden case로 바꾼다음 문자열을 리턴하시오

## 풀이

* capitalize가 있는 걸 몰랐을 때 -> 런타임 에러남
```python
def solution(s)
    splt = s.split(' ')
    for c in splt:
        if not c[0].isalpha():
            word = c.lower()
        else:
            word = c[0].upper() + c[1:].lower()
        strs.append(word)
    return ' '.join(strs)
```

* 알고난 후 -> 에러 안남
```python
def solution(s):
    return ' '.join([word.capitalize() if word[0].isalpha() else word.lower() for word in s.split(' ')])
```
