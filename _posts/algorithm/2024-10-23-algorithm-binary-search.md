---
title: "이진탐색"
excerpt: "Grind75 :   week 1"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/binary-search

toc: true
toc_sticky: true

date: 2024-10-23
last_modified_at: 2024-10-23
---
## 문제

리스트 안에 타겟이 있으면 그 인덱스를, 없으면 -1을 반환하기
근데 문제 제목이 이진검색인걸로 보아 이진 검색 알고리즘을 구현해야하는것 같다. 

## 풀이
이진 검색을 안하는 경우
```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        if target not in nums:
            return -1
        else:
            return nums.index(target)
```
하는 경우

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums)-1
        while left <= right:
            mid = (left+right) // 2
            if target == nums[mid]:
                return mid
            elif target > nums[mid]:
                left += 1
            else:
                right -= 1
        return -1
```

## 알게된 점
* 인덱스를 구하는 경우에 ! 이중포인터를 쓰는게 좋다. 
