---
title: "완주하지 못한 선수"
excerpt: "이어드림 코딩테스트 특강 1주차"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /docker/docker-storage/

toc: true
toc_sticky: true

date: 2024-07-25
last_modified_at: 2024-07-25
---

![프로그래머스 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42576)


## 문제 파악

## 접근 방법

이미 풀어보려고 했었는데 효율성 통과를 못해서 실패 상태로 있었다

```python
def solution(participant, completion):
    for i in completion:
        if i in participant:
            participant.remove(i)
    return participant[0]
```

시간 복잡도가 O(n^2)가 걸렷군. 단순 이중 포문으로 풀어서.

이번에는 포인터나 해시맵으로 풀어보려고 한다

해시맵으로 풀 때 꼭 인덱스랑 엮어주려고 하지 말것. 해시맵의 핵심은 정렬이다!!

## 코드 구현

```java
def solution(completion, participant):
	completion.sort()
	participant.sort()

	for i, o in list(zip(completion, participant):
		if i!=o:
			print(i)
```

이렇게 하면 맨 마지막 순서의 요소가 정답인 경우에 null값을 리턴하므로, if문을 모두 돌고도 i값이 없는 경우에 participant 리스트의 마지막 요소를 반환해주는 가지를 만들어 줘야 한다.

첫번째 시도로 당연히 and문을 붙이고 else 식을 탔다.

```python
def solution(completion, participant):
	completion.sort()
	participant.sort()

	for i, o in list(zip(completion, participant):
		if i!=o and i is not Null:
			print(i)
		else:
			print(participant[-1])
```

아 그랫더니 당연히 I 는 언제나 not null이겟지(왜냐면 해시맵을 짜면 짧은 요소에서 끊기니까 1개가 더 많은 participant요소의 마지막 요소는 끊기게 되니깐) 그래서 정확도에서도 틀려버린다

그리고 그게 아니더라도 and문을 붙여서 반복문을 돌리는 경우에는 시간 효율성이 당연히 떨어지겟지용

그래서 그냥 for문을 다 돌고도 리턴을 못한 경우에 마지막에 리턴값을 정해주면 된다.. 간단

```python
def solution(completion, participant):
	completion.sort()
	participant.sort()

	for i, o in list(zip(completion, participant):
		if i!=o:
			print(i)
	print(participant[-1])
```
