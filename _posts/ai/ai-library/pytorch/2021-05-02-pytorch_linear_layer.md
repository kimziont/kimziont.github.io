---
title:  "[Pytorch] 선형 레이어"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Pytorch
tags:
  - AI
last_modified_at: 2021-05-02
---  

## 선형 모델 직접 구현

```python
import torch
import torch.nn as nn
```

```python
class MyLinear(nn.Module):

    def __init__(self, input_dim=3, output_dim=2):
        self.input_dim = input_dim
        self.output_dim = output_dim
        
        super().__init__()
        
        self.W = nn.Parameter(torch.FloatTensor(input_dim, output_dim))
        self.b = nn.Parameter(torch.FloatTensor(output_dim))
        
    def forward(self, x):
        # |x| = (batch_size, input_dim)
        y = torch.matmul(x, self.W) + self.b
        # |y| = (batch_size, input_dim) * (input_dim, output_dim)
        #     = (batch_size, output_dim)
        
        return y
```

```python
x = torch.FloatTensor([[1, 1, 1],
                       [2, 2, 2],
                       [3, 3, 3],
                       [4, 4, 4]])

linear = MyLinear(3, 2)

y = linear(x)

list(linear.parameters())
-------------------------------------------------------------
[Parameter containing:
 tensor([[0.0000e+00, 8.0995e-43],
         [6.7501e-07, 3.4008e-06],
         [1.0503e-05, 5.4720e+22]], requires_grad=True),
 Parameter containing:
 tensor([3.0601e+32, 1.6533e+19], requires_grad=True)]
```  

## nn.Linear 이용하기

```python
linear = nn.Linear(3, 2)
```  

## 여러 모델로 결합된 모델 만들기 

```python
class MyLinear(nn.Module):

    def __init__(self, input_dim=3, output_dim=2):
        self.input_dim = input_dim
        self.output_dim = output_dim
        
        super().__init__()
        
        self.linear = nn.Linear(input_dim, output_dim)
        
    def forward(self, x):
        y = self.linear(x)

        return y
```