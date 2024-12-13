---
title: "Valid Palindrome"
excerpt: "Grind75 :   week 1"

categories:
  - algorithm
tags:
  - [Algorithm]

permalink: /algorithm/palindrome

toc: true
toc_sticky: true

date: 2024-10-22
last_modified_at: 2024-10-22
---
## 문제
string이 주어지는데, 알파벳을 모두 소문자로 변경하고 나머지 문자는 제외하였을때 이 문자열이 회문인지를 판단한다. 

예시)
Example 1:

Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
Example 2:

Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
Example 3:

Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.

## 풀이
```
import re
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        s = re.sub(r'[^a-z]',"",s)
        if s == s[::-1]:
            return True
        else: return False
```
        
            
## 알게된 점
* 문제에서 alphanumeric이라고 한다면 알파벳 뿐 아니라 숫자도 남겨야 한다. 
나는 그냥 알파벳 빼면 다 남기는줄 알아서 아닌건 삭제하는 정규 표현식을 썼는데, 이런 경우에는 isalpha()수식을 쓸 수 있다고 한다. 그치만 영어가 모국어가 아닌 사람이 알기는 어렵다. 
* 문자열 슬라이싱에서 거꾸로 뒤집어봐야 하는 경우에는 다음과 같은 선택지가 있다. 
```
text = "hello"
reversed_text = ''.join(reversed(text))
```
reversed는 리스트를 뒤집어주는데, reversed된 걸 그대로 print하면 객체 자체를 반환한다. 
리스트를 즉시 뒤집어서 반환하지 않고 반복자(iterator)를 반환하기 때문이다. 반복자는 원본 데이터를 한번에 하나씩 읽을 수 있지만, 직접 출력하면 객체의 메모리 주소를 반환한다. 
따라서 이를 다시 리스트나 문자열로 반환하는 과정이 필요하다!
