---
title:  "[NLP] Hugging face Chap3. A full training (customizing training)"
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

# Hugging face: A full training (customizing training)
Huggingface에 관한 포스트는 [Huggingface 공식 홈페이지](https://huggingface.co/)를 참고하여 작성하였으며 그 중에서도 Huggingface를 사용하는 방법에 관해 친절하게 설명해 놓은 글[(Huggingface course)](https://huggingface.co/course/chapter1)이 있어 이것을 바탕으로 작성하였습니다.  

이번 포스트에서는 Fine-tuning단계에서의 `학습을 커스터마이징`하는 방법에 대해 알아보도록 하겠습니다.🤗  

## 1. Prepare for training

Now we’ll see how to achieve the same results as we did in the last section without using the Trainer class. 

Before actually writing our training loop, we will need to define a few objects. The first ones are the dataloaders we will use to iterate over batches. But before we can define those dataloaders, we need to apply a bit of postprocessing to our tokenized_datasets, to take care of some things that the Trainer did for us automatically. Specifically, we need to:

(Trainer가 알아서 해주던 과정을 직접 손봐야 하는 부분이 있습니다. 가장 먼저 datasets 라이브러리로 정의한 저희의 tokenized_datasets을 다음의 사항들을 중심으로 수정합니다)  

- Remove the columns corresponding to values the model does not expect (like the __sentence1__ and __sentence2__ columns)
- Rename the column __label__ to __labels__ (because the model expects the argument to be named labels)
- Set the format of the datasets so they return PyTorch tensors instead of lists

```python
from datasets import load_dataset
from transformers import AutoTokenizer, DataCollatorWithPadding

raw_datasets = load_dataset("glue", "mrpc")
checkpoint = "bert-base-uncased"
tokenizer = AutoTokenizer.from_pretrained(checkpoint)

def tokenize_function(example):
    return tokenizer(example["sentence1"], example["sentence2"], truncation=True)

tokenized_datasets = raw_datasets.map(tokenize_function, batched=True)
data_collator = DataCollatorWithPadding(tokenizer=tokenizer)

# tokenized_datasets 수정
tokenized_datasets = tokenized_datasets.remove_columns(
    ["sentence1", "sentence2", "idx"]
)
tokenized_datasets = tokenized_datasets.rename_column("label", "labels")
tokenized_datasets.set_format("torch")

# We can then check that the result only has columns that our model will accept
tokenized_datasets["train"].column_names
--------------------------------------------------------------------------------------
['attention_mask', 'input_ids', 'labels', 'token_type_ids']
```

Now that this is done, we can easily define our `dataloaders`:  

```python
from torch.utils.data import DataLoader

train_dataloader = DataLoader(
    tokenized_datasets["train"], shuffle=True, batch_size=8, collate_fn=data_collator
)
eval_dataloader = DataLoader(
    tokenized_datasets["validation"], batch_size=8, collate_fn=data_collator
)
```  

To quickly check there is no mistake in the data processing, we can inspect a batch like this:  

```python
for batch in train_dataloader:
    break
{k: v.shape for k, v in batch.items()}
----------------------------------------------
{'attention_mask': torch.Size([8, 65]),
 'input_ids': torch.Size([8, 65]),
 'labels': torch.Size([8]),
 'token_type_ids': torch.Size([8, 65])}
```

모델을 정의해서 잘 동작하는지 보도록 하겠습니다.  

```python
from transformers import AutoModelForSequenceClassification

model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)

outputs = model(**batch)

print(outputs.loss, outputs.logits.shape)
-------------------------------------------------------------------
tensor(0.5441, grad_fn=<NllLossBackward>) torch.Size([8, 2])
```  

이제 optimizer, lr_scheduler만 정의해주면 됩니다. 🤗`Transformers`라이브러리에서 가져오도록 하겠습니다.  

```python
# transformers 라이브러리에서 default로 사용하는 optimizer, lr_scheduler을 사용해보겠습니다
from transformers import AdamW, get_scheduler

optimizer = AdamW(model.parameters(), lr=5e-5)

num_epochs = 3
num_training_steps = num_epochs * len(train_dataloader) # 1377

lr_scheduler = get_scheduler(
    "linear",
    optimizer=optimizer,
    num_warmup_steps=0,
    num_training_steps=num_training_steps
)
```

## 2. Training loop  

```python
import torch

device = torch.device("cuda") if torch.cuda.is_available() else torch.device("cpu")
model.to(device)
```

```python
from tqdm.auto import tqdm

progress_bar = tqdm(range(num_training_steps))

model.train()
for epoch in range(num_epochs):
    for batch in train_dataloader:
        batch = {k: v.to(device) for k, v in batch.items()}
        outputs = model(**batch)
        loss = outputs.loss
        loss.backward()
        
        optimizer.step()
        lr_scheduler.step()
        optimizer.zero_grad()
        progress_bar.update(1)
```

## 3. Evaluation loop

```python
from datasets import load_metric

metric= load_metric("glue", "mrpc")
model.eval()
for batch in eval_dataloader:
    batch = {k: v.to(device) for k, v in batch.items()}
    with torch.no_grad():
        outputs = model(**batch)
    
    logits = outputs.logits
    predictions = torch.argmax(logits, dim=-1)
    metric.add_batch(predictions=predictions, references=batch["labels"])

metric.compute()
-------------------------------------------------
{'accuracy': 0.8431372549019608, 'f1': 0.8907849829351535}
```

🔔 __multiple GPUs__  

The training loop we defined earlier works fine on a single CPU or GPU. But using the 🤗`Accelerate` library, with just a few adjustments we can enable distributed training on multiple GPUs or TPUs. Starting from the creation of the training and validation dataloaders, here is what our manual training loop looks like:  

```python
+ from accelerate import Accelerator
  from transformers import AdamW, AutoModelForSequenceClassification, get_scheduler

+ accelerator = Accelerator()

  model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)
  optimizer = AdamW(model.parameters(), lr=3e-5)

- device = torch.device("cuda") if torch.cuda.is_available() else torch.device("cpu")
- model.to(device)

+ train_dataloader, eval_dataloader, model, optimizer = accelerator.prepare(
+     train_dataloader, eval_dataloader, model, optimizer
+ )

  num_epochs = 3
  num_training_steps = num_epochs * len(train_dataloader)
  lr_scheduler = get_scheduler(
      "linear",
      optimizer=optimizer,
      num_warmup_steps=0,
      num_training_steps=num_training_steps
  )

  progress_bar = tqdm(range(num_training_steps))

  model.train()
  for epoch in range(num_epochs):
      for batch in train_dataloader:
-         batch = {k: v.to(device) for k, v in batch.items()}
          outputs = model(**batch)
          loss = outputs.loss
-         loss.backward()
+         accelerator.backward(loss)

          optimizer.step()
          lr_scheduler.step()
          optimizer.zero_grad()
          progress_bar.update(1)
```

코드에 대한 설명은 아래와 같습니다.  

The first line to add is the import line. The second line instantiates an Accelerator object that will look at the environment and initialize the proper distributed setup. 🤗`Accelerate` handles the device placement for you, so you can remove the lines that put the model on the device (or, if you prefer, change them to use __accelerator.device__ instead of __device__).

Then the main bulk of the work is done in the line that sends the dataloaders, the model, and the optimizer to __accelerator.prepare__. This will wrap those objects in the proper container to make sure your distributed training works as intended. The remaining changes to make are removing the line that puts the batch on the device (again, if you want to keep this you can just change it to use __accelerator.device__) and replacing loss.backward() with __accelerator.backward(loss)__.  

```
# TPU는 전체 데이터셋이 직사각형 shape일 때 성능이 더 좋습니다 
# -> (배치마다 직사각형 shape -> 전체 데이터셋 직사각형 shape)
⚠️ In order to benefit from the speed-up offered by Cloud TPUs, 
we recommend padding your samples to a fixed length with the 
`padding="max_length"` and `max_length` arguments of the tokenizer.
```  

<details>
<summary>here’s what the complete training loop looks like with 🤗 Accelerate</summary>
<div markdown="1">       

```python
from accelerate import Accelerator
from transformers import AdamW, AutoModelForSequenceClassification, get_scheduler

accelerator = Accelerator()

model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)
optimizer = AdamW(model.parameters(), lr=3e-5)

train_dl, eval_dl, model, optimizer = accelerator.prepare(
    train_dataloader, eval_dataloader, model, optimizer
)

num_epochs = 3
num_training_steps = num_epochs * len(train_dl)
lr_scheduler = get_scheduler(
    "linear",
    optimizer=optimizer,
    num_warmup_steps=0,
    num_training_steps=num_training_steps
)

progress_bar = tqdm(range(num_training_steps))

model.train()
for epoch in range(num_epochs):
    for batch in train_dl:
        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        
        optimizer.step()
        lr_scheduler.step()
        optimizer.zero_grad()
        progress_bar.update(1)
```

Putting this in a train.py script will make that script runnable on any kind of distributed setup. To try it out in your distributed setup, run the command:  

```python
accelerate config
```
which will prompt you to answer a few questions and dump your answers in a configuration file used by this command:
```python
accelerate launch train.py
```  

which will launch the distributed training.

If you want to try this in a Notebook (for instance, to test it with TPUs on Colab), just paste the code in a training_function and run a last cell with:  

```python
from accelerate import notebook_launcher

notebook_launcher(training_function)
```
</div>
</details>  

