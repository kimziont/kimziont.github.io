---
title:  "[Algorithm_Problem_String] 팰린드롬"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - string_problem
tags:
  - data_structure
last_modified_at: 2021-04-05
---  

## 문제  
리트코드 125. Valid Palindrome  
난이도: ⭐  
```
Given a string s, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.  
(주어진 문자열이 팰린드롬인지 확인하라. 대소문자를 구분하지 않으며, 영문자와 숫자만을 대상으로 한다.)
```
※ 팰린드롬(Palindrome): 앞뒤가 똑같은 단어나 문장, 뒤집어도 변하지 않는 단어나 문장을 팰린드롬이라고 합니다.  

예시 1  

```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.  
```

예시 2  

```
Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
```  
------------------------------------------------------------------------  
### 나의 풀이: 투 포인터 이용
```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        s = re.sub('[^a-zA-Z]', '', s)
        p_1, p_2 = 0, len(s)-1
        while p_1 < len(s)//2:
            if s[p_1] != s[p_2]:
                return False
            p_1 += 1
            p_2 -= 1
        return True
------------------------------------------------
결과: 통과, 런타임 48ms  
```

### 풀이 2: 슬라이싱 사용

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        s = re.sub('[^a-zA-Z]', '', s)
        
        if s == s[::-1]:
            return True
        return False
---------------------------------------------
좀 더 간결한 코드  
class Solution:
    def isPalindrome(self, s: str) -> bool:
        s = s.lower()
        s = re.sub('[^a-zA-Z]', '', s)
        
        return s==s[::-1]
----------------------------------------------
결과: 통과, 런타임 32ms  
```  

### 풀이 3: 데크 자료형 이용  

```python
def isPalindrome(self, s: str) -> bool:
  strs = collections.deque()

  for char is s:
    if char.isalnum():
      strs.append(char.lower())
  
  while len(strs) > 1:
    if strs.popleft() != strs.pop():
      return False

  return True
----------------------------------------------
결과: 통과, 런타임 64ms  
```  

### 배운 점  
* 파이썬에서는 문자열의 슬라이싱 속도가 굉장히 빠르기 때문에, 문자열을 조작할 때는 항상 슬라이싱을 우선으로 사용하는 편이 속도 개선에 유리하다.