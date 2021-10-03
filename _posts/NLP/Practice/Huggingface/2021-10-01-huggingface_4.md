---
title:  "[NLP] Hugging face 공부하기 4일차: Behind the pipeline"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - nlp_huggingface
tags:
  - NLP
last_modified_at: 2021-10-01
---  

# Hugging face 공부하기 4일차: Behind the pipeline
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

앞에서 봤던 pipeline API는 간단하고 나의 task에 바로 적용할 수 있지만, 모델을 나의 task에 알맞게 조금 더 수정하고 싶다면 내부를 들여다 볼 필요가 있습니다. 이번 포스트에서는 pipeline API 내부를 조금 더 자세히 알아보도록 하겠습니다.  

## 1. Introduction
Transformer 기반의 모델들은 크기가 굉장히 크고, 훈련, 배포가 쉽지 않습니다. 게다가 최근에는 Transformer기반의 새로운 모델들이 계속 나오고 있기 때문에, 그 때마다 모델을 훈련하는 방법을 익혀야 한다면 굉장히 힘들 것 입니다. 다행히 Huggingface는 이러한 점들을 염두해두고 Library를 개발했기 때문했습니다. 다음은 Huggingface에서 제공하는 Library들의 철학입니다.  

- __Ease of use__: Downloading, loading, and using a state-of-the-art NLP model for inference can be done in just two lines of code.
- __Flexibility__: At their core, all models are simple PyTorch nn.Module or TensorFlow tf.keras.Model classes and can be handled like any other models in their respective machine learning (ML) frameworks.
- __Simplicity__: Hardly any abstractions are made across the library. The “All in one file” is a core concept: a model’s forward pass is entirely defined in a single file, so that the code itself is understandable and hackable.

![](/assets/images/huggingface_8.png){: width="100%" height="70%"}  

## 2. Tokenizer  

Like other neural networks, Transformer models can’t process raw text directly, so the first step of our pipeline is to `convert the text inputs into numbers that the model can make sense of`.  

Tokenizing needs to be done in exactly the `same way as when the model was pretrained`, so we first need to download that information from the Model Hub. To do this, we use the AutoTokenizer class and its from_pretrained method. Using the checkpoint name of our model, it will automatically fetch the data associated with the model’s tokenizer and cache it  

```python
from transformers import AutoTokenizer

# 우리가 사용할 모델의 이름
checkpoint = "distilbert-base-uncased-finetuned-sst-2-english"

# checkpoint가 학습할 때 사용되었던 Tokenizer가 불러와진다(downloaded and cached)
tokenizer = AutoTokenizer.from_pretrained(checkpoint)
```  

Once we have the tokenizer, `we can directly pass our sentences to it` and `we’ll get back a dictionary that’s ready to feed to our model!` The only thing left to do is to `convert the list of input IDs to tensors`.  

```python
raw_inputs = [
    "I've been waiting for a HuggingFace course my whole life.", 
    "I hate this so much!",
]

# return_tensors -> if no type is passed, you will get a list of lists as a result
inputs = tokenizer(raw_inputs, padding=True, truncation=True, return_tensors="pt")
print(inputs)
----------------------------------------------------------------------------------------
{
    'input_ids': tensor([
        [  101,  1045,  1005,  2310,  2042,  3403,  2005,  1037, 17662, 12172, 2607,  2026,  2878,  2166,  1012,   102],
        [  101,  1045,  5223,  2023,  2061,  2172,   999,   102,     0,     0,     0,     0,     0,     0,     0,     0]
    ]), 
    'attention_mask': tensor([
        [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
        [1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0]
    ])
}
```

## 3. Going through the model

```python
from transformers import AutoModel

checkpoint = "distilbert-base-uncased-finetuned-sst-2-english"
model = AutoModel.from_pretrained(checkpoint)
```