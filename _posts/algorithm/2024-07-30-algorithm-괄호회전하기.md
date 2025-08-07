---
title: "괄호 회전하기"
excerpt: "이어드림 코딩테스트 특강 2주차"

category:  algorithm
tags:
  - [알고리즘, 프로그래머스, 이어드림4기]


permalink: /algorithm/괄호회전하기

toc: true
toc_sticky: true

date: 2024-07-30
last_modified_at: 2024-07-30
---
[프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/76502)

## 문제

왼쪽으로 한칸씩 이동했을 때 true가 나오는 괄호 조합이 몇개인지를 리턴한다.

## 해결

```python
def isValid(s):
    tmp = []
    for i in s:
        if i in ["(","[","{"]:
            tmp.append(i)
        else:
            if i==")":
                if not tmp:#아무것도 없는데 끝괄호만 들어가면 f
                    return False
                elif tmp[-1]=="(":#마지막 요소가 첨괄호면 팝
                    tmp.pop()
                else:#마지막 괄호가 첨괄혼데 다른 종류면 f
                    return False
            elif i == "]":
                if not tmp:
                    return False
                elif tmp[-1]=="[":
                    tmp.pop()
                else:
                    return False
            else:
                if not tmp:
                    return False
                elif tmp[-1]=="{":
                    tmp.pop()
                else:
                    return False
    return not tmp

def toleft(s):
    return(s[1:]+s[0])

def solution(s):
    res = 0
    for i in range(len(s)-1):
        #print(isValid(s))
        res += isValid(s)
        s = toleft(s)
        #print(s)
    return res

```

## 알게된 점

- bit shift는 스트링 타입에는 안된다. 