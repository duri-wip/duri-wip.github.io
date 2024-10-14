---
title: "two sum"
excerpt: "이어드림 코딩테스트 특강 1주차"

categories:
  - algorithm
tags:
  - [Algorithm, ]

permalink: /algorithm/two-sum

toc: true
toc_sticky: true

date: 2024-07-29
last_modified_at: 2024-07-29
---
[Two Sum - LeetCode](https://leetcode.com/problems/two-sum/description/)

## 문제 파악

## 접근 방법

1. 이중반복문 $O(n^2)$
2. 정렬 (two pointer) $O(nlogn)$
3. 해시테이블 $O(n)$

## 코드 구현

### 이중반복문

```python
class Solution:
    def twoSum(self, nums, target):
        n = len(nums)
        for i in range(n):
            for j in range(i+1, n):
                if nums[i] + nums[j] == target:
                    return [i,j]
```

시행착오

```python
class trialanderror:
	def twoSum(self, nums, target):
		for i in range(len(nums)):
			for j in range(len(nums[i+1:])):
				if nums[i]+nums[i+j] == target:
					return(i, i+j]
```

이 경우에는 테스트 케이스 [3,2,4] (target=6)과 [3,3] (target=6)에서 에러가 났다.

인덱스 시작점에 +1을 할건지 말건지 항상 주의깊게 생각하자. 

### 이중포인터

```python
nums = [4, 1, 9, 7, 5, 3, 16]
class Solution:
    def twoSum(self, nums, target):
        new_nums = [[v, i] for i, v in enumerate(nums)]
        new_nums.sort(key=lambda x:x[0])
        l, r = 0, len(nums)-1
        while l < r :
            nums_sum = new_nums[l][0]+new_nums[r][0]
            if nums_sum > target:
                r -= 1
            elif nums_sum < target:
                l += 1
            else:
                return [new_nums[l][1], new_nums[r][1]]

s = Solution().twoSum(nums, 14)
print(s)
```

시행착오

이중포인터의 핵심은 포인터만 돌면서 값을 가지고와서 계산을 한다는 점인데, 여기서 핵심은 초기의 인덱스 값을 밸류값과 매치하여 저장하고 밸류를 미리 정렬해 둔다는 점이다. 

```python
nums = [4, 1, 9, 7, 5, 3, 16]
new_nums = [[v, i] for i, v in enumerate(nums)]
new_nums.sort(key=lambda x:x[0])
```

위 코드를 수행하면 new_nums에는 (밸류, 인덱스)가 매칭되어 저장되게 된다. 

(구현 방식은 여러가지가 있겠지만..)

그리고 합이 매치되는 조합을 찾는 것이 목적이므로 최대한 적은 수의 검색을 하기 위해 left에 있을 수 있는 가장 최대의 right를 찾아서 up, down 을 따져보는 방식으로 경우의 수를 줄인다.

만약에 left가 가질 수 잇는 가장 최대의 right로도 target을 넘지 못한다면 left의 값으로는 target을 만들 수 없으므로 left 포인터를 이동하는 방식이다.

사실 합을 구하는 방식은 비슷해 보이고, 위에 밸류와 키값을 매치하는 방식을 다르게 구현해 보았다.

```python
new_nums = list(zip(nums, range(len(nums)))
```

### 해시맵

해시맵이란?

자바에서 해시맵(HashMap)은 **키(Key)와 값(Value)의 쌍으로 데이터를 저장하는 자료구조**. 해시맵은 내부적으로 해시 테이블을 사용하여 데이터를 관리하므로 검색 속도가 빠르다.

키로 인덱스를 반환해야 하는 경우에 잘 쓸 수 있음. 

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        memo = {}
        for i, num in enumerate(nums): #[3,2,4]
            needed = target - num #3
            if needed in memo :
                return [memo[needed], i]
            memo[num] = i
```

## 배우게 된 점

- 이중포인터
- 

## 질문

댓글로 또는 이곳에 질문 남겨주세요.