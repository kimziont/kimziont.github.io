---
title:  "[Pytorch] 선형 레이어"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Keras
tags:
  - AI
last_modified_at: 2021-05-02
---  

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
 tensor([[ 0.4935, -0.3351, -0.3799],
         [-0.3448,  0.5737, -0.5283]], requires_grad=True),
 Parameter containing:
 tensor([0.2594, 0.5389], requires_grad=True)]
```