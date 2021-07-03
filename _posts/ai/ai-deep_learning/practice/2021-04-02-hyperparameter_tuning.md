---
title:  "[Deep_learning-Practice] 신경망 하이퍼파라미터 튜닝하기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - dl_practice
tags:
  - AI
last_modified_at: 2021-04-02
---


## 1. 하이퍼파라미터 튜닝하기
신경망의 유연성은 단점이기도 합니다. 조정할 하이퍼파라미터가 많기 때문입니다.  
대표적인 하이퍼파라미터  
`신경망 층의 개수`  
`층마다 뉴런의 개수`  
`각 층의 활성화 함수`
`학습률`  
`배치 크기`  
`옵티마이저`  

GridSearchCV, RandomizedSearchCV를 사용해 하이퍼파라미터 공간을 탐색할 수 있습니다. 이렇게 하려면 케라스 모델을 사이킷런 추정기처럼 보이도록 바꾸어야 합니다.  

```python
def build_model(n_hidden=1, n_neurons=30, learning_rate=3e-3, input_shape=[8]):
    model = keras.models.Sequential()
    model.add(keras.layers.InputLayer(input_shape=input_shape))
    for layer in range(n_hidden):
        model.add(keras.layers.Dense(n_neurons, activation="relu"))
    model.add(keras.layers.Dense(1))
    optimizer = keras.optimizers.SGD(lr=learning_rate)
    model.compile(loss="mse", optimizer=optimizer)
    return model
```  

```python
keras_reg = keras.wrappers.scikit_learn.KerasRegressor(build_model)
```  

KearsRegressor 객체는 build_model()함수로 만들어진 케라스 모델을 감싸는 간단한 래퍼(wrapper)입니다. 이제 일반적인 사이킷런 회귀 추정기처럼 이 객체를 사용할 수 있습니다.  

```python
from scipy.stats import reciprocal
from sklearn.model_selection import RandomizedSearchCV

param_distribs = {
    "n_hidden": [0, 1, 2, 3],
    "n_neurons": np.arange(1, 100).tolist(),
    "learning_rate": reciprocal(3e-4, 3e-2).rvs(1000).tolist(),
}

rnd_search_cv = RandomizedSearchCV(keras_reg, param_distribs, n_iter=10, cv=3, verbose=2)
rnd_search_cv.fit(X_train, y_train, epochs=100,
                  validation_data=(X_valid, y_valid),
                  callbacks=[keras.callbacks.EarlyStopping(patience=10)])
```  

```python
>>> rnd_search_cv.best_params_ 
{'n_neurons': 74, 'n_hidden': 3, 'learning_rate': 0.005803602934201024} 

>>> rnd_search_cv.best_score_  
-0.32437342405319214

>>> rnd_search_cv.best_estimator_  
<tensorflow.python.keras.wrappers.scikit_learn.KerasRegressor at 0x2152af4b108>

>>> rnd_search_cv.score(X_test, y_test)  
162/162 [==============================] - 0s 948us/step - loss: 0.2800
-0.2800329327583313

>>> model = rnd_search_cv.best_estimator_.model  
>>> model
<tensorflow.python.keras.engine.sequential.Sequential at 0x2150cde90c8>

>>> model.evaluate(X_test, y_test)
162/162 [==============================] - 0s 954us/step - loss: 0.2800
0.2800329327583313
```  

랜덤 탐색을 사용하는 것은 크게 어렵지 않으며 간단한 문제에서 잘 동작합니다. 하지만 훈련에 시간이 많이 걸리면 탐색할 수 있는 하이퍼파라미터 공간에 제약이 생깁니다. 다행히 효율적으로 하이퍼파라미터 공간을 탐색하는 여러 기법이 있습니다. 핵심적인 아이디어는 `탐색 지역이 좋다고 판명될 때 더 탐색을 수행하는 것입니다.`  
다음은 하이퍼 파라미터 최적화에 사용할 수 있는 몇 개의 파이썬 라이브러리입니다.  
`Hyperopt`  
`Hyperas, lopt, Talos`  
`Keras Tuner`  
`Scikit-Optimize(skopt)`  
`Spearmint`  
`Hyperband`  
`Sklearn-Deap`  

마지막으로 구글의 경우 하이퍼파라미터 탐색뿐만 아니라 문제에 최적인 신경망 구조를 찾기 위해 진화 전략을 사용합니다. 구글의 AutoML 서비스(https://cloud.google.com/automl)는 이미 클라우드로 제공됩니다. 아마도 신경망을 수동으로 만드는 시대는 곧 막을 내리지 않을까요?

## 2. 하이퍼 파라미터 튜닝의 범위를 결정하기 위해  
탐색 공간을 적당한 범위로 제한하기 위해 각 하이퍼파라미터에 어떤 값이 적절한지 판단할 수 있는 것이 좋습니다.  

### 은닉층 개수  
이론적으로 은닉층은 하나만 있어도 뉴런 개수가 충분하면 어떤 복잡한 함수도 모델링 할 수 있다고 합니다. 하지만 복잡한 문제에서는 심층 신경망이 얕은 신경망보다 파라미터 효율성이 훨씬 좋습니다. 심층 신경망은 얕읕 신경망보다 적은 수의 뉴런을 사용할 수 있어 동일한 양의 훈련 데이터에서 더 높은 성능을 낼 수 있습니다.  
왜 그런지 이해하기 위해 예를 들어보겠습니다. 드로잉 소프트웽로 숲을 그린다고 가정해봅시다. 나무, 가지, 잎을 하나하나 전부 그려야 한다면 시간이 아주 많이 걸릴 것입니다. 하지만 만약 나무, 가지, 잎을 그려놓고 복사/붙여넣기할 수 있다면 금세 일을 마칠 수 있을 겁니다. 실제 데이터는 이런 계층 구조를 가진 경우가 많으므로 심층 신경망은 이런 면에서 유리합니다. 아래쪽 은닉층은 저수준의 구조를 모델링 하고(선), 중간 은닉층의 저수준의 구조를 연결해 중간 수준의 구조를 모델링(사각형, 원)합니다. 그리고 가장 위쪽 은닉층은 중간 수준의 구조를 연결해 고수준의 구조를 모델링합니다(얼굴)  
요약하면, 복잡한 문제라면 과대적합 될 만큼 은닉층의 수를 점진적으로 늘립니다(수십~수백) 그리고 추가적인 규제를 주는 것이 훨씬 성능이 뛰어납니다.   

### 은닉층의 뉴런 개수  
뉴런의 개수는 해당 작업에 필요한 입력과 출력의 형태에 따라 다릅니다. 예전에는 은닉층의 뉴런 개수는 보통 많은 뉴런의 수에서 점점 뉴런 수를 줄이는 깔때기 모양으로 구성했습니다. 왜냐하면 저수준의 많은 특성이 고수준의 적은 특성으로 합쳐질 수 있기 때문입니다. 하지만 요즘에는 모든 은닉층의 뉴런 개수를 통일시킵니다. 그러면 각 층 별로 뉴런 개수를 달리할 필요가 없으니 하이퍼파라미터의 수가 줄어드는 셈이고 성능 또한 동일하거나 더 나은  성능을 냅니다.  
요약하면 층의 개수와 마찬가지로 과대적합이 될 정도로 많은 층과 뉴런을 가진 모델을 선택하고, 조기 종료나 규제 기법을 사용하는 것이 간단하고 효과적입니다.  

### 학습률  
일반적으로 최적의 학습률은 최대 학습률의 절반 정도입니다.  좋은 학습률을 찾는 한 가지 방법은 매우 낮은 학습률에서 점진적으로 큰 학습률까지 수백 번 반복하여 모델을 훈련하는 것입니다.

### 배치크기  
많은 연구자들과 기술자들은 GPU 램에 맞는 가장 큰 배치 크기를 사용하라고 권장합니다. 하지만 어떤 사람들은 작은 배치가 적은 훈련 시간으로 더 좋은 모델을 만들기 때문에 작은 배치(2~32)를 사용하는 것이 바람직하다고도 합니다. 또 어떤 사람들은 학습률 예열과 같은 다양한 기법을 사용하면 매우 큰 배치 크기(8192)를 사용할 수 있다고 밝혔습니다.

### 옵티마이저  
미니 배치 경사 하강법보다 더 좋은 옵티마이저가 있습니다. (Ada, Adam, Nadam 등)

### 활성화함수  
일반적으로 은닉층에서 ReLU활성화 함수를 기본으로 사용하고 출력층은 수행하는 작업에 따라 다릅니다.
