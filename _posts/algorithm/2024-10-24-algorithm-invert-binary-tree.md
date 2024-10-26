---
title: "이진트리 뒤집기"
excerpt: "Grind75 :   week 1"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/invert-binary-tree

toc: true
toc_sticky: true

date: 2024-10-24
last_modified_at: 2024-10-24
---
## 문제

전위 표기 된 이진트리가 주어졌을때 이를 반대로 뒤집고 다시 전위 표기해서 반환하기

예시)
Input: root = [4,2,7,1,3,6,9]
Output: [4,7,2,9,6,3,1]

## 풀이
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if root is None:
            return None
        
        root.left, root.right = root.right, root.left

        self.invertTree(root.left)
        self.invertTree(root.right)

        return root
```
## 알게된 점
* 재귀를 이렇게도 쓰는구나. while만 생각하지 말기

