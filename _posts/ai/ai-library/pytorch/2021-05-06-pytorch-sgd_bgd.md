---
title:  "[Pytorch] SGD, BGD"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Keras
tags:
  - AI
last_modified_at: 2021-05-06
---   

## SGD  

```python
for i in range(n_epochs):
    # Shuffle the index to feed-forward.
    indices = torch.randperm(x.size(0))
    x_ = torch.index_select(x, dim=0, index=indices)
    y_ = torch.index_select(y, dim=0, index=indices)
    
    x_ = x_.split(batch_size, dim=0)
    y_ = y_.split(batch_size, dim=0)
    # |x_[i]| = (batch_size, input_dim)
    # |y_[i]| = (batch_size, output_dim)
    
    y_hat = []
    total_loss = 0
    
    for x_i, y_i in zip(x_, y_):
        # |x_i| = |x_[i]|
        # |y_i| = |y_[i]|
        y_hat_i = model(x_i)
        loss = F.mse_loss(y_hat_i, y_i)

        optimizer.zero_grad()
        loss.backward()

        optimizer.step()
        
        total_loss += float(loss) # This is very important to prevent memory leak.
        y_hat += [y_hat_i]

    total_loss = total_loss / len(x_)
    if (i + 1) % print_interval == 0:
        print('Epoch %d: loss=%.4e' % (i + 1, total_loss))
    
y_hat = torch.cat(y_hat, dim=0)
y = torch.cat(y_, dim=0)
```


## BGD  

```python

```