---
title:  "[Data_visualization] Matplotlib 해부하기 chap 2: Axes customizing"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Matplotlib
tags:
  - Data_visualization
last_modified_at: 2021-08-08
---  

## Axes customizing

### 1. Axes Layout Adjustment


#### 1) title, xlabel, ylabel 설정

```python

fig, ax = plt.subplots(figsize=(7, 7))

ax.set_title('Title', fontsize=20)
ax.set_xlabel('X', fontsize=15)
ax.set_ylabel('Y', fontsize=15)

```

다음 그림에서 왼쪽 그림과 같이 축 표시가 서로 겹치게 되면 굉장히 보기 안좋아진다. 이를 오른쪽으로 바꾸는 방법은 굉장히 간단하다.

![](/assets/images/plt_17.png){: width="90%"}   


#### 2) 축 간격 잡아주기

```python
fig.tight_layout()
```

![](/assets/images/plt_19.png){: width="100%"}  

![](/assets/images/plt_18.png){: width="100%"}  


#### 3) 눈금 간격 없애기

우리가 넣는 그림이 이미지인 경우, ax들의 눈금 간격을 없애주는게 깔끔하다

![](/assets/images/plt_20.png){: width="100%"}  

#### 4) 확장하기

![](/assets/images/plt_21.png){: width="100%"}  

![](/assets/images/plt_22.png){: width="100%"}  

![](/assets/images/plt_23.png){: width="100%"}  

