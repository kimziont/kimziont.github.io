---
title:  "[NLP] Hugging face Chap3. Fine-tuning a pretrained model"
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

# Hugging face: Fine-tuning a pretrained model
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 Pretrained된 모델을 나의 task에 맞게 Fine-tuning하는 방법에 대해 알아보겠습니다.🤗  

## 1. Processing data

```python
import torch
from transformers import AdamW, AutoTokenizer, AutoModelForSequenceClassification

# Same as before
checkpoint = "bert-base-uncased"
tokenizer = AutoTokenizer.from_pretrained(checkpoint)
model = AutoModelForSequenceClassification.from_pretrained(checkpoint)
sequences = [
    "I've been waiting for a HuggingFace course my whole life.",
    "This course is amazing!",
]
batch = tokenizer(sequences, padding=True, truncation=True, return_tensors="pt")

# This is new
batch["labels"] = torch.tensor([1, 1])

optimizer = AdamW(model.parameters())
loss = model(**batch).loss
loss.backward()
optimizer.step()
```  

Of course, just training the model on two sentences is not going to yield very good results. To get better results, you will need to prepare a bigger dataset.

In this section we will use as an example the __MRPC (Microsoft Research Paraphrase Corpus) dataset__, introduced in a paper by William B. Dolan and Chris Brockett. The dataset consists of 5,801 pairs of sentences, with a label indicating if they are paraphrases or not (i.e., if both sentences mean the same thing). We’ve selected it for this chapter because it’s a small dataset, so it’s easy to experiment with training on it.  

The Hub doesn’t just contain models; it also has multiple datasets in lots of different languages. You can browse the datasets here, and we recommend you try to load and process a new dataset once you have gone through this section (see the general documentation here). But for now, let’s focus on the MRPC dataset! This is one of the 10 datasets composing the GLUE benchmark, which is an academic benchmark that is used to measure the performance of ML models across 10 different text classification tasks.

The 🤗`Datasets` library provides a very simple command to download and cache a dataset on the Hub. We can download the MRPC dataset like this:  

```python
from datasets import load_dataset

raw_datasets = load_dataset("glue", "mrpc")

raw_datasets
----------------------------------------------------
DatasetDict({
    train: Dataset({
        features: ['sentence1', 'sentence2', 'label', 'idx'],
        num_rows: 3668
    })
    validation: Dataset({
        features: ['sentence1', 'sentence2', 'label', 'idx'],
        num_rows: 408
    })
    test: Dataset({
        features: ['sentence1', 'sentence2', 'label', 'idx'],
        num_rows: 1725
    })
})
```  

This command downloads and caches the dataset, by default in ~/.cache/huggingface/dataset. Recall from Chapter 2 that you can customize your cache folder by setting the __HF_HOME__ environment variable.  

We can access each pair of sentences in our raw_datasets object by indexing, like with a dictionary:

```python
raw_train_dataset = raw_datasets["train"]

raw_train_dataset[0]
----------------------------------------------------
{'idx': 0,
 'label': 1,
 'sentence1': 'Amrozi accused his brother , whom he called " the witness " , of deliberately distorting his evidence .',
 'sentence2': 'Referring to him as only " the witness " , Amrozi accused his brother of deliberately distorting his evidence .'}
```  

We can inspect the features of our raw_train_dataset. This will tell us the type of each column:  

```python
raw_train_dataset.features
---------------------------------------------------------------------------------------------------
{'sentence1': Value(dtype='string', id=None),
 'sentence2': Value(dtype='string', id=None),
 'label': ClassLabel(num_classes=2, names=['not_equivalent', 'equivalent'], names_file=None, id=None),
 'idx': Value(dtype='int32', id=None)}
```

## 2. Dataset.map

Now that we have seen how our tokenizer can deal with one pair of sentences, we can use it to tokenize our whole dataset: like in the previous chapter, we can feed the tokenizer a list of pairs of sentences by giving it the list of first sentences, then the list of second sentences. This is also compatible with the padding and truncation options we saw in Chapter 2. So, one way to preprocess the training dataset is:  

```python
tokenized_dataset = tokenizer(
    raw_datasets["train"]["sentence1"],
    raw_datasets["train"]["sentence2"],
    padding=True,
    truncation=True,
)
```  

This works well, but it has the `disadvantage of returning a dictionary` (with our keys, input_ids, attention_mask, and token_type_ids, and values that are lists of lists). It will also `only work if you have enough RAM to store your whole dataset during the tokenization` (whereas the datasets from the 🤗`Datasets` library are Apache Arrow files stored on the disk, so you only keep the samples you ask for loaded in memory).

To keep the data as a dataset, we will use the [__Dataset.map__](https://huggingface.co/docs/datasets/package_reference/main_classes.html#datasets.Dataset.map) method. This also allows us some `extra flexibility`, if we need more preprocessing done than just tokenization. The map method works by applying a function on each element of the dataset, so let’s define a function that tokenizes our inputs:  

```python
def tokenize_function(example):
    return tokenizer(example["sentence1"], example["sentence2"], truncation=True)

tokenized_datasets = raw_datasets.map(tokenize_function, batched=True)

tokenized_datasets
--------------------------------------------------------------------------------------
DatasetDict({
    train: Dataset({
        features: ['attention_mask', 'idx', 'input_ids', 'label', 'sentence1', 'sentence2', 'token_type_ids'],
        num_rows: 3668
    })
    validation: Dataset({
        features: ['attention_mask', 'idx', 'input_ids', 'label', 'sentence1', 'sentence2', 'token_type_ids'],
        num_rows: 408
    })
    test: Dataset({
        features: ['attention_mask', 'idx', 'input_ids', 'label', 'sentence1', 'sentence2', 'token_type_ids'],
        num_rows: 1725
    })
})
```  

This function takes a dictionary (like the items of our dataset) and returns a new dictionary with the keys input_ids, attention_mask, and token_type_ids. This will allow us to use the option __batched=True__ in our call to __map__, which will greatly speed up the tokenization. The tokenizer is backed by a tokenizer written in Rust from the 🤗Tokenizers library. This tokenizer can be very fast, but only if we give it lots of inputs at once.  

We’re using batched=True in our call to map so the function is applied to multiple elements of our dataset at once, and not on each element separately. This allows for faster preprocessing.

Note that we’ve left the padding argument out in our tokenization function for now. This is because padding all the samples to the maximum length is not efficient: it’s better to pad the samples when we’re building a batch, as then we only need to pad to the maximum length in that batch, and not the maximum length in the entire dataset. This can save a lot of time and processing power when the inputs have very variable lengths!

(패딩 작업은 전체 데이터 셋의 최대 길이를 기준으로 하는 것 보다, 각 배치의 최대 길이를 기준으로 하는 것이 효율적이다)  

The last thing we will need to do is pad all the examples to the length of the longest element when we batch elements together — a technique we refer to as `dynamic padding`.  

To do this in practice, we have to define a collate function that will apply the correct amount of padding to the items of the dataset we want to batch together. Fortunately, the 🤗 Transformers library provides us with such a function via DataCollatorWithPadding. It takes a tokenizer when you instantiate it (to know which padding token to use, and whether the model expects padding to be on the left or on the right of the inputs) and will do everything you need:  

```python
from transformers import DataCollatorWithPadding

data_collator = DataCollatorWithPadding(tokenizer=tokenizer)
```

To test this new toy, let’s grab a few samples from our training set that we would like to batch together. Here, we remove the columns idx, sentence1, and sentence2 as they won’t be needed and contain strings (and we can’t create tensors with strings) and have a look at the lengths of each entry in the batch:  

```python
samples = tokenized_datasets["train"][:8]
samples = {
    k: v for k, v in samples.items() if k not in ["idx", "sentence1", "sentence2"]
}
[len(x) for x in samples["input_ids"]]
---------------------------------------------------------------------------------
[50, 59, 47, 67, 59, 50, 62, 32]
```

No surprise, we get samples of varying length, from 32 to 67. Dynamic padding means the samples in this batch should all be padded to a length of 67, `the maximum length inside the batch`. Without dynamic padding, all of the samples would have to be padded to the maximum length in the whole dataset, or the maximum length the model can accept. Let’s double-check that our data_collator is dynamically padding the batch properly:

```python
batch = data_collator(samples)

{k: v.shape for k, v in batch.items()}
----------------------------------------------
{'attention_mask': torch.Size([8, 67]),
 'input_ids': torch.Size([8, 67]),
 'token_type_ids': torch.Size([8, 67]),
 'labels': torch.Size([8])}
```