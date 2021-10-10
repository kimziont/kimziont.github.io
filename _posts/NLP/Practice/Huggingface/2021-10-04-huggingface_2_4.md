---
title:  "[NLP] Hugging face Chap2. Handling multiple sequences"
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

# Hugging face: Handling multiple sequences
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 여러 문장을 입력으로 사용할 때 생긱는 문제점과 이를 해결하는 방법에 대해 알아보도록 하겠습니다.🤗  

## 1. Multiple sentences  

In the previous section, we explored the simplest of use cases: doing inference on a single sequence of a small length. However, some questions emerge already:

- How do we handle multiple sequences?
- How do we handle multiple sequences of different lengths?
- Are vocabulary indices the only inputs that allow a model to work well?
- Is there such a thing as too long a sequence?  

Let’s see what kinds of problems these questions pose, and how we can solve them using the 🤗Transformers API.    

## 2. Models expect a batch of inputs  

```python
import torch
from transformers import AutoTokenizer, AutoModelForSequenceClassification

checkpoint = "distilbert-base-uncased-finetuned-sst-2-english"
tokenizer = AutoTokenizer.from_pretrained(checkpoint)
model = AutoModelForSequenceClassification.from_pretrained(checkpoint)

sequence = "I've been waiting for a HuggingFace course my whole life."

tokens = tokenizer.tokenize(sequence)
ids = tokenizer.convert_tokens_to_ids(tokens)
input_ids = torch.tensor(ids)
# This line will fail.
model(input_ids)
--------------------------------------------------------------------
IndexError: Dimension out of range (expected to be in range of [-1, 0], but got 1)
```  

Oh no! Why did this fail?  

The problem is that we sent a single sequence to the model, whereas 🤗Transformers models expect multiple sentences by default. Here we tried to do everything the tokenizer did behind the scenes when we applied it to a sequence.  

(tokenizer가 하는 일을 tokenizer.tokenize()와 tokenizer.convert_tokens_to_ids()를 통해 세분화하여 모델에 넣어봤지만 오류가 났습니다. 그 이유는 모델은 기본적으로 여러 문장이 들어오기를 기대하고 있기 때문입니다. 그래서 만약에 세분화해서 직접 한다면 마지막에 차원을 추가해줘야 합니다. tokenizer는 기본적으로 이 기능을 제공합니다. 밑에 예시를 확인해보겠습니다.)  

Here we tried to do everything the tokenizer did behind the scenes when we applied it to a sequence, but if you look closely, you’ll see that it didn’t just convert the list of input IDs into a tensor, it `added a dimension on top of it`:  

```python
tokenized_inputs = tokenizer(sequence, return_tensors="pt")
print(tokenized_inputs["input_ids"])
---------------------------------------------------------------------------
# 하나의 문장이 들어왔어도 자동으로 차원이 추가되어 2차원이 되었습니다. -> 문장 수 * 토큰 수
tensor([[  101,  1045,  1005,  2310,  2042,  3403,  2005,  1037, 17662, 12172,
          2607,  2026,  2878,  2166,  1012,   102]])
```



Batching is the act of sending multiple sentences through the model  

Batching allows the model to work when you feed it multiple sentences. Using multiple sequences is just as simple as building a batch with a single sequence. There’s a second issue, though. When you’re trying to batch together two (or more) sentences, they might be of `different lengths`. If you’ve ever worked with tensors before, you know that they need to be of `rectangular shape`. To work around this problem, we usually `pad` the inputs.  

## 3. Padding inputs

The following list of lists cannot be converted to a tensor:  

```python
batched_ids = [
  [200, 200, 200],
  [200, 200]
]
```

we’ll use padding to make our tensors have a rectangular shape. Padding makes sure all our sentences have the same length by adding a special word called the padding token to the sentences with fewer values.  

```python
padding_id = 100

batched_ids = [
  [200, 200, 200],
  [200, 200, padding_id]
]
```  

The padding token ID can be found in __tokenizer.pad_token_id__. Let’s use it and send our two sentences through the model individually and batched together:

```python
model = AutoModelForSequenceClassification.from_pretrained(checkpoint)

# 하나씩 넣어보자
sequence1_ids = [[200, 200, 200]]
sequence2_ids = [[200, 200]]

# batch로 묶어서 보내보자
batched_ids = [[200, 200, 200], [200, 200, tokenizer.pad_token_id]]

# 각 결과를 보자
print(model(torch.tensor(sequence1_ids)).logits)
print(model(torch.tensor(sequence2_ids)).logits)
print(model(torch.tensor(batched_ids)).logits)
--------------------------------------------------------------------------
tensor([[ 1.5694, -1.3895]], grad_fn=<AddmmBackward>)

tensor([[ 0.5803, -0.4125]], grad_fn=<AddmmBackward>)

tensor([[ 1.5694, -1.3895],
        [ 1.3373, -1.2163]], grad_fn=<AddmmBackward>)

# 두 번째 sentence의 값이 다르다 -> padding이 attention 연산에 참여했기 때문에
# 같기를 원한다
```

여기서, 두 번째 sentence의 텐서 값이 달라졌습니다. 왜냐하면 batch단위로 넣을 때 padding을 해주었고 이 값이 attention 계산에 함께 포함되었기 때문입니다. 같은 문장이 model에서 나올 때 다른 값이 나오면 안되기 때문에 저희는 이를 해결하기 위해 padding은 attention 계산에 포함되지 않도록 할 것입니다.  

`To get the same result` when passing individual sentences of different lengths through the model or when passing a batch with the same sentences and padding applied, we need to tell those attention layers to ignore the padding tokens. This is done by `using an attention mask`.  

## 4. Attention masks  

Attention masks are tensors with the exact same shape as the input IDs tensor, filled with 0s and 1s: 1s indicate the corresponding tokens should be attended to, and 0s indicate the corresponding tokens should not be attended to  

```python
batched_ids = [
    [200, 200, 200],
    [200, 200, tokenizer.pad_token_id]
]

attention_mask = [
  [1, 1, 1],
  [1, 1, 0]
]

outputs = model(torch.tensor(batched_ids), attention_mask=torch.tensor(attention_mask))
print(outputs.logits)
-----------------------------------------
tensor([[ 1.5694, -1.3895],
        [ 0.5803, -0.4125]], grad_fn=<AddmmBackward>)
```

## 5. Longer sequences

With Transformer models, there is a limit to the lengths of the sequences we can pass the models. Most models handle sequences of up to 512 or 1024 tokens, and will crash when asked to process longer sequences. There are two solutions to this problem:

- Use a model with a longer supported sequence length.
- Truncate your sequences.  

Models have different supported sequence lengths, and some specialize in handling very long sequences. __Longformer__ is one example, and another is __LED__. If you’re working on a task that requires very long sequences, we recommend you take a look at those models.

Otherwise, we recommend you truncate your sequences by specifying the max_sequence_length parameter:  

```python
sequence = sequence[:max_sequence_length]
```