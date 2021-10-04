---
title:  "[NLP] Hugging face 공부하기 5일차: Models"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - nlp_huggingface
tags:
  - NLP
last_modified_at: 2021-10-04
---  

# Hugging face: Models
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 모델을 구성하는 방법에 대해 조금 더 자세히 알아보겠습니다.🤗    

## 1. Creating a Model from scratch

In this section we’ll take a closer look at creating and using a model. We’ll use the `AutoModel class`, which is handy when you want to instantiate any model from a checkpoint.

The AutoModel class and all of its relatives are actually `simple wrappers over the wide variety of models available in the library`. It’s a clever wrapper as it can `automatically guess the appropriate model architecture for your checkpoint`, and then instantiates a model with this architecture.

However, if you know the type of model you want to use, you can use the class that defines its architecture directly. Let’s take a look at how this works with a BERT model.

```python
from transformers import BertConfig, BertModel

# Building the config
config = BertConfig()

# Building the model from the config
model = BertModel(config)

print(config)
--------------------------------------------
BertConfig {
  "attention_probs_dropout_prob": 0.1,
  "classifier_dropout": null,
  "hidden_act": "gelu",
  "hidden_dropout_prob": 0.1,
  "hidden_size": 768,
  "initializer_range": 0.02,
  "intermediate_size": 3072,
  "layer_norm_eps": 1e-12,
  "max_position_embeddings": 512,
  "model_type": "bert",
  "num_attention_heads": 12,
  "num_hidden_layers": 12,
  "pad_token_id": 0,
  "position_embedding_type": "absolute",
  "transformers_version": "4.11.2",
  "type_vocab_size": 2,
  "use_cache": true,
  "vocab_size": 30522
}
```  

## 2. Loading Pretrained Model

The model can be used in this state, but it will output gibberish; it needs to be trained first. We could train the model from scratch on the task at hand, but as you saw in Chapter 1, this would `require a long time and a lot of data`, and it would have a non-negligible environmental impact. To avoid unnecessary and duplicated effort, it’s imperative to be able to share and reuse models that have already been trained.

Loading a Transformer model that is already trained is simple — we can do this using the `from_pretrained method`:  

```python
from transformers import BertModel

model = BertModel.from_pretrained("bert-base-cased")
```  

As you saw earlier, we could replace BertModel with the equivalent `AutoModel class`. We’ll do this from now on as this produces `checkpoint-agnostic code`; if your code works for one checkpoint, it should work seamlessly with another.  

In the code sample above `we didn’t use BertConfig`, and instead loaded a pretrained model via the bert-base-cased identifier. This is a model checkpoint that was trained by the authors of BERT themselves  

(모델을 학습시킬 것이 아니기 때문에, config를 정의할 필요가 없다, 학습에 사용된 config를 확인해보고 싶다면)  

```python
model.config
----------------------------------------------
DistilBertConfig {
  "_name_or_path": "distilbert-base-uncased-finetuned-sst-2-english",
  "activation": "gelu",
  "architectures": [
    "DistilBertForSequenceClassification"
  ],
  "attention_dropout": 0.1,
  "dim": 768,
  "dropout": 0.1,
  "finetuning_task": "sst-2",
  "hidden_dim": 3072,
  "id2label": {
    "0": "NEGATIVE",
    "1": "POSITIVE"
  },
  "initializer_range": 0.02,
  "label2id": {
    "NEGATIVE": 0,
    "POSITIVE": 1
  },
  "max_position_embeddings": 512,
  "model_type": "distilbert",
  "n_heads": 12,
  "n_layers": 6,
  "output_past": true,
  "pad_token_id": 0,
  "qa_dropout": 0.1,
  "seq_classif_dropout": 0.2,
  "sinusoidal_pos_embds": false,
  "tie_weights_": true,
  "transformers_version": "4.11.2",
  "vocab_size": 30522
}
```  

The weights have been downloaded and cached (so future calls to the from_pretrained method won’t re-download them) in the cache folder, which defaults to ~/.cache/huggingface/transformers. You can customize your cache folder by setting the __HF_HOME__ environment variable.  

## 3. Save Model

```python
model.save_pretrained("directory_on_my_computer")  
```

This saves two files to your disk:  

```
config.json

pytorch_model.bin
```  

If you take a look at the `config.json` file, `you’ll recognize the attributes necessary to build the model architecture.` This file `also contains some metadata`, such as where the checkpoint originated and what 🤗Transformers version you were using when you last saved the checkpoint.  

The `pytorch_model.bin` file is known as the `state dictionary`; it contains all your `model’s weights`. The two files go hand in hand; the configuration is necessary to know your model’s architecture, while the model weights are your model’s parameters.