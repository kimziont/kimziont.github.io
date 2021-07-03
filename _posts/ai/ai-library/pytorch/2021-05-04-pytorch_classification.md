---
title:  "[Pytorch] 로지스틱 회귀 모델"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Keras
tags:
  - AI
last_modified_at: 2021-05-04
---  

## activation_functions

```python
# 1. 객체 생성을 이용한 방법
act = nn.Sigmoid()
act(x)

# 2. 함수를 이용한 방법
torch.sigmoid(x)
```

## Build model

```python
# Define costum model.
class MyModel(nn.Module):
    
    def __init__(self, input_dim, output_dim):
        self.input_dim = input_dim
        self.output_dim = output_dim
        
        super().__init__()
        
        self.linear = nn.Linear(input_dim, output_dim)
        self.act = nn.Sigmoid()
        
    def forward(self, x):
        # |x| = (batch_size, input_dim)
        y = self.act(self.linear(x))
        # |y| = (batch_size, output_dim)
        
        return y
```

```python
model = MyModel(input_dim=x.size(-1),
                output_dim=y.size(-1))
crit = nn.BCELoss() # Define BCELoss instead of MSELoss.

optimizer = optim.SGD(model.parameters(),
                      lr=learning_rate)
```

## Train  

```python
for i in range(n_epochs):
    y_hat = model(x)
    loss = crit(y_hat, y)
    
    optimizer.zero_grad()
    loss.backward()
    
    optimizer.step()
    
    if (i + 1) % print_interval == 0:
        print('Epoch %d: loss=%.4e' % (i + 1, loss))
```

## Evaluation  

```python
correct_cnt = (y == (y_hat > .5)).sum()
total_cnt = float(y.size(0))

print('Accuracy: %.4f' % (correct_cnt / total_cnt))
```