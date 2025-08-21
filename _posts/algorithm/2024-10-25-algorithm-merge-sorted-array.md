---
title: "정렬된 배열에 대한 merge"
excerpt: "Grind75 :   week 1"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/merge-sorted-array

toc: true
toc_sticky: true

date: 2024-10-25
last_modified_at: 2024-10-25
---
링크드 리스트에 대해서 더 알아보고 싶어서 연관 문제를 풀어봤다. 

## 문제

두개의 배열이 주어지고 유효한 요소의 숫자가 각각 주어질때 두 배열을 합치고 정렬한다. 
근데 이때 새로운 요소를 만들어 반환하지 않고 nums1요소를 수정한다.

예시)

Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.

## 풀이

처음에는 새로운 요소를 반환하지 않고 nums1 요소를 수정한다고 해서 이렇게 풀었다. 
```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        nums1 = nums1[:m] + nums2[:n]
        nums1.sort()
```
근데 프린트를 하면 답이 나오는데 채점 결과는 계속 틀린것. 
그래서 다시 생각해보니 내가 하고 있었던 것은 지역변수에 대한 수정이었다. 
```
전역변수
nums1, nums2
지역변수
```
의 상태에서 nums1 = nums1[:m] + nums2[:n]을 수행하면 
```
전역변수
nums1, nums2
지역변수
nums1
```
의 상태로 변경된다. 함수 내에서는 지역변수인 nums1을 바라보게 되므로 print하면 맞게 뜨지만 채점을 하면 전역변수 nums1을 바라보고 있으니까 틀린것..
return을 하지 말라는 문제는 처음 만나봐서 이런 생각은 안해봤다. 

다시 풀어본다.
nums1의 인덱스를 받아 비교하고 수정하고 추가하는 방법이면 되려나?

```python

```

앞에서 부터 수정하려고 했는데, 리스트의 길이때문에 어떤 테스트 케이스는 해결할 수 없었다. 
뒤에서부터 비교하는 알고리즘으로 수정해본다. 그러면 길이대로 리스트를 수정하는 과정을 거치지 않아도 되고, 길이 문제도 해결할 수 있을것이다.
