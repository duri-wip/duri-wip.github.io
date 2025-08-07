---
title: "스택을 사용해서 queue 실행하기"
excerpt: "Grind75 :   week 1"

category:  algorithm
tags:
  - [알고리즘, Grind75]

permalink: /algorithm/implementing-queue-using-stack

toc: true
toc_sticky: true

date: 2024-10-24
last_modified_at: 2024-10-24
---
## 문제
2개의 스택만 사용해서 fifo 큐 구현하기. 

### 두 개의 스택을 사용하는 이유

스택 1 (입력 스택): 요소를 삽입할 때 사용
스택 2 (출력 스택): 요소를 꺼낼 때 사용

**요소 삽입 (push)**:
stack1에 순서대로 쌓기

**요소 제거 (pop)**:
stack2가 비어 있을 경우, stack1에 있는 모든 요소를 stack2로 옮긴다.
이 과정에서 요소의 순서가 뒤집힌다.
이제 stack2에서 pop을 하면 1부터 제거되므로 FIFO 순서를 유지하게 된다.

**peek (peek)**:
pop과 같은 원리로 stack2가 비어 있으면 stack1에서 요소를 옮긴 후, stack2의 맨 위 요소를 확인

## 풀이

```python
class MyQueue:

    def __init__(self):
        self.stack1 = []
        self.stack2 = []

    def push(self, x: int) -> None:
        self.stack1.append(x)

    def pop(self) -> int:
        if not self.stack2:
            while self.stack1:
                self.stack2.append(self.stack1.pop())
        return self.stack2.pop()
    def peek(self) -> int:
        if not self.stack2:
            while self.stack1:
                self.stack2.append(self.stack1.pop())
        return self.stack2[-1]

    def empty(self) -> bool:
        return not self.stack1 and not self.stack2
```