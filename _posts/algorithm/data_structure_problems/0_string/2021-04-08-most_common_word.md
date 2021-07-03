---
title:  "[Algorithm_Problem_String] 가장 흔한 단어"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - string_problem
tags:
  - data_structure
last_modified_at: 2021-04-08
---  

## 문제  
리트코드 819. Most Common Word  
난이도: ⭐  

```
Given a string paragraph and a string array of the banned words banned, return the most frequent word that is not banned. It is guaranteed there is at least one word that is not banned, and that the answer is unique.

The words in paragraph are case-insensitive and the answer should be returned in lowercase.
(금지된 단어를 제외한 가장 흔하게 등장하는 단어를 출력하라. 대소문자 구분을 하지 않으며, 구두점 또한 무시한다.)
```  

예시 1   
```
Input: paragraph = "Bob hit a ball, the hit BALL flew far after it was hit.", banned = ["hit"]
Output: "ball"
Explanation: 
"hit" occurs 3 times, but it is a banned word.
"ball" occurs twice (and no other word does), so it is the most frequent non-banned word in the paragraph. 
Note that words in the paragraph are not case sensitive,
that punctuation is ignored (even if adjacent to words, such as "ball,"), 
and that "hit" isn't the answer even though it occurs more because it is banned.
```  

예시 2  
```
Input: paragraph = "a.", banned = []
Output: "a"
```

### 나의 풀이:  
```python
class Solution:
    def mostCommonWord(self, paragraph: str, banned: List[str]) -> str:
        words = re.sub('[^a-zA-Z ]', '', paragraph.lower()).split()
        dict1 = collections.Counter([word for word in words if word not in banned])
        return dict1.most_common(1)[0][0]
------------------------------------------------------------------------------------
결과: 통과, 32ms
```

### 풀이 2:
```python
class Solution:
    def mostCommonWord(self, paragraph: str, banned: List[str]) -> str:
        words =[word for word in re.sub(r'[\W]', ' ', paragraph).lower().split() if word not in banned]
        
        counts = collections.Counter(words)
        return counts.most_common(1)[0][0]
----------------------------------------------------------------------------------------------
결과: 통과, 36ms
```