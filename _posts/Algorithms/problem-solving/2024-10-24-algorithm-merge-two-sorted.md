---
title: "두 정렬된 리스트 병합"
excerpt: "Grind75 :   week 1"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/merge-two-sorted

toc: true
toc_sticky: true

date: 2024-10-24
last_modified_at: 2024-10-24
---
## 문제

이미 정렬되어 있고 다음 노드와 연결된 형태의 리스트 두개가 주어졌을때, 같은 방식으로 정렬하고 다음 노드와 연결되어 있는 리스트를 반환하기

예시)
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
Example 2:

Input: list1 = [], list2 = []
Output: []
Example 3:

Input: list1 = [], list2 = [0]
Output: [0]

## 풀이
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if list1 is None:
            return list2
        if list2 is None:
            return list1
        
        if list1.val <= list2.val :
            list1.next = self.mergeTwoLists(list1.next, list2)
            return list1
        else:
            list2.next = self.mergeTwoLists(list2.next, list1)
            return list2

```
## 알게된 점

* 재귀를 이렇게도 쓰는구나~ 저번의 이진 정렬 문제와 비슷하면서 다르다.
* 함수를 정의할 때 인풋 자료형을 지정하면 지정된 자료형을 넣도록 주의하자.