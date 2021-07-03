---
title:  "[Pytorch] 선형 회귀 모델"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Keras
tags:
  - AI
last_modified_at: 2021-05-03
---  

## MSE

```python
x = torch.FloatTensor([[1, 1],
                       [2, 2]])
x_hat = torch.FloatTensor([[0, 0],
                           [0, 0]])
```

```python
import torch.nn as nn

mse_loss = nn.MSELoss()

mse_loss(x_hat, x)
-------------------------------
tensor(2.5000)
```

```python
import torch.nn.functional as F

F.mse_loss(x_hat, x)
----------------------------
tensor(2.5000)

F.mse_loss(x_hat, x, reduction='sum')
------------------------------
tensor(10.)

F.mse_loss(x_hat, x, reduction='none')
------------------------------
tensor([[1., 1.],
        [4., 4.]])
```

## Gradient descent

```python
x = torch.FloatTensor([[1, 2],
                       [3, 4]]).requires_grad_(True)

x3 = x**2 - 4
y = x3.sum()

y.backward()

print(x.grad)
----------------------
tensor([[2., 4.],
        [6., 8.]])
```

```python
# Instead of implement gradient equation,
# we can use <optim class> to update model parameters, automatically.

import torch.nn.functional as F
import torch.optim as optim

optimizer = optim.SGD(model.parameters(),
                      lr=learning_rate)

# We don't need learning rate hyper-parameter for Adam.
# optimizer = optim.Adam(model.parameters())

for i in range(n_epochs):
    y_hat = model(x)
    loss = F.mse_loss(y_hat, y)
    
    optimizer.zero_grad()
    loss.backward()
    
    optimizer.step()
```

