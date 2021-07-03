---
title:  "[Deep_learning-Practice] Keras로 간단하게 LSTM, GRU 모델 만들기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - dl_practice
tags:
  - AI
last_modified_at: 2021-04-05
---  
#### 이론과 관련된 내용은 [여기](https://kimziont.github.io/dl_theory/advanced_rnn/)를 참고하시면 됩니다.  

## LSTM 모델  

사실 실제로 SimpleRNN이 사용되는 경우는 거의 없습니다. 이보다는 LSTM이나 GRU을 주로 사용하는데, 이번에는 임의의 입력에 대해서 LSTM을 사용할 경우를 보겠습니다.   

```python
lstm = LSTM(3, return_sequences=False, return_state=True)
hidden_state, last_state, last_cell_state = lstm(train_X)

print('hidden state : {}, shape: {}'.format(hidden_state, hidden_state.shape))
print('last hidden state : {}, shape: {}'.format(last_state, last_state.shape))
print('last cell state : {}, shape: {}'.format(last_cell_state, last_cell_state.shape))
------------------------------------------------------------------------------------------
hidden state : [[-0.00263056  0.20051427 -0.22501363]], shape: (1, 3)
last hidden state : [[-0.00263056  0.20051427 -0.22501363]], shape: (1, 3)
last cell state : [[-0.04346419  0.44769213 -0.2644241 ]], shape: (1, 3)
```

## Bidirectional LSTM  
난이도를 조금 올려서 양방향 LSTM의 출력값을 확인해보겠습니다. return_sequences가 True인 경우와 False인 경우에 대해서 은닉 상태의 값이 어떻게 바뀌는지 직접 비교하기 위해서 이번에는 출력되는 은닉 상태의 값을 고정시켜주겠습니다.  

```python
k_init = tf.keras.initializers.Constant(value=0.1)
b_init = tf.keras.initializers.Constant(value=0)
r_init = tf.keras.initializers.Constant(value=0.1)
```  

```python
from tensorflow.keras.layers import LSTM, Bidirectional

bilstm = Bidirectional(LSTM(3, return_sequences=False, return_state=True, \
                            kernel_initializer=k_init, bias_initializer=b_init, recurrent_initializer=r_init))
hidden_states, forward_h, forward_c, backward_h, backward_c = bilstm(train_X)

print('hidden states : {}, shape: {}'.format(hidden_states, hidden_states.shape))
print('forward state : {}, shape: {}'.format(forward_h, forward_h.shape))
print('backward state : {}, shape: {}'.format(backward_h, backward_h.shape))
----------------------------------------------------------------------------------------------------------------
hidden states : [[0.6303139  0.6303139  0.6303139  0.70387346 0.70387346 0.70387346]], shape: (1, 6)
forward state : [[0.6303139 0.6303139 0.6303139]], shape: (1, 3)
backward state : [[0.70387346 0.70387346 0.70387346]], shape: (1, 3)
```  
이번에는 무려 5개의 값을 반환합니다. return_state가 True인 경우에는 정방향 LSTM의 은닉 상태와 셀 상태, 역방향 LSTM의 은닉 상태와 셀 상태 4가지를 반환하기 때문입니다. 다만, 셀 상태는 각각 forward_c와 backward_c에 저장만 하고 출력하지 않았습니다. 첫번째 출력값의 크기가 (1, 6)인 것에 주목합시다. 이는 return_sequences가 False인 경우 정방향 LSTM의 마지막 시점의 은닉 상태와 역방향 LSTM의 첫번째 시점의 은닉 상태가 연결된 채 반환되기 때문입니다. 그림으로 표현하면 아래와 같이 연결되어 다음층에서 사용됩니다.  

![](/assets/images/rnn_practice_4.png){: width="100%"}  