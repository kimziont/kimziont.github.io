---
title:  "[Data_visualization] Matplotlib 해부하기 chap 3: Text와 Font dict"
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

## Figure와 Axes 제목 달기

```python
fig, ax = plt.subplots(figsize=(7, 7))

# Figure 제목 달기 ( 잘 사용 안함)
fig.suptitle("Figure title", fontsize=30, fontfamily='monospace')

# Axes에 제목 달기
ax.set_title("Ax title", fontsize=30, fontfamily='serif')

# alpha는 투명도
ax.set_xlabel('X', fontsize=20, color='darkblue', alpha=0.7)

ax.set_ylabel('Y', fontsize=20)
```

## Text Alignment  

```python
# (0, 0)에 글자의 vertical center 와 horizontal center가 위치한다
ax.text(x=0, y=0, va='center', ha='center', s ='Hello', fontsize=30)
```

![](/assets/images/plt_31.png){: width="100%"}  

### 1) Title Alignment

![](/assets/images/plt_32.png){: width="100%"}  

![](/assets/images/plt_33.png){: width="100%"}  
