---
title:  "[NLP] Hugging face Chap2. Putting it all together(powerful tokenizer API)"
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

# Hugging face: Powerful tokenizer API
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 tokenizer가 얼마나 high level의 API인지 보도록 하겠습니다.🤗  

## 1. Multiple sentences  

We’ve explored how tokenizers work and looked at tokenization, conversion to input IDs, padding, truncation, and attention masks.  

🤗Transformers API can `handle all of this` for us with a high-level function that we’ll dive into here. When you call your tokenizer directly on the sentence, you get back inputs that are ready to pass through your model:  

(모델에 맞는 tokenizer를 호출하면 tokenizer는 raw text에 모델에 필요한 모든 tokenization과정을 알아서 해줍니다.)  

```python
from transformers import AutoTokenizer

checkpoint = "distilbert-base-uncased-finetuned-sst-2-english"
tokenizer = AutoTokenizer.from_pretrained(checkpoint)

sequence = "I've been waiting for a HuggingFace course my whole life."

model_inputs = tokenizer(sequence)
```

Here, the model_inputs variable `contains everything that’s necessary for a model to operate well`. For DistilBERT, that includes the input IDs as well as the attention mask. Other models that accept additional inputs will also have those output by the tokenizer object.

- Handle __single__, __multiple__ sequence
- Support __padding__, __truncate__, __attention masking__ operation
- Support __diverse return type__
- Support __special tokens__

```python
# Will pad the sequences up to the maximum sequence length
model_inputs = tokenizer(sequences, padding="longest")

# Will pad the sequences up to the model max length
# (512 for BERT or DistilBERT)
model_inputs = tokenizer(sequences, padding="max_length")

# Will pad the sequences up to the specified max length
model_inputs = tokenizer(sequences, padding="max_length", max_length=8)
```

```python
sequences = [
  "I've been waiting for a HuggingFace course my whole life.",
  "So have I!"
]

# Will truncate the sequences that are longer than the model max length
# (512 for BERT or DistilBERT)
model_inputs = tokenizer(sequences, truncation=True)

# Will truncate the sequences that are longer than the specified max length
model_inputs = tokenizer(sequences, max_length=8, truncation=True)
```

```python
sequences = [
  "I've been waiting for a HuggingFace course my whole life.",
  "So have I!"
]

# Returns PyTorch tensors
model_inputs = tokenizer(sequences, padding=True, return_tensors="pt")

# Returns TensorFlow tensors
model_inputs = tokenizer(sequences, padding=True, return_tensors="tf")

# Returns NumPy arrays
model_inputs = tokenizer(sequences, padding=True, return_tensors="np")
```

```python
print(tokenizer.decode(model_inputs["input_ids"]))
--------------------------------------------------
"[CLS] i've been waiting for a huggingface course my whole life. [SEP]"
```

🔔 __어떤 parameter가 존재하는지 어떻게 알 수 있을까?__  

이 부분은 파이썬 관련된 부분입니다. 개인적인 궁금증으로 알아보니 `inspect`라는 라이브러리의 `signature` 함수가 이를 지원합니다. 바로 예시를 보도록 하겠습니다.  

```python
signature(tokenizer)
---------------------------------------
<Signature (text: Union[str, List[str], List[List[str]]], text_pair: Union[str, List[str], List[List[str]], NoneType] = None, add_special_tokens: bool = True, padding: Union[bool, str, transformers.file_utils.PaddingStrategy] = False, truncation: Union[bool, str, transformers.tokenization_utils_base.TruncationStrategy] = False, max_length: Union[int, NoneType] = None, stride: int = 0, is_split_into_words: bool = False, pad_to_multiple_of: Union[int, NoneType] = None, return_tensors: Union[str, transformers.file_utils.TensorType, NoneType] = None, return_token_type_ids: Union[bool, NoneType] = None, return_attention_mask: Union[bool, NoneType] = None, return_overflowing_tokens: bool = False, return_special_tokens_mask: bool = False, return_offsets_mapping: bool = False, return_length: bool = False, verbose: bool = True, **kwargs) -> transformers.tokenization_utils_base.BatchEncoding>
```

```python
# 좀 더 예쁘게 출력하는 방법
for param in signature(tokenizer).parameters.values():
    print(param)
-----------------------------------------------------------
text: Union[str, List[str], List[List[str]]]
text_pair: Union[str, List[str], List[List[str]], NoneType] = None
add_special_tokens: bool = True
padding: Union[bool, str, transformers.file_utils.PaddingStrategy] = False
truncation: Union[bool, str, transformers.tokenization_utils_base.TruncationStrategy] = False
max_length: Union[int, NoneType] = None
stride: int = 0
is_split_into_words: bool = False
pad_to_multiple_of: Union[int, NoneType] = None
return_tensors: Union[str, transformers.file_utils.TensorType, NoneType] = None
return_token_type_ids: Union[bool, NoneType] = None
return_attention_mask: Union[bool, NoneType] = None
return_overflowing_tokens: bool = False
return_special_tokens_mask: bool = False
return_offsets_mapping: bool = False
return_length: bool = False
verbose: bool = True
**kwargs
```