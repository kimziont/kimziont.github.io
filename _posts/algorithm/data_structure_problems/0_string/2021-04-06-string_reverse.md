---
title:  "[Algorithm_Problem_String] 문자열 뒤집기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - string_problem
tags:
  - data_structure
last_modified_at: 2021-04-06
---  

## 문제  
리트코드 344. Reverse String  
난이도: ⭐  

```
Write a function that reverses a string. The input string is given as an array of characters s.
(문자열을 뒤집는 함수를 작성하라. 입력값은 문자 배열이며, 린턴 없이 리스트 내부를 직접 조작하라.)
```  

예시  

```
Input: s = ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```  

### 나의 풀이: 슬라이싱

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        s = s[::-1]
----------------------------------------------------
결과: 오류
```

원래는 s[::-1]도 정상적으로 처리되어야 하지만, 이 문제는 공간 복잡도를 O(1)로 제한하다 보니 변수 할당을 처리하는데 다소 제약이 있다. 이 때 다음과 같은 트릭을 사용하면 잘 동작한다.
s[:] = s[::-1]

### 풀이 2: Pythonic way  
```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        s.reverse()
-----------------------------------------------------
결과: 통과, 48ms
```

### 풀이 3: 투 포인터 이용  

```python
class Solution:
    def reverseString(self, s: List[str]) -> None:
        left, right = 0, len(s) - 1
        
        while left < right:
            s[left], s[right] = s[right], s[left]
            
            left += 1
            right -= 1
------------------------------------------------
결과: 통과, 런타임 52ms  
        
```
