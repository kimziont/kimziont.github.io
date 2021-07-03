---
title:  "[Algorithm_Problem_Stack] 유효한 괄호"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - stack_queue_problem
tags:
  - data_structure
last_modified_at: 2021-05-01
---  

## 문제  
리트코드 20. Valid Parentheses  
난이도: ⭐  
```
Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:
Open brackets must be closed by the same type of brackets.
Open brackets must be closed in the correct order.
(괄호로 된 입력값이 올바른지 판별하라)
```

예시 1  

```
Input: s = "()"
Output: true
```

예시 2  

```
Input: s = "([)]"
Output: false
```  
------------------------------------------------------------------------  
### 나의 풀이: 스택을 이용한 풀이
```python
class Solution:
    def isValid(self, s: str) -> bool:
        
        dict1 = {'(':')', '{':'}', '[': ']'}
        stack = []
        
        for i in s:
            if i in dict1.keys():
                stack.append(i)
            elif not stack or i != dict1[stack.pop()]:
                return False
        return not stack
------------------------------------------------
결과: 통과, 런타임 32ms  
```