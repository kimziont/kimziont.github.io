---
title:  "[NLP] Main class of transformers: Tokenizer"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - nlp_huggingface
tags:
  - NLP
last_modified_at: 2021-10-10
---  

# \[NLP] Main class of transformers: Tokenizer
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였습니다.  

## Temp

A tokenizer is in charge of preparing the inputs for a model. The library contains tokenizers for all the models. Most of the tokenizers are available in two flavors: a full python implementation and a “Fast” implementation based on the Rust library tokenizers. The “Fast” implementations allows:  

- a significant speed-up in particular when doing batched tokenization and
- additional methods to map between the original string (character and words) and the token space (e.g. getting the index of the token comprising a given character or the span of characters corresponding to a given token). Currently no “Fast” implementation is available for the SentencePiece-based tokenizers (for T5, ALBERT, CamemBERT, XLMRoBERTa and XLNet models).  
  
The base classes `PreTrainedTokenizer` and `PreTrainedTokenizerFast` implement the common methods for encoding string inputs in model inputs (see below) and instantiating/saving python and “Fast” tokenizers either from a local file or directory or from a pretrained tokenizer provided by the library (downloaded from HuggingFace’s AWS S3 repository). They both rely on `PreTrainedTokenizerBase` that contains the common methods, and `SpecialTokensMixin`.  

`PreTrainedTokenizer` and `PreTrainedTokenizerFast` thus implement the main methods for using all the tokenizers:

- Tokenizing (splitting strings in sub-word token strings), converting tokens strings to ids and back, and encoding/decoding (i.e., tokenizing and converting to integers).
- Adding new tokens to the vocabulary in a way that is independent of the underlying structure (BPE, SentencePiece…).
- Managing special tokens (like mask, beginning-of-sentence, etc.): adding them, assigning them to attributes in the tokenizer for easy access and making sure they are not split during tokenization.  
  
`BatchEncoding` holds the output of the tokenizer’s encoding methods (\__call__, encode_plus and batch_encode_plus) and is derived from a Python dictionary. When the tokenizer is a pure python tokenizer, this class behaves just like a standard python dictionary and holds the various model inputs computed by these methods (input_ids, attention_mask…). When the tokenizer is a “Fast” tokenizer (i.e., backed by HuggingFace tokenizers library), this class provides in addition several advanced alignment methods which can be used to map between the original string (character and words) and the token space (e.g., getting the index of the token comprising a given character or the span of characters corresponding to a given token).

## PreTrainedTokenizerBase  

This page lists all the utility functions used by the tokenizers, mainly the class   

PreTrainedTokenizerBase that implements the common methods between PreTrainedTokenizer and PreTrainedTokenizerFast and the mixin SpecialTokensMixin.  

Most of those are only useful if you are studying the code of the tokenizers in the library.  

### methods

- \__call__
- batch_decode
- batch_endode_plus
- build_inputs_with_special_tokens
- clean_up_tokenization
- convert_tokens_to_string
- create_token_type_ids_from_sequences
- decode
- encode
- encode_plus
- from_pretrained
- get_special_tokens_mask
- get_vocab
- property max_len_sentences_pair
- property max_len_single_sentence
- pad
- prepare_for_model
- prepare_seq2seq_batch
- push_to_hub
- save_pretrained
- save_vocabulary
- tokenize
- truncate_sequences  
- 
## PreTrainedTokenizer  

Base class for all slow tokenizers.  

Inherits from PreTrainedTokenizerBase.  

Handle all the shared methods for tokenization and special tokens as well as methods downloading/caching/loading pretrained tokenizers as well as adding tokens to the vocabulary.  

This class also contain the added tokens in a unified way on top of all tokenizers so we don’t have to handle the specific vocabulary augmentation methods of the various underlying dictionary structures (BPE, sentencepiece…).  

### params  

- model_max_length (int, optional) – The maximum length (in number of tokens) for the inputs to the transformer model. When the tokenizer is loaded with from_pretrained(), this will be set to the value stored for the associated model in max_model_input_sizes (see above). If no value is provided, will default to VERY_LARGE_INTEGER (int(1e30)).

- padding_side – (str, optional): The side on which the model should have padding applied. Should be selected between [‘right’, ‘left’]. Default value is picked from the class attribute of the same name.

- model_input_names (List[string], optional) – The list of inputs accepted by the forward pass of the model (like "token_type_ids" or "attention_mask"). Default value is picked from the class attribute of the same name.

- bos_token (str or tokenizers.AddedToken, optional) – A special token representing the beginning of a sentence. Will be associated to self.bos_token and self.bos_token_id.

- eos_token (str or tokenizers.AddedToken, optional) – A special token representing the end of a sentence. Will be associated to self.eos_token and self.eos_token_id.

- unk_token (str or tokenizers.AddedToken, optional) – A special token representing an out-of-vocabulary token. Will be associated to self.unk_token and self.unk_token_id.

- sep_token (str or tokenizers.AddedToken, optional) – A special token separating two different sentences in the same input (used by BERT for instance). Will be associated to self.sep_token and self.sep_token_id.

- pad_token (str or tokenizers.AddedToken, optional) – A special token used to make arrays of tokens the same size for batching purpose. Will then be ignored by attention mechanisms or loss computation. Will be associated to self.pad_token and self.pad_token_id.

- cls_token (str or tokenizers.AddedToken, optional) – A special token representing the class of the input (used by BERT for instance). Will be associated to self.cls_token and self.cls_token_id.

- mask_token (str or tokenizers.AddedToken, optional) – A special token representing a masked token (used by masked-language modeling pretraining objectives, like BERT). Will be associated to self.mask_token and self.mask_token_id.

- additional_special_tokens (tuple or list of str or tokenizers.AddedToken, optional) – A tuple or a list of additional special tokens. Add them here to ensure they won’t be split by the tokenization process. Will be associated to self.additional_special_tokens and self.additional_special_tokens_ids.  

### methods  

- convert_ids_to_tokens
- convert_tokens_to_ids
- convert_tokens_to_string
- get_added_vocab
- get_special_tokens_mask
- tokenize
- property vocab_size



## PreTrainedTokenizerFast

### params

- model_max_length (int, optional) – The maximum length (in number of tokens) for the inputs to the transformer model. When the tokenizer is loaded with from_pretrained(), this will be set to the value stored for the associated model in max_model_input_sizes (see above). If no value is provided, will default to VERY_LARGE_INTEGER (int(1e30)).

- padding_side – (str, optional): The side on which the model should have padding applied. Should be selected between [‘right’, ‘left’]. Default value is picked from the class attribute of the same name.

- model_input_names (List\[string], optional) – The list of inputs accepted by the forward pass of the model (like "token_type_ids" or "attention_mask"). Default value is picked from the class attribute of the same name.

- bos_token (str or tokenizers.AddedToken, optional) – A special token representing the beginning of a sentence. Will be associated to self.bos_token and self.bos_token_id.

- eos_token (str or tokenizers.AddedToken, optional) – A special token representing the end of a sentence. Will be associated to self.eos_token and self.eos_token_id.

- unk_token (str or tokenizers.AddedToken, optional) – A special token representing an out-of-vocabulary token. Will be associated to self.unk_token and self.unk_token_id.

- sep_token (str or tokenizers.AddedToken, optional) – A special token separating two different sentences in the same input (used by BERT for instance). Will be associated to self.sep_token and self.sep_token_id.

- pad_token (str or tokenizers.AddedToken, optional) – A special token used to make arrays of tokens the same size for batching purpose. Will then be ignored by attention mechanisms or loss computation. Will be associated to self.pad_token and self.pad_token_id.

- cls_token (str or tokenizers.AddedToken, optional) – A special token representing the class of the input (used by BERT for instance). Will be associated to self.cls_token and self.cls_token_id.

- mask_token (str or tokenizers.AddedToken, optional) – A special token representing a masked token (used by masked-language modeling pretraining objectives, like BERT). Will be associated to self.mask_token and self.mask_token_id.

- additional_special_tokens (tuple or list of str or tokenizers.AddedToken, optional) – A tuple or a list of additional special tokens. Add them here to ensure they won’t be split by the tokenization process. Will be associated to self.additional_special_tokens and self.additional_special_tokens_ids.

- tokenizer_object (tokenizers.Tokenizer) – A tokenizers.Tokenizer object from 🤗 tokenizers to instantiate from. See Using tokenizers from 🤗 tokenizers for more information.

- tokenizer_file (str) – A path to a local JSON file representing a previously serialized tokenizers.Tokenizer object from 🤗 tokenizers.

### methods

- convert_ids_to_tokens
- convert_tokens_to_ids
- convert_tokens_to_string
- get_added_vocab
- get_special_tokens_mask
- tokenize
- property vocab_size
- property backend_tokenizer
- property decoder
- get_vocab
- set_truncation_and_padding
- train_new_from_iterator  
  

## BatchEncoding