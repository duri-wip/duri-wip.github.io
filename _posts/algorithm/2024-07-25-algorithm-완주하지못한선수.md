---
title: "완주하지 못한 선수"
excerpt: "이어드림 코딩테스트 특강 1주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/completion-participant

toc: true
toc_sticky: true

date: 2024-07-25
last_modified_at: 2024-07-25
---

![프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42576)


## 문제
도전한 선수와 완주한 선수 리스트가 주어질때 완주한 선수를 선별하기.

## 해결

### 이중 반복문을 사용한 경우

```python
def solution(participant, completion):
    for i in completion:
        if i in participant:
            participant.remove(i)
    return participant[0]
```
시간 복잡도가 아쉽다. 


### 정렬을 사용한 경우

```python
def solution(completion, participant):
	completion.sort()
	participant.sort()

	for i, o in list(zip(completion, participant):
		if i!=o:
			print(i)
	print(participant[-1])
```

### 해시맵을 사용한 경우

```python
def solution(participant, completion):
    import collections
    count = collections.Counter(participant) - collections.Counter(completion)
    return list(count.keys())[0]
```
