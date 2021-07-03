---
title:  "[Algorithm_Problem_Array] 두 수의 합"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - array_problem
tags:
  - data_structure
last_modified_at: 2021-05-01
---  

## 문제  
리트코드 1. Two sum  
난이도: ⭐  
```
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.  
You may assume that each input would have exactly one solution, and you may not use the same element twice.
You can return the answer in any order.
(덧셈하여 타겟을 만들 수 있는 배열의 두 숫자 인덱스를 리턴하라)
```

예시 1  

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Output: Because nums[0] + nums[1] == 9, we return [0, 1].
```

예시 2  

```
Input: nums = [3,3], target = 6
Output: [0,1]
```  
------------------------------------------------------------------------  
### 나의 풀이: in을 이용한 탐색
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for index, num in enumerate(nums):
            answer = target - num

            if answer in nums[index+1:]:
                return [nums.index(num), nums[index+1:].index(answer) + (index+1)]
------------------------------------------------
결과: 통과, 런타임 864ms  
```


### 풀이 2: 브루트 포스  

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for num1 in nums:
            for num2 in nums:
                if num1 + num2 == target:
                    return [nums.index(num1), nums.index(num2)]
----------------------------------------------
결과: 통과, 런타임 5284ms  
```  

### 풀이 3: 딕셔너리 사용

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        dict1 = {key:value for value, key in enumerate(nums)}

        for index, num in enumerate(nums):
            if target-num in dict1 and index != dict1[target-num]:
                return [index, dict1[target-num]]
---------------------------------------------
결과: 통과, 런타임 48ms  
```  


### 배운 점  
* 해시테이블에서 key를 통해 value찾는 것 평균적으로 O(1), 그러므로 값을 key로 저장해놓고, 값의 인덱스를 value로 저장해놓으면, 그 값(key)이 있다면 인덱스를 O(1)으로 찾아준다. 그렇다면 값이 있는지 확인하는 것의 복잡도는? 이것 또한 O(1)이다 왜냐하면 KEY를 HASH 함수에 넣어보기만 하면 바로 찾아주기 때문이다.   

* 리스트를 딕셔너리로 바꾸는 방법  

    ```python
    nums = [2,7,11,15]

    # 쓰레기같은 방법 (내가 한 방법ㅋㅋ)
    {key:value for key, value in list(zip(list(range(len(nums))), nums))}
    {0: 2, 1: 7, 2: 11, 3: 15}

    # 좋은 방법
    {key:value for key, value in enumerate(nums)}
    {0: 2, 1: 7, 2: 11, 3: 15}

    # 값을 KEY로, 인덱스를 VALUE로 할 수도 있다.
    {key:value for value, key in enumerate(nums)}
    ```  