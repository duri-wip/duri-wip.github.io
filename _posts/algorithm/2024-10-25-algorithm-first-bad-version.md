---
title: "첫번째 bad version 찾기"
excerpt: "Grind 75 : Week 2"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/first-bad-version

toc: true
toc_sticky: true

date: 2024-10-25
last_modified_at: 2024-10-28
---
## 문제
api콜을 통해 매개변수로 준 버전이 나쁜지 아닌지 알수 있다. 
최초로 나쁜 버전이 몇번인지를 반환하기

## 풀이
이진 분류로 풀어본다. 

```python
class Solution:
    def firstBadVersion(self, n: int) -> int:
        start = 1
        fin = n
        while start < fin:
            mid = (start + fin)//2
            if isBadVersion(mid) :
                fin = mid
            else:
                start = mid + 1
        return start
```