---
title:  "[NLP] Hugging face Chap3. Trainer API"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - nlp_huggingface
tags:
  - NLP
last_modified_at: 2021-10-05
---  

# Hugging face: Trainer API
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 Pretrained된 모델을 나의 task에 맞게 Fine-tuning할 때 학습시키는 방법에 대해 알아보겠습니다.🤗  

## 1. Training
🤗Transformers provides a `Trainer class` to help you fine-tune any of the pretrained models on your dataset. Once you’ve done all the data preprocessing work in the last section, `you have just a few steps left to define the Trainer.` The hardest part is likely to be `preparing the environment to run Trainer.train`, as it will run very slowly on a CPU. If you don’t have a GPU set up, you can get access to free GPUs or TPUs on Google Colab.  

The first step before we can define our Trainer is to `define a TrainingArguments class` that will contain all the hyperparameters the Trainer will use for training and evaluation. The only argument you have to provide is a directory where the trained model will be saved, as well as the checkpoints along the way. For all the rest, you can leave the defaults, which should work pretty well for a basic fine-tuning.  

```python
from transformers import TrainingArguments

training_args = TrainingArguments("test-trainer")
```

```python
from transformers import AutoModelForSequenceClassification

model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)
```

Once we have our model, we can define a Trainer by passing it all the objects constructed up to now — the __model__, the __training_args__, the __training and validation datasets__, our __data_collator__, and our __tokenizer__:  

```python
from transformers import Trainer

trainer = Trainer(
    model,
    training_args,
    train_dataset=tokenized_datasets["train"],
    eval_dataset=tokenized_datasets["validation"],
    data_collator=data_collator,
    tokenizer=tokenizer,
)
```  

To fine-tune the model on our dataset, we just have to call the __train__ method of our Trainer:  

```python
trainer.train()
```  

This will start the fine-tuning (which should take a couple of minutes on a GPU) and report the training loss every 500 steps. It won’t, however, tell you how well (or badly) your model is performing. This is because:  

- We didn’t tell the __Trainer__ to evaluate during training by setting __evaluation_strategy__ to either __"steps"__ or __"epoch"__.  

- We didn’t provide the __Trainer__ with a __compute_metrics__ function to calculate a metric during said evaluation (otherwise the evaluation would just have printed the loss, which is not a very intuitive number).  

## 2. Evaluation  

Let’s see how we can build a useful __compute_metrics__ function and use it the next time we train. The function must take an __EvalPrediction__ object (which is a named tuple with a __predictions__ field and a __label_ids__ field) and will return a dictionary mapping strings to floats (the strings being the names of the metrics returned, and the floats their values). To get some predictions from our model, we can use the __Trainer.predict__ command:  

```python
predictions = trainer.predict(tokenized_datasets["validation"])

print(predictions.predictions.shape, predictions.label_ids.shape)
----------------------------------------------------------------
(408, 2) (408,)
```  

The output of the predict method is named tuple with three fields: __predictions__, __label_ids__, and __metrics__. The __metrics__ field will just contain the loss on the dataset passed, as well as some time metrics (how long it took to predict, in total and on average). Once we complete our __compute_metrics__ function and pass it to the __Trainer__, that field will also contain the metrics returned by __compute_metrics__.  

As you can see, __predictions__ is a two-dimensional array with shape 408 x 2 (408 being the number of elements in the dataset we used). Those are the logits for each element of the dataset we passed to predict. To transform them into predictions that we can compare to our labels, we need to take the index with the maximum value on the second axis:

```python
import numpy as np
preds = np.argmax(predictions.predictions, axis=-1)
```  

We can now compare those __preds__ to the labels. To build our __compute_metric__ function, we will rely on the metrics from the 🤗`Datasets` library. We can load the metrics associated with the MRPC dataset as easily as we loaded the dataset, this time with the __load_metric__ function. The object returned has a compute method we can use to do the metric calculation:  

```python
from datasets import load_metric

metric = load_metric("glue", "mrpc")
metric.compute(predictions=preds, references=predictions.label_ids)
------------------------------------------------------------------------
{'accuracy': 0.8578431372549019, 'f1': 0.8996539792387542}
```  

Wrapping everything together, we get our __compute_metrics__ function:  

```python
def compute_metrics(eval_preds):
    metric = load_metric("glue", "mrpc")
    logits, labels = eval_preds
    predictions = np.argmax(logits, axis=-1)
    return metric.compute(predictions=predictions, references=labels)
```

And to see it used in action to `report metrics at the end of each epoch`, here is how we define a new __Trainer__ with this __compute_metrics__ function:  

```python
training_args = TrainingArguments("test-trainer", evaluation_strategy="epoch")

model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)

trainer = Trainer(
    model,
    training_args,
    train_dataset=tokenized_datasets["train"],
    eval_dataset=tokenized_datasets["validation"],
    data_collator=data_collator,
    tokenizer=tokenizer,
    compute_metrics=compute_metrics
)

trainer.train()
```