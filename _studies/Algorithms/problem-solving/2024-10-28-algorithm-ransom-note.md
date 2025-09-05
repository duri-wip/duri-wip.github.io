---
title: "ransome note 구성하기"
excerpt: "Grind 75 : Week 2"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/ransome-note

toc: true
toc_sticky: true

date: 2024-10-28
last_modified_at: 2024-10-28
---
## 문제
magazine에 있는 문자로 ransomnote를 쓸 수 있으면 t, 아니면 f

## 풀이
```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        r = [w for w in ransomNote]
        m = [w for w in magazine]

        for w in r:
            if w in m:
                m.pop(m.index(w))
            else:
                return False
        return True
```