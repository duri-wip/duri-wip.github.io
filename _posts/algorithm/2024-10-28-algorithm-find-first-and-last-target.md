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


## 풀이
```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        # 정렬된 배열에서 어떤게 타겟인지 찾아오기
        start = 0
        fin  = len(nums) -1
        # 배열이 비어있는 경우
        if not nums :
            return [-1, -1]
        # 타겟이 배열 안에 없는 경우
        elif nums[start] > target or nums[fin] < target:
            return [-1, -1]
        # 타겟이 있을지도 모르는 경우
        else:
            result = []
            for i in range(len(nums)):
                if nums[i] == target:
                    result.append(i)
            if not result:
                return [-1, -1]
            else:
                return [min(result), max(result)]
```