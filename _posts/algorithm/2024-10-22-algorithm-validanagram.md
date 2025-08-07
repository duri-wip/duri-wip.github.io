---
title: "Valid Anagram"
excerpt: "Grind75 :   week 1"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/anagram

toc: true
toc_sticky: true

date: 2024-10-22
last_modified_at: 2024-10-22
---
## 문제
t와 s가 주어졌을때, s의 알파벳을 재조합하면 t를 구성할 수 있는지 판단한다. 
예시)
Example 1:

Input: s = "anagram", t = "nagaram"

Output: true

Example 2:

Input: s = "rat", t = "car"

Output: false

## 풀이
```
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) == len(t):
            s = [c for c in s]
            t = [c for c in t]

            while s:
                if s[-1] in t:
                    t.pop(t.index(s[-1]))
                    s.pop()
                else:
                    return False
            return True
        else:
            return False        
```

## 알게된 점
* 문자열은 list지 stack이 아니다. 그러니까 pop을 쓸수는 없다. 
