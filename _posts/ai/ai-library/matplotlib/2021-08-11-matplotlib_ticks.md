---
title:  "[Data_visualization] Matplotlib 해부하기 chap 5: Tick and Ticklabels"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Matplotlib
tags:
  - Data_visualization
last_modified_at: 2021-08-11
---  

## 1. Tick

### 1) 값 나타내기

#### a. major

```python
xticks = [i for i in range(10)]

ax.set_xticks(xticks)
```

![](/assets/images/tick_1.png){: width="40%"}  

set_xlim()과 비교했을 때 좀 더 자유롭다고 할 수 있습니다.  

![](/assets/images/tick_2.png){: width="100%"}  

#### b. minor

```python
xticks = [i for i in range(10)]

ax.set_xticks(xticks, minor=True)
```

```python
major_xticks = [i for i in range(0, 101, 20)]
minor_xticks = [i for i in range(0, 101, 5)]

major_yticks = [i for i in range(0, 11, 2)]
minor_yticks = [i for i in range(0, 11, 1)]

ax.set_xticks(major_xticks)
ax.set_xticks(minor_xticks, minor=True)

ax.set_yticks(major_yticks)
ax.set_yticks(minor_yticks, minor=True)
```

![](/assets/images/tick_3.png){: width="80%"}  


### 2) 기타 설정하기

```python
# 글씨 크기
ax.tick_params(labelsize=20)

# Tick의 길이와 두께(이거는 티가 별로 안나서 안 쓸 것 같다)
ax.tick_params(length=10, width=3)
```

```python
# Tick 위치
ax.tick_params(bottom=False, labelbottom=False, top=True, labeltop=True)

ax.tick_params(left=False, labelleft=False, right=True, labelright=True)
```

![](/assets/images/tick_4.png){: width="40%"}  

```python
# Tick 고르기 (x, y, major, minor)
# axis의 default는 both이고, which의 default는 major다

# x tick(major)
ax.tick_params(axis='x')

# x tick(minor)
ax.tick_params(axis='x', which='minor')

```
## 2. Ticklabels

```python
# Tick을 숫자값이 아닌 문자열로 나타내고 싶은 경우  
xtick_labels = ['Class ' + str(i) for i in range(10)]

ax.set_xticklabels(xtick_labels)

ax.tick_params(rotation=60)
```

![](/assets/images/tick_5.png){: width="40%"}  