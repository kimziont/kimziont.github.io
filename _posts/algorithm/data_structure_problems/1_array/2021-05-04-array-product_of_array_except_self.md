---
title:  "[Algorithm_Problem_Array] 자신을 제외한 배열의 곱"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - array_problem
tags:
  - data_structure
last_modified_at: 2021-05-04
---  

## 문제  
리트코드 238. Product of Array Except Self  
난이도: ⭐⭐
```
Given an integer array nums, return an array answer such that answer[i] is equal to the product of all the elements of nums except nums[i].
The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer.
You must write an algorithm that runs in O(n) time and without using the division operation.
(배열을 입력받아 output[i]가 자신을 제외한 나머지 모든 요소의 곱셈 결과가 되도록 출력하라. (나눗셈을 하지않고, O(n)에 풀이하라))
```

예시 1  

```
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

예시 2  

```
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```  

------------------------------------------------------------------------  
### 나의 풀이: 

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        p = 1
        list1= []
        for index, num in enumerate(nums):
            if index != 0:
                p*= nums[index-1]
            list1.append(p)
            
        p = 1
        for i in range(len(nums)-1, -1, -1):
            if i < len(nums) - 1:
                p *= nums[i+1]
                list1[i] *= p
        return list1

-------------------------------------------------------------------
# 더 나은 코드
out = []
p = 1
for i in range(0, len(nums)):
    out.append(p)
    p = p * num[i]

p = 1
for i in range(len(nums) - 1, -1, -1):
    out[i] = out[i] * p
    p = p * nums[i]
return out
```

### 배운 점  

range() 함수를 이용해서 끝에서 부터 처음까지 역순으로 순회하고자 할 때, 처음 인덱스를 0 으로 하면 그 앞에서(인덱스 1에서) 멈추게 되어 어떻게 해야할까 라는 생각을 했었다. 나는 -1로 하면 시퀀스의 가장 끝 인덱스를 표현하는 또 다른 방법이 -1이기에 -1이 될 줄 몰랐는데, -1로 하니까 된다. 내 생각에 range() 함수 안에서 -1은 말 그대로 -1 이다

```python
for i in range(0, -1, -1):
    print(i)
---------------------------
0 시작 -1 전까지 1씩 감소 => 0만 출력
```

```python
for i in range(0, -1):
    print(i)
----------------------------
0 시작 -1 전까지 1씩 증가 => 아무것도 출력 안된다
```

```python
for i in range(5, -1, -1):
    print(i, end=' ')
--------------------------------
5 4 3 2 1 0
```