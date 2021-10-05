---
title:  "[NLP] Hugging face Chap1. Transformers, what can they do?"
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

# Hugging face: Transformers, what can they do?
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

Huggingface는 자연어 처리(NLP)를 위한 생태계(Ecosystem)로 대표적으로 🤗`Transformers`, 🤗`Datasets`, 🤗`Tokenizers`과 같은 라이브러리를 제공합니다.  

## 1. Transformers, what can they do?
🤗Transformers 라이브러리는 모델을 만들거나 공유된 모델을 쉽게 사용할 수 있도록 해줍니다. [Model Hub](https://huggingface.co/models)에는 수천개의 pretrained model을 제공하고 있습니다. 그리고 원한다면 자신이 만든 모델을 Hub에 공유할 수도 있습니다.    

이번 포스트에서는, 🤗Transformers 라이브러리의 중요한 도구중 하나인 `pipeline API`를 이용해 몇 가지 흥미로운 NLP task를 푸는 예시를 보도록 하겠습니다.  

🔔 __pipeline__   
> The most basic object in the 🤗Transformers library is the `pipeline`. It `connects a model with its necessary preprocessing and postprocessing steps`.  

> By default, this `pipeline selects a particular pretrained model that has been fine-tuned for specific task`. The model is downloaded and `cached when you create object`. If you rerun the command, the cached model will be used instead and `there is no need to download the model again`.

> There are three main steps involved when you pass some text to a pipeline:

- The text is preprocessed into a format the model can understand.
- The preprocessed inputs are passed to the model.
- The predictions of the model are post-processed, so you can make sense of them.



### 1) Mask Filling  

```python
from transformers import pipeline

unmasker = pipeline("fill-mask")
unmasker("This course will teach you all about <mask> models.", top_k=2)
-------------------------------------------------------------------------
[{'sequence': 'This course will teach you all about mathematical models.',
  'score': 0.19619831442832947,
  'token': 30412,
  'token_str': ' mathematical'},
 {'sequence': 'This course will teach you all about computational models.',
  'score': 0.04052725434303284,
  'token': 38163,
  'token_str': ' computational'}]
```

### 2) Question answering

```python
from transformers import pipeline

question_answerer = pipeline("question-answering")
question_answerer(
    question="Where do I work?",
    context="My name is Sylvain and I work at Hugging Face in Brooklyn"
)
--------------------------------------------------------
{'score': 0.6385916471481323, 'start': 33, 'end': 45, 'answer': 'Hugging Face'}
```

### 3) Sentiment analysis

```python
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
classifier("I've been waiting for a HuggingFace course my whole life.")
----------------------------------------------------------------------
[{'label': 'POSITIVE', 'score': 0.9598047137260437}]
```