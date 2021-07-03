---
title:  "[Algorithm_Problem_String] 가장 긴 팰린드롬 부분 문자열"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - string_problem
tags:
  - data_structure
last_modified_at: 2021-04-10
---  

## 문제  
리트코드 5. Longest Palindrome Substring  
난이도: ⭐⭐  

```
Given a string s, return the longest palindromic substring in s.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.
(문자열 배열을 받아 애너그램 단위로 그루핑하라)
```  

예시 1   
```
Input: s = "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```  

예시 2  
```
Input: s = "cbbd"
Output: "bb"
```

### 풀이 : 중앙을 중심으로 투 포인터 확장  
```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        
        def expand(left: int, right: int) -> str:
            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1
            return s[left+1 : right]
        
        if len(s) < 2 or s == s[::-1]:
            return s
        
        result = ''
        for i in range(len(s)-1):
            result = max(result, expand(i, i+1), expand(i, i+2), key=len)
            
        return result
        
------------------------------------------------------------------------------------
결과: 통과, 36ms
```