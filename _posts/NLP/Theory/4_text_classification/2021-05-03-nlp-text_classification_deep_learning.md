---
title:  "[NLP] 텍스트 분류를 위한 딥러닝 모델"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - text_classification
tags:
  - NLP
last_modified_at: 2021-05-03
---  

## 순환 신경망(RNN)

### 랜덤 시드 고정  

```python
SEED_NUM = 1234
tf.random.set_seed(SEED_NUM)
```

### 학습 데이터 불러오기

```python
DATA_IN_PATH = './'
DATA_OUT_PATH = './'

TRAIN_INPUT_DATA = 'train_input.npy'
TRAIN_LABEL_DATA = 'train_label.npy'
DATA_CONFIGS = 'data_configs.json'

train_input = np.load(open(DATA_IN_PATH + TRAIN_INPUT_DATA, 'rb'))
train_input = pad_sequences(train_input, maxlen=train_input.shape[1])
train_label = np.load(open(DATA_IN_PATH + TRAIN_LABEL_DATA, 'rb'))
prepro_configs = json.load(open(DATA_IN_PATH + DATA_CONFIGS, 'r'))
```

### 모델 하이퍼파라미터 정의

```python
model_name = 'rnn_classifier_en'
BATCH_SIZE = 128
NUM_EPOCHS = 5
VALID_SPLIT = 0.1
MAX_LEN = train_input.shape[1]

kargs = {'model_name': model_name, 
        'vocab_size': prepro_configs['vocab_size'],
        'embedding_dimension': 100,
        'dropout_rate':0.2,
        'lstm_dimension':150,
        'dense_dimension':150,
        'output_dimension': 1}
```

### 모델 구현

```python
class RNNClassifier(tf.keras.Model):
    def __init__(self, **kargs):
        super(RNNClassifier, self).__init__(name=kargs['model_name'])
        
        # e.g. [[4], [20]] -> [[0.25, 0.1], [0.6, -0.2]]
        self.embedding = tf.keras.layers.Embedding(input_dim=kargs['vocab_size'], output_dim=kargs['embedding_dimension'])
        
        self.lstm_1_layer = tf.keras.layers.LSTM(kargs['lstm_dimension'], return_sequences=True)
        self.lstm_2_layer = tf.keras.layers.LSTM(kargs['lstm_dimension'])
        self.dropout = tf.keras.layers.Dropout(kargs['dropout_rate'])
        self.fc1 = tf.keras.layers.Dense(units=kargs['dense_dimension'], activation=tf.keras.activations.tanh)
        self.fc2 = tf.keras.layers.Dense(units=kargs['output_dimension'], activation=tf.keras.activations.sigmoid)
        
    def call(self, x):
        x = self.embedding(x)
        x = self.dropout(x)
        x = self.lstm_1_layer(x)
        x = self.lstm_2_layer(x)
        x = self.dropout(x)
        x = self.fc1(x)
        x = self.dropout(x)
        x = self.fc2(x)
        
        return x
```

### 모델 생성

```python
model = RNNClassifier(**kargs)
model.compile(optimizer=tf.keras.optimizers.Adam(1e-4),
             loss=tf.keras.losses.BinaryCrossentropy(),
             metrics=[tf.keras.metrics.BinaryAccuracy(name='accuracy')])
```

### 모델 학습

```python
earlystop_callback = tf.keras.callbacks.EarlyStopping(monitor='val_accuracy', min_delta=0.0001, patience=2)

checkpoint_path = DATA_OUT_PATH + model_name + '/weights.h5'
checkpoint_dir = os.path.dirname(checkpoint_path)

if os.path.exists(checkpoint_dir):
    print(f"{checkpoint_dir} ==Folder already exists \n")
else:
    os.makedirs(checkpoint_dir, exist_ok=True)
    print(f"{checkpoint_dir} -- Folder create complete \n")

cp_callback = tf.keras.callbacks.ModelCheckpoint(checkpoint_path, monitor='cal_accuracy', verbose=1, save_best_only=True, save_weights_only=True)

history = model.fit(train_input, train_label, batch_size=BATCH_SIZE, epochs=NUM_EPOCHS, validation_split=VALID_SPLIT, callbacks=[earlystop_callback, cp_callback])
-----------------------------------------------------------------------------------------------------------------------------------------------------
Epoch 1/5
176/176 [==============================] - 25s 113ms/step - loss: 0.6932 - accuracy: 0.5033 - val_loss: 0.6929 - val_accuracy: 0.5096
Epoch 2/5
176/176 [==============================] - 19s 109ms/step - loss: 0.6915 - accuracy: 0.5177 - val_loss: 0.4860 - val_accuracy: 0.7576
Epoch 3/5
176/176 [==============================] - 18s 104ms/step - loss: 0.3949 - accuracy: 0.8297 - val_loss: 0.2773 - val_accuracy: 0.8844
Epoch 4/5
176/176 [==============================] - 19s 109ms/step - loss: 0.1899 - accuracy: 0.9298 - val_loss: 0.2784 - val_accuracy: 0.8888
Epoch 5/5
176/176 [==============================] - 19s 107ms/step - loss: 0.1234 - accuracy: 0.9584 - val_loss: 0.2997 - val_accuracy: 0.8872
```

## 합성곱 신경망(CNN)

### 모델 하이퍼파라미터 정의

```python
model_name = 'cnn_classifier_en'
BATCH_SIZE = 512
NUM_EPOCHS = 5
VALID_SPLIT = 0.1
MAX_LEN =train_input.shape[1]

kargs = {'model_name': model_name, 
        'vocab_size': prepro_configs['vocab_size'],
        'embedding_size': 128,
        'num_filters':100,
        'dropout_rate':0.5,
         'hidden_dimension':250,
        'output_dimension': 1}
```

### 모델 구현

```python
class CNNClassifier(tf.keras.Model):
    
    def __init__(self, **kargs):
        super(CNNClassifier, self).__init__(name=kargs['model_name'])
        
        self.embedding = tf.keras.layers.Embedding(input_dim=kargs['vocab_size'], output_dim=kargs['embedding_size'])
        
        self.conv_list = [tf.keras.layers.Conv1D(filters=kargs['num_filters'],
                                                kernel_size=kernel_size,
                                                padding='valid',
                                                activation=tf.keras.activations.relu,
                                                kernel_constraint=tf.keras.constraints.MaxNorm(max_value=3.)) for kernel_size in [3,4,5]]
        
        self.pooling = tf.keras.layers.GlobalMaxPooling1D()
        self.dropout = tf.keras.layers.Dropout(kargs['dropout_rate'])
        self.fc1 = tf.keras.layers.Dense(units=kargs['hidden_dimension'],
                                        activation=tf.keras.activations.relu,
                                        kernel_constraint=tf.keras.constraints.MaxNorm(max_value=3.))
        self.fc2 = tf.keras.layers.Dense(units=kargs['output_dimension'],
                                        activation=tf.keras.activations.sigmoid,
                                        kernel_constraint=tf.keras.constraints.MaxNorm(max_value=3.))
        
    
    def call(self, x):
        x = self.embedding(x)
        x = self.dropout(x)
        x = tf.concat([self.pooling(conv(x)) for conv in self.conv_list], axis=-1)
        x = self.fc1(x)
        x = self.fc2(x)
        
        return x
```

### 모델 생성

```python
model = CNNClassifier(**kargs)
model.compile(optimizer=tf.keras.optimizers.Adam(1e-4),
             loss=tf.keras.losses.BinaryCrossentropy(),
             metrics=[tf.keras.metrics.BinaryAccuracy(name='accuracy')])
```

### 모델 학습

```python
earlystop_callback = tf.keras.callbacks.EarlyStopping(monitor='val_accuracy', min_delta=0.0001, patience=2)

checkpoint_path = DATA_OUT_PATH + model_name + '/weights.h5'
checkpoint_dir = os.path.dirname(checkpoint_path)

if os.path.exists(checkpoint_dir):
    print(f"{checkpoint_dir} ==Folder already exists \n")
else:
    os.makedirs(checkpoint_dir, exist_ok=True)
    print(f"{checkpoint_dir} -- Folder create complete \n")

cp_callback = tf.keras.callbacks.ModelCheckpoint(checkpoint_path, monitor='cal_accuracy', verbose=1, save_best_only=True, save_weights_only=True)

history = model.fit(train_input, train_label, batch_size=BATCH_SIZE, epochs=NUM_EPOCHS, validation_split=VALID_SPLIT, callbacks=[earlystop_callback, cp_callback])
-----------------------------------------------------------------------------------------------------------------------------------------------------
Epoch 1/5
176/176 [==============================] - 25s 133ms/step - loss: 0.6933 - accuracy: 0.5085 - val_loss: 0.6884 - val_accuracy: 0.5452
Epoch 2/5
176/176 [==============================] - 24s 134ms/step - loss: 0.6870 - accuracy: 0.5671 - val_loss: 0.6573 - val_accuracy: 0.7108
Epoch 3/5
176/176 [==============================] - 24s 135ms/step - loss: 0.6248 - accuracy: 0.7423 - val_loss: 0.4921 - val_accuracy: 0.7924
Epoch 4/5
176/176 [==============================] - 23s 132ms/step - loss: 0.4403 - accuracy: 0.8137 - val_loss: 0.3663 - val_accuracy: 0.8508
Epoch 5/5
176/176 [==============================] - 23s 132ms/step - loss: 0.3216 - accuracy: 0.8690 - val_loss: 0.3253 - val_accuracy: 0.8684
```