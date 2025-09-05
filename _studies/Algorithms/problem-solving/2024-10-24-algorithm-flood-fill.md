---
title: "flood fill : graph"
excerpt: "Grind75 :   week 1"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/flood-fill

toc: true
toc_sticky: true

date: 2024-10-24
last_modified_at: 2024-10-24
---
## 문제

image와 flood가 시작될 근원지의 r,c그리고 어떤 색으로 칠해져야 하는지에 대한 color가 주어진다.
인접한 동서남북 방향으로 그림을 채울 수 있으며, 근원지와 동일한 색을 가진 픽셀들만 새로운 색으로 칠해진다.

## 풀이
기본 적인 bfs로 풀었다.

```python
class Solution:
    def floodFill(self, image: List[List[int]], sr: int, sc: int, color: int) -> List[List[int]]:
        colored_img = image
        nrow = len(image)
        ncol = len(image[0])
        visited = [[False]*ncol for _ in range(nrow)]
        org_color = image[sr][sc]
        
        dr = [-1,1,0,0]
        dc = [0,0,1,-1]

        queue = deque()
        queue.append((sr,sc))
        visited[sr][sc] = True
        colored_img[sr][sc] = color


        def isValid(r, c):
            if 0<=r<nrow and 0<=c<ncol and image[r][c] == org_color :
                return True
            else: return False

        while queue:
            cur_r, cur_c = queue.popleft()  
            for i in range(4):  
                next_r = cur_r + dr[i]  
                next_c = cur_c + dc[i]  
                if isValid(next_r, next_c): 
                    if not visited[next_r][next_c]: 
                        queue.append((next_r, next_c))  
                        visited[next_r][next_c] = True
                        colored_img[next_r][next_c] = color
        return colored_img
```
