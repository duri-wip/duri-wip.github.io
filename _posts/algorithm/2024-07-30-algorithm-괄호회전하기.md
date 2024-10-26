---
title: "괄호 회전하기"
excerpt: "이어드림 코딩테스트 특강 2주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/괄호회전하기

toc: true
toc_sticky: true

date: 2024-07-30
last_modified_at: 2024-07-30
---
[프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/76502)

## 문제 파악

왼쪽으로 한칸씩 이동했을 때 true가 나오는 괄호 조합이 몇개인지를 리턴합니다

## 접근 방법

이미 t.f가 나오는 모듈은 구현햇으므로 여기에 왼쪽으로 하나씩 이동해서 조합을 만드는 모듈을 붙여서 사용하기로 합니다

## 코드 구현

```java
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

## 배우게 된 점

그냥 되네요…

left로 움직이는 방법을 좀 더 잘 할수 잇을거 같은데 이게 최선인가요?

비트 shift 기능을 사용해보려고 했는데 스트링 타입에는 안된다고 해서 바로 포기햇는데 좀 더 찾아보겠습니다
