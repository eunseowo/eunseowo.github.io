---
layout: post
title: IMDB 영화평 감성 분석
date: 2025-05-20 17:04:00 +0900
categories: [ML]
tags: [NLP, Sentiment_Analysis]
---

## 1. Install


```python
pip install transformers datasets evaluate accelerate
```

## 2. 코드

### 2-1. 데이터


```python
!pip install -U datasets fsspec
```


```python
from datasets import load_dataset
imdb = load_dataset("imdb")
```

    /usr/local/lib/python3.11/dist-packages/huggingface_hub/utils/_auth.py:94: UserWarning: 
    The secret `HF_TOKEN` does not exist in your Colab secrets.
    To authenticate with the Hugging Face Hub, create a token in your settings tab (https://huggingface.co/settings/tokens), set it as secret in your Google Colab and restart your session.
    You will be able to reuse this secret in all of your notebooks.
    Please note that authentication is recommended but still optional to access public models or datasets.
      warnings.warn(



    README.md:   0%|          | 0.00/7.81k [00:00<?, ?B/s]



    train-00000-of-00001.parquet:   0%|          | 0.00/21.0M [00:00<?, ?B/s]



    test-00000-of-00001.parquet:   0%|          | 0.00/20.5M [00:00<?, ?B/s]



    unsupervised-00000-of-00001.parquet:   0%|          | 0.00/42.0M [00:00<?, ?B/s]



    Generating train split:   0%|          | 0/25000 [00:00<?, ? examples/s]



    Generating test split:   0%|          | 0/25000 [00:00<?, ? examples/s]



    Generating unsupervised split:   0%|          | 0/50000 [00:00<?, ? examples/s]



```python
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("distilbert/distilbert-base-uncased")
```


    tokenizer_config.json:   0%|          | 0.00/48.0 [00:00<?, ?B/s]



    config.json:   0%|          | 0.00/483 [00:00<?, ?B/s]



    vocab.txt:   0%|          | 0.00/232k [00:00<?, ?B/s]



    tokenizer.json:   0%|          | 0.00/466k [00:00<?, ?B/s]



```python
# 텍스트 -> 숫자들
tokenizer("Transformers are really useful for natural language processing.")
```




    {'input_ids': [101, 19081, 2024, 2428, 6179, 2005, 3019, 2653, 6364, 1012, 102], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}




```python
def preprocess_function(examples):
    return tokenizer(examples["text"], truncation=True)
```


```python
examples = imdb['train'][1]
#tokenizer(examples["text"], truncation=True)
preprocess_function(examples)
```




    {'input_ids': [101, 1000, 1045, 2572, 8025, 1024, 3756, 1000, 2003, 1037, 15544, 19307, 1998, 3653, 6528, 20771, 19986, 8632, 1012, 2009, 2987, 1005, 1056, 3043, 2054, 2028, 1005, 1055, 2576, 5328, 2024, 2138, 2023, 2143, 2064, 6684, 2022, 2579, 5667, 2006, 2151, 2504, 1012, 2004, 2005, 1996, 4366, 2008, 19124, 3287, 16371, 25469, 2003, 2019, 6882, 13316, 1011, 2459, 1010, 2008, 3475, 1005, 1056, 2995, 1012, 1045, 1005, 2310, 2464, 1054, 1011, 6758, 3152, 2007, 3287, 16371, 25469, 1012, 4379, 1010, 2027, 2069, 3749, 2070, 25085, 5328, 1010, 2021, 2073, 2024, 1996, 1054, 1011, 6758, 3152, 2007, 21226, 24728, 22144, 2015, 1998, 20916, 4691, 6845, 2401, 1029, 7880, 1010, 2138, 2027, 2123, 1005, 1056, 4839, 1012, 1996, 2168, 3632, 2005, 2216, 10231, 7685, 5830, 3065, 1024, 8040, 7317, 5063, 2015, 11820, 1999, 1996, 9478, 2021, 2025, 1037, 17962, 21239, 1999, 4356, 1012, 1998, 2216, 3653, 6528, 20771, 10271, 5691, 2066, 1996, 2829, 16291, 1010, 1999, 2029, 2057, 1005, 2128, 5845, 2000, 1996, 2609, 1997, 6320, 25624, 1005, 1055, 17061, 3779, 1010, 2021, 2025, 1037, 7637, 1997, 5061, 5710, 2006, 9318, 7367, 5737, 19393, 1012, 2077, 6933, 1006, 2030, 20242, 1007, 1000, 3313, 1011, 3115, 1000, 1999, 5609, 1997, 16371, 25469, 1010, 1996, 10597, 27885, 5809, 2063, 2323, 2202, 2046, 4070, 2028, 14477, 6767, 8524, 6321, 5793, 28141, 4489, 2090, 2273, 1998, 2308, 1024, 2045, 2024, 2053, 8991, 18400, 2015, 2006, 4653, 2043, 19910, 3544, 15287, 1010, 1998, 1996, 2168, 3685, 2022, 2056, 2005, 1037, 2158, 1012, 1999, 2755, 1010, 2017, 3227, 2180, 1005, 1056, 2156, 2931, 8991, 18400, 2015, 1999, 2019, 2137, 2143, 1999, 2505, 2460, 1997, 22555, 2030, 13216, 14253, 2050, 1012, 2023, 6884, 3313, 1011, 3115, 2003, 2625, 1037, 3313, 3115, 2084, 2019, 4914, 2135, 2139, 24128, 3754, 2000, 2272, 2000, 3408, 20547, 2007, 1996, 19008, 1997, 2308, 1005, 1055, 4230, 1012, 102], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}



map 메소드 사용


```python
tokenized_imdb = imdb.map(preprocess_function, batched=True)
```


    Map:   0%|          | 0/25000 [00:00<?, ? examples/s]



    Map:   0%|          | 0/25000 [00:00<?, ? examples/s]



    Map:   0%|          | 0/50000 [00:00<?, ? examples/s]


- `imdb` 에는 자료가 “텍스트” 형태로 저장되어있고, `tokenized_imdb` 는 자료가 “텍스트 + 숫자” 형태로 저장되어 있음.
  - 즉 `tokenized_imdb` 는 원래데이터 (raw data) 와 전처리된 데이터 (preprocessed data) 가 같이 있음.

### 2-2. 인공지능 생성


```python
from transformers import AutoModelForSequenceClassification
model = AutoModelForSequenceClassification.from_pretrained(
    "distilbert/distilbert-base-uncased", num_labels=2
)
```

    Xet Storage is enabled for this repo, but the 'hf_xet' package is not installed. Falling back to regular HTTP download. For better performance, install the package with: `pip install huggingface_hub[hf_xet]` or `pip install hf_xet`
    WARNING:huggingface_hub.file_download:Xet Storage is enabled for this repo, but the 'hf_xet' package is not installed. Falling back to regular HTTP download. For better performance, install the package with: `pip install huggingface_hub[hf_xet]` or `pip install hf_xet`



    model.safetensors:   0%|          | 0.00/268M [00:00<?, ?B/s]


    Some weights of DistilBertForSequenceClassification were not initialized from the model checkpoint at distilbert/distilbert-base-uncased and are newly initialized: ['classifier.bias', 'classifier.weight', 'pre_classifier.bias', 'pre_classifier.weight']
    You should probably TRAIN this model on a down-stream task to be able to use it for predictions and inference.


### 2-3. 인공지능 학습

트레이너를 만들 때 필요한 재료인 `data_collator` 생성


```python
from transformers import DataCollatorWithPadding
data_collator = DataCollatorWithPadding(tokenizer=tokenizer)
```

트레이너를 만들 때 필요한 재료인 `compute_metrics` 생성


```python
import evaluate
import numpy as np
accuracy = evaluate.load("accuracy")
def compute_metrics(eval_pred):
    predictions, labels = eval_pred
    predictions = np.argmax(predictions, axis=1)
    return accuracy.compute(predictions=predictions, references=labels)
```


    Downloading builder script:   0%|          | 0.00/4.20k [00:00<?, ?B/s]



```python
from transformers import TrainingArguments, Trainer
training_args = TrainingArguments(
    output_dir="my_awesome_model",
    learning_rate=2e-5,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    num_train_epochs=2, # 전체문제세트를 2번 공부하라..
    weight_decay=0.01,
    report_to="none", # wandb 비활성화를 위한 코드
    eval_strategy="epoch",
    save_strategy="epoch",
    load_best_model_at_end=True,
    push_to_hub=False,
)
```


```python
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_imdb["train"],
    eval_dataset=tokenized_imdb["test"],
    tokenizer=tokenizer,
    data_collator=data_collator,
    compute_metrics=compute_metrics,
)
```

    <ipython-input-30-20bc156c0443>:1: FutureWarning: `tokenizer` is deprecated and will be removed in version 5.0.0 for `Trainer.__init__`. Use `processing_class` instead.
      trainer = Trainer(


트레이너를 이용하여 인공지능을 학습시켜보자.


```python
# wandb (Weights & Biases)의 로그인 메시지를 무시하거나 비활성화하는 코드
import os
os.environ["WANDB_DISABLED"] = "true"
```


```python
trainer.train()
```



    <div>

      <progress value='3126' max='3126' style='width:300px; height:20px; vertical-align: middle;'></progress>
      [3126/3126 49:50, Epoch 2/2]
    </div>
    <table border="1" class="dataframe">
  <thead>
 <tr style="text-align: left;">
      <th>Epoch</th>
      <th>Training Loss</th>
      <th>Validation Loss</th>
      <th>Accuracy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>0.223200</td>
      <td>0.215903</td>
      <td>0.918720</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.133300</td>
      <td>0.255583</td>
      <td>0.931200</td>
    </tr>
  </tbody>
</table><p>





    TrainOutput(global_step=3126, training_loss=0.1694532328512298, metrics={'train_runtime': 2990.832, 'train_samples_per_second': 16.718, 'train_steps_per_second': 1.045, 'total_flos': 6556904415524352.0, 'train_loss': 0.1694532328512298, 'epoch': 2.0})



- **Training Loss** 감소 → 학습이 잘 진행됨
- **Validation Loss** 소폭 증가 → 약간의 과적합 가능성 있음
- **Accuracy** 증가 (91.87% → 93.12%) → 전반적으로 모델 성능 향상

### 2-4. 예측


```python
text0 = "The movie was a major disappointment. The plot was weak, and the pacing felt disjointed. The characters lacked depth, making it hard to connect with them. Predictable twists and a cliché resolution left no sense of excitement. Visually, it was unimpressive, and the soundtrack didn’t fit the scenes, pulling me out of the experience. Overall, it failed to deliver anything memorable."
text1 = "This was a masterpiece. Not completely faithful to the books, but enthralling from beginning to end. Might be my favorite of the three."
```

0: 부정적
1: 긍정적


```python
from transformers import pipeline
classifier1 = pipeline("sentiment-analysis", model="/content/my_awesome_model/checkpoint-1563")
print(classifier1(text0))
print(classifier1(text1))
```

    Device set to use cuda:0


    [{'label': 'LABEL_0', 'score': 0.9944108128547668}]
    [{'label': 'LABEL_1', 'score': 0.9950580596923828}]


- text0
  - 모델이 예측한 라벨(label): LABEL_0
  - 모델이 이 라벨을 정답이라고 판단한 확률(score): 99.44%

- text1
  - 모델이 예측한 라벨(label): LABEL_1
  - 모델이 이 라벨을 정답이라고 판단한 확률(score): 99.50%


```python
classifier2 = pipeline("sentiment-analysis", model="/content/my_awesome_model/checkpoint-3126")
print(classifier2(text0))
print(classifier2(text1))
```

    Device set to use cuda:0


    [{'label': 'LABEL_0', 'score': 0.9979580640792847}]
    [{'label': 'LABEL_1', 'score': 0.9984914064407349}]


- text0
  - 모델이 예측한 라벨(label): LABEL_0
  - 모델이 이 라벨을 정답이라고 판단한 확률(score): 99.79%

- text1
  - 모델이 예측한 라벨(label): LABEL_1
  - 모델이 이 라벨을 정답이라고 판단한 확률(score): 99.84%


- 두 모델 모두 `text0`은 **부정**, `text1`은 **긍정**으로 예측하여 일관된 판단을 보임
- **Epoch 2까지 학습된 모델**은 두 문장 모두에 대해 **더 높은 확신도**를 보임
- 따라서, **2 에폭 모델이 더 안정적이고 신뢰도 높은 결과를 제공**하는 것으로 판단됨

---

## 3. DataSet

실제 현업에서는 우리가 원하는 형식 그대로의 데이터를 그대로 받는 경우는 드물다.
- 내가 원하는 데이터를 아래와 같은 양식 (=형태)으로 정리해야함.


```python
import datasets

data = datasets.load_dataset('imdb')
data
```




    DatasetDict({
        train: Dataset({
            features: ['text', 'label'],
            num_rows: 25000
        })
        test: Dataset({
            features: ['text', 'label'],
            num_rows: 25000
        })
        unsupervised: Dataset({
            features: ['text', 'label'],
            num_rows: 50000
        })
    })




```python
data, type(data)
```




    (DatasetDict({
         train: Dataset({
             features: ['text', 'label'],
             num_rows: 25000
         })
         test: Dataset({
             features: ['text', 'label'],
             num_rows: 25000
         })
         unsupervised: Dataset({
             features: ['text', 'label'],
             num_rows: 50000
         })
     }),
     datasets.dataset_dict.DatasetDict)




```python
data['train'], type(data['train'])
```




    (Dataset({
         features: ['text', 'label'],
         num_rows: 25000
     }),
     datasets.arrow_dataset.Dataset)



train, test의 타입은 `Dataset`, data의 타입은 `DatasetDict`라고 생각하면 된다.


```python
data['train'][0]
```




    {'text': 'I rented I AM CURIOUS-YELLOW from my video store because of all the controversy that surrounded it when it was first released in 1967. I also heard that at first it was seized by U.S. customs if it ever tried to enter this country, therefore being a fan of films considered "controversial" I really had to see this for myself.<br /><br />The plot is centered around a young Swedish drama student named Lena who wants to learn everything she can about life. In particular she wants to focus her attentions to making some sort of documentary on what the average Swede thought about certain political issues such as the Vietnam War and race issues in the United States. In between asking politicians and ordinary denizens of Stockholm about their opinions on politics, she has sex with her drama teacher, classmates, and married men.<br /><br />What kills me about I AM CURIOUS-YELLOW is that 40 years ago, this was considered pornographic. Really, the sex and nudity scenes are few and far between, even then it\'s not shot like some cheaply made porno. While my countrymen mind find it shocking, in reality sex and nudity are a major staple in Swedish cinema. Even Ingmar Bergman, arguably their answer to good old boy John Ford, had sex scenes in his films.<br /><br />I do commend the filmmakers for the fact that any sex shown in the film is shown for artistic purposes rather than just to shock people and make money to be shown in pornographic theaters in America. I AM CURIOUS-YELLOW is a good film for anyone wanting to study the meat and potatoes (no pun intended) of Swedish cinema. But really, this film doesn\'t have much of a plot.',
     'label': 0}



### 3-1. datasets.Dataset.from_dict

`datasets.Dataset.from_dict`: Python의 딕셔너리(dict)를 Dataset 객체로 변환하는 함수
  - 주로 딕셔너리 형태의 데이터를 빠르게 데이터셋으로 변환할 때 사용됨.

- 간단한 사용법
```
from datasets import Dataset
# 딕셔너리를 Dataset으로 변환
data_dict = {
    'text': ["Hello world", "How are you?", "Fine, thanks!"],
    'label': [0, 1, 1]
}
# Dataset 생성
dataset = Dataset.from_dict(data_dict)
# 출력
print(dataset)
```
주요 매개변수:

- `mapping`: 필수, 문자열을 키로 하고 리스트 또는 배열을 값으로 하는 딕셔너리.
- `features`: 선택, 데이터셋의 각 필드 타입을 정의.
- `info`: 선택, 데이터셋에 대한 추가 정보(설명, 인용 등).
- `split`: 선택, 데이터셋의 나누기(‘train’, ‘test’ 등).

반환값:

- `Dataset`: PyArrow 기반의 데이터셋 객체.


```python
train_dict = {
    'text': [
        "I prefer making decisions based on logic and objective facts.",
        "I always consider how others might feel when making a decision.",
        "Data and analysis drive most of my decisions.",
        "I rely on my empathy and personal values to guide my choices."
    ],
    'label': [0, 1, 0, 1]  # 0은 T(사고형), 1은 F(감정형)
}

test_dict = {
    'text': [
        "I find it important to weigh all the pros and cons logically.",
        "When making decisions, I prioritize harmony and people's emotions."
    ],
    'label': [0, 1]  # 0은 T(사고형), 1은 F(감정형)
}
```


```python
train_data = datasets.Dataset.from_dict(train_dict)
test_data = datasets.Dataset.from_dict(test_dict)
```

`datasets.dataset_dict.DatasetDict`의 인스턴스 만들기


```python
my_data = datasets.dataset_dict.DatasetDict({'train':train_data, 'test':test_data})
```


```python
my_data['train'][0]
```




    {'text': 'I prefer making decisions based on logic and objective facts.',
     'label': 0}



- 일단 아래와 같은 형태로 분석할 데이터를 저장할 수 있다면, 나머지 분석은 코드를 복/붙하여 진행할 수 있음.

```
train_dict = {
    'text': [
        "I prefer making decisions based on logic and objective facts.",
        "I always consider how others might feel when making a decision.",
        "Data and analysis drive most of my decisions.",
        "I rely on my empathy and personal values to guide my choices."
    ],
    'label': [0, 1, 0, 1]  # 0은 T(사고형), 1은 F(감정형)
}

test_dict = {
    'text': [
        "I find it important to weigh all the pros and cons logically.",
        "When making decisions, I prioritize harmony and people's emotions."
    ],
    'label': [0, 1]  # 0은 T(사고형), 1은 F(감정형)
}
```

---

## 4. Tokenizer

**토크나이저(tokenizer)**: 자연어 문장을 **모델이 이해할 수 있는 숫자 형태(input_ids 등)**로 바꿔주는 도구
- 텍스트를 토큰(token)이라는 단위로 쪼개고, 이를 사전에 정의된 어휘집(vocabulary)을 바탕으로 숫자 ID로 매핑함.


왜 필요한가?
- 인공지능 모델은 텍스트 자체를 이해하지 못하고, **숫자**만 처리할 수 있다.
  - `"I love you."` 같은 문장을 `[101, 1045, 2293, 2017, 102]`와 같은 숫자 시퀀스로 바꿔줘야 함.


주요 출력값 설명

| 항목               | 설명                                                        |
|--------------------|-------------------------------------------------------------|
| `input_ids`        | 문장의 각 단어(또는 subword)를 숫자로 인코딩한 값           |
| `attention_mask`   | 실제 토큰에는 1, 패딩된 부분에는 0을 부여해 무시할 부분을 지정 |




```python
# str -> Dict
tokenizer(my_data['train']['text'][0])
```




    {'input_ids': [101, 1045, 9544, 2437, 6567, 2241, 2006, 7961, 1998, 7863, 8866, 1012, 102], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}



### 4-1. 기능
1. 단어별로 다른 숫자를 맵핑
2. 처음과 끝은 각각 `101`, `102` 라는 숫자로 맵핑


```python
tokenizer = AutoTokenizer.from_pretrained("distilbert/distilbert-base-uncased")
```


```python
tokenizer('hi hello')
```




    {'input_ids': [101, 7632, 7592, 102], 'attention_mask': [1, 1, 1, 1]}




```python
tokenizer('hi hi hello hello hello hi')
```




    {'input_ids': [101, 7632, 7632, 7592, 7592, 7592, 7632, 102], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1]}



3. 여러개의 텍스트도 `[텍스트1, 텍스트2, ... ]` 꼴로 전달하면 토크나이저가 알아서 잘 처리해준다.


```python
tokenizer(['hi hello', 'hello hello hello', 'hi hi'])
```




    {'input_ids': [[101, 7632, 7592, 102], [101, 7592, 7592, 7592, 102], [101, 7632, 7632, 102]], 'attention_mask': [[1, 1, 1, 1], [1, 1, 1, 1, 1], [1, 1, 1, 1]]}



### 4-2. truncation=True

`truncation=True` 의 역할
- 너무 문장이 길면 잘라내는 역할을 한다.


```python
tokenizer('hi hello '*2)
```




    {'input_ids': [101, 7632, 7592, 7632, 7592, 102], 'attention_mask': [1, 1, 1, 1, 1, 1]}




```python
len(tokenizer('hi hello '*2)['input_ids']), len(tokenizer('hi hello '*2)['attention_mask'])
```




    (6, 6)




```python
# 원래는 602가 나와야하는데 짤려서 512만 나옴
len(tokenizer('hi hello '*300,truncation=True)['input_ids'])
```




    512



### 4-3. padding

- 모델은 입력 길이가 다르면 한 번에 처리하지 못하므로, 짧은 문장을 0으로 채워 길이를 맞춰준다.
- 이렇게 길이를 맞춰주는 과정을 **패딩(padding)**이라고 하며, `max_length`와 함께 자주 사용됨.
- `attention_mask`는 패딩된 0을 모델이 무시하도록 도와준다.



```python
tokenizer('hi hello', max_length = 10, padding="max_length" )
```




    {'input_ids': [101, 7632, 7592, 102, 0, 0, 0, 0, 0, 0], 'attention_mask': [1, 1, 1, 1, 0, 0, 0, 0, 0, 0]}



| 항목               | 설명                                                     |
| ---------------- | ------------------------------------------------------ |
| `max_length`     | 토큰 길이를 최대 10으로 맞춤 (초과하면 잘리고, 부족하면 패딩)                  |
| `padding`        | 길이가 짧은 입력은 `max_length`에 맞춰 0으로 채움 (`"max_length"` 옵션) |
| `attention_mask` | 실제 입력 토큰은 1, 패딩된 부분은 0 → 모델이 패딩을 무시하게 함                |


### 4-4. batch

**배치(Batch)**

- 모델은 한 번에 하나의 데이터를 처리할 수도 있지만, 보통은 여러 개의 데이터를 묶어서 한 번에 처리함.
- 이렇게 묶인 데이터 덩어리를 **배치(batch)**라고 함.
- 한 번에 처리할 데이터의 개수를 **배치 크기(batch size)**라고 부름.
  - 예: `batch_size=8`이면 문장 8개를 동시에 처리

- 굳이 배치 처리를 하는 이유는?
  - 연산 속도 향상 (병렬 처리)
    - GPU는 데이터를 한 번에 많이 처리할수록 효율이 높다.
    - 여러 문장을 **배치(batch)**로 묶어 처리하면 **병렬 연산**이 가능해져 훨씬 빠름.
.


```python
# 배치 처리가 가능
tokenizer(my_data['train']['text'])
```




    {'input_ids': [[101, 1045, 9544, 2437, 6567, 2241, 2006, 7961, 1998, 7863, 8866, 1012, 102], [101, 1045, 2467, 5136, 2129, 2500, 2453, 2514, 2043, 2437, 1037, 3247, 1012, 102], [101, 2951, 1998, 4106, 3298, 2087, 1997, 2026, 6567, 1012, 102], [101, 1045, 11160, 2006, 2026, 26452, 1998, 3167, 5300, 2000, 5009, 2026, 9804, 1012, 102]], 'attention_mask': [[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]]}



---

## 5. preprocess_function

인공지능의 입력으로 가정된 두가지 경우
1. 토크나이징 결과
2. 토크나이징 결과 + label (결과에 loss 값이 추가됨)
  - 이와 같은 형태의 입력을 정리하기 위해서는  `{'text':[...], 'label':[...]}` 이러한 형태로 정리된 자료를 `{'text':[...], 'label':[...], 'input_ids':[...], 'attention_mask':[...]}` 이러한 형태로 만들어야 함.
  - 이를 쉽게처리해주는 함수가 바로 `my_data.map()`



```python
# 데이터 전처리
def preprocess_function(examples):
    return tokenizer(examples["text"], truncation=True)

tokenized_my_data = my_data.map(preprocess_function, batched=True)
```


    Map:   0%|          | 0/4 [00:00<?, ? examples/s]



    Map:   0%|          | 0/2 [00:00<?, ? examples/s]



```python
tokenized_my_data['train'][0]
```




    {'text': 'I prefer making decisions based on logic and objective facts.',
     'label': 0,
     'input_ids': [101,
      1045,
      9544,
      2437,
      6567,
      2241,
      2006,
      7961,
      1998,
      7863,
      8866,
      1012,
      102],
     'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}



---

## 6. data_collator

전처리된 데이터가 인공지능의 입력에는 맞지 않음.


```python
tokenized_my_data['train'][0]
```




    {'text': 'I prefer making decisions based on logic and objective facts.',
     'label': 0,
     'input_ids': [101,
      1045,
      9544,
      2437,
      6567,
      2241,
      2006,
      7961,
      1998,
      7863,
      8866,
      1012,
      102],
     'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}



이유: 인공지능은 `torch.tensor()` 자료형을 가지며 (n,m)의 행렬로 정리된 “묶음” 형태의 자료형을 기대함.


### 6-1. 데이터 전처리 단계별 비교 (Tokenizer → DataCollator)



| 항목               | 주어진 자료 (`text`, `label`)         | 전처리된 자료 (`tokenizer`, `map`)                  | 더 전처리된 자료 (`data_collator`)                   |
|--------------------|----------------------------------------|-----------------------------------------------------|-------------------------------------------------------|
| **Dict의 Keys**     | `text`, `label`                        | `input_ids`, `attention_mask`, `label`              | `input_ids`, `attention_mask`, `labels`              |
| **자료의 형태**      | 텍스트, 라벨                           | 숫자화 O, 행렬화 X                                  | 숫자화 O, 행렬화 O                                   |
| **torch.tensor**    | -                                      | ❌                                                  | ✅                                                   |
| **미니배치 지원**    | -                                      | ❌                                                  | ✅                                                   |
| **패딩/동적패딩**    | -                                      | ❌                                                  | ✅                                                   |
| **예측할 때**        | 강인공지능의 입력                      | 트레이너의 입력                                     | 인공지능의 입력                                      |


- 데이터 콜렉터에서 우리가 기대하는 것
  -  [Dict,Dict,Dict,Dict] 로 되어있는 경우 (tokenized_my_data['train'])
    - (4,??) shape 텐서의 `input_ids`
    - (4,??) shape 텐서의 `attention_mask`
    - (4,) shape 텐서의 `labels`로 변환해줌.


```python
from transformers import DataCollatorWithPadding
data_collator = DataCollatorWithPadding(tokenizer=tokenizer)
# 우리에게 필요한 입력 형태 : [Dict, Dict, Dict, ...]
```


```python
data_collator(
    [
        # 샘플마다 길이가 다르기 때문에 모델이 그대로 처리할 수 없음
        dict(label=0, input_ids=[101,1045,102],attention_mask=[1,1,1]),
        dict(label=1, input_ids=[101,1045,9544,102],attention_mask=[1,1,1,1]),
        dict(label=0, input_ids=[101,1045,9544,9544,102],attention_mask=[1,1,1,1,1])

    ]
)
```




    {'input_ids': tensor([[ 101, 1045,  102,    0,    0],
            [ 101, 1045, 9544,  102,    0],
            [ 101, 1045, 9544, 9544,  102]]), 'attention_mask': tensor([[1, 1, 1, 0, 0],
            [1, 1, 1, 1, 0],
            [1, 1, 1, 1, 1]]), 'labels': tensor([0, 1, 0])}



-> 모든 입력이 동일한 길이로 맞춰졌고, 모델에 바로 넣을 수 있는 배치 형태의 텐서가 만들어짐.

---

## 7. compute_metrics (평가하기)


```python
def compute_metrics(eval_pred):
    predictions, labels = eval_pred
    predictions = np.argmax(predictions, axis=1)
    accuracy = evaluate.load("accuracy")
    return accuracy.compute(predictions=predictions, references=labels)
```


```python
accuracy.compute(predictions=[0,0,0], references=[0,0,0])
```




    {'accuracy': 1.0}




```python
accuracy.compute(predictions=[1,1,1], references=[0,0,0])
```




    {'accuracy': 0.0}




```python
accuracy.compute(predictions=[0,1,0], references=[0,0,0])
```




    {'accuracy': 0.6666666666666666}



---


```python
from google.colab import drive
drive.mount('/content/drive')
```

    Mounted at /content/drive



```python
!jupyter nbconvert --to markdown ["/content/drive/MyDrive/Colab Notebooks/"/"IMDB_영화평_감성분석"].ipynb
```

    [NbConvertApp] WARNING | pattern '[/content/drive/MyDrive/Colab Notebooks//IMDB_영화평_감성분석].ipynb' matched no files
    This application is used to convert notebook files (*.ipynb)
            to various other formats.
    
            WARNING: THE COMMANDLINE INTERFACE MAY CHANGE IN FUTURE RELEASES.
    
    Options
    =======
    The options below are convenience aliases to configurable class-options,
    as listed in the "Equivalent to" description-line of the aliases.
    To see all configurable class-options for some <cmd>, use:
        <cmd> --help-all
    
    --debug
        set log level to logging.DEBUG (maximize logging output)
        Equivalent to: [--Application.log_level=10]
    --show-config
        Show the application's configuration (human-readable format)
        Equivalent to: [--Application.show_config=True]
    --show-config-json
        Show the application's configuration (json format)
        Equivalent to: [--Application.show_config_json=True]
    --generate-config
        generate default config file
        Equivalent to: [--JupyterApp.generate_config=True]
    -y
        Answer yes to any questions instead of prompting.
        Equivalent to: [--JupyterApp.answer_yes=True]
    --execute
        Execute the notebook prior to export.
        Equivalent to: [--ExecutePreprocessor.enabled=True]
    --allow-errors
        Continue notebook execution even if one of the cells throws an error and include the error message in the cell output (the default behaviour is to abort conversion). This flag is only relevant if '--execute' was specified, too.
        Equivalent to: [--ExecutePreprocessor.allow_errors=True]
    --stdin
        read a single notebook file from stdin. Write the resulting notebook with default basename 'notebook.*'
        Equivalent to: [--NbConvertApp.from_stdin=True]
    --stdout
        Write notebook output to stdout instead of files.
        Equivalent to: [--NbConvertApp.writer_class=StdoutWriter]
    --inplace
        Run nbconvert in place, overwriting the existing notebook (only
                relevant when converting to notebook format)
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory=]
    --clear-output
        Clear output of current file and save in place,
                overwriting the existing notebook.
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory= --ClearOutputPreprocessor.enabled=True]
    --coalesce-streams
        Coalesce consecutive stdout and stderr outputs into one stream (within each cell).
        Equivalent to: [--NbConvertApp.use_output_suffix=False --NbConvertApp.export_format=notebook --FilesWriter.build_directory= --CoalesceStreamsPreprocessor.enabled=True]
    --no-prompt
        Exclude input and output prompts from converted document.
        Equivalent to: [--TemplateExporter.exclude_input_prompt=True --TemplateExporter.exclude_output_prompt=True]
    --no-input
        Exclude input cells and output prompts from converted document.
                This mode is ideal for generating code-free reports.
        Equivalent to: [--TemplateExporter.exclude_output_prompt=True --TemplateExporter.exclude_input=True --TemplateExporter.exclude_input_prompt=True]
    --allow-chromium-download
        Whether to allow downloading chromium if no suitable version is found on the system.
        Equivalent to: [--WebPDFExporter.allow_chromium_download=True]
    --disable-chromium-sandbox
        Disable chromium security sandbox when converting to PDF..
        Equivalent to: [--WebPDFExporter.disable_sandbox=True]
    --show-input
        Shows code input. This flag is only useful for dejavu users.
        Equivalent to: [--TemplateExporter.exclude_input=False]
    --embed-images
        Embed the images as base64 dataurls in the output. This flag is only useful for the HTML/WebPDF/Slides exports.
        Equivalent to: [--HTMLExporter.embed_images=True]
    --sanitize-html
        Whether the HTML in Markdown cells and cell outputs should be sanitized..
        Equivalent to: [--HTMLExporter.sanitize_html=True]
    --log-level=<Enum>
        Set the log level by value or name.
        Choices: any of [0, 10, 20, 30, 40, 50, 'DEBUG', 'INFO', 'WARN', 'ERROR', 'CRITICAL']
        Default: 30
        Equivalent to: [--Application.log_level]
    --config=<Unicode>
        Full path of a config file.
        Default: ''
        Equivalent to: [--JupyterApp.config_file]
    --to=<Unicode>
        The export format to be used, either one of the built-in formats
                ['asciidoc', 'custom', 'html', 'latex', 'markdown', 'notebook', 'pdf', 'python', 'qtpdf', 'qtpng', 'rst', 'script', 'slides', 'webpdf']
                or a dotted object name that represents the import path for an
                ``Exporter`` class
        Default: ''
        Equivalent to: [--NbConvertApp.export_format]
    --template=<Unicode>
        Name of the template to use
        Default: ''
        Equivalent to: [--TemplateExporter.template_name]
    --template-file=<Unicode>
        Name of the template file to use
        Default: None
        Equivalent to: [--TemplateExporter.template_file]
    --theme=<Unicode>
        Template specific theme(e.g. the name of a JupyterLab CSS theme distributed
        as prebuilt extension for the lab template)
        Default: 'light'
        Equivalent to: [--HTMLExporter.theme]
    --sanitize_html=<Bool>
        Whether the HTML in Markdown cells and cell outputs should be sanitized.This
        should be set to True by nbviewer or similar tools.
        Default: False
        Equivalent to: [--HTMLExporter.sanitize_html]
    --writer=<DottedObjectName>
        Writer class used to write the
                                            results of the conversion
        Default: 'FilesWriter'
        Equivalent to: [--NbConvertApp.writer_class]
    --post=<DottedOrNone>
        PostProcessor class used to write the
                                            results of the conversion
        Default: ''
        Equivalent to: [--NbConvertApp.postprocessor_class]
    --output=<Unicode>
        Overwrite base name use for output files.
                    Supports pattern replacements '{notebook_name}'.
        Default: '{notebook_name}'
        Equivalent to: [--NbConvertApp.output_base]
    --output-dir=<Unicode>
        Directory to write output(s) to. Defaults
                                      to output to the directory of each notebook. To recover
                                      previous default behaviour (outputting to the current
                                      working directory) use . as the flag value.
        Default: ''
        Equivalent to: [--FilesWriter.build_directory]
    --reveal-prefix=<Unicode>
        The URL prefix for reveal.js (version 3.x).
                This defaults to the reveal CDN, but can be any url pointing to a copy
                of reveal.js.
                For speaker notes to work, this must be a relative path to a local
                copy of reveal.js: e.g., "reveal.js".
                If a relative path is given, it must be a subdirectory of the
                current directory (from which the server is run).
                See the usage documentation
                (https://nbconvert.readthedocs.io/en/latest/usage.html#reveal-js-html-slideshow)
                for more details.
        Default: ''
        Equivalent to: [--SlidesExporter.reveal_url_prefix]
    --nbformat=<Enum>
        The nbformat version to write.
                Use this to downgrade notebooks.
        Choices: any of [1, 2, 3, 4]
        Default: 4
        Equivalent to: [--NotebookExporter.nbformat_version]
    
    Examples
    --------
    
        The simplest way to use nbconvert is
    
                > jupyter nbconvert mynotebook.ipynb --to html
    
                Options include ['asciidoc', 'custom', 'html', 'latex', 'markdown', 'notebook', 'pdf', 'python', 'qtpdf', 'qtpng', 'rst', 'script', 'slides', 'webpdf'].
    
                > jupyter nbconvert --to latex mynotebook.ipynb
    
                Both HTML and LaTeX support multiple output templates. LaTeX includes
                'base', 'article' and 'report'.  HTML includes 'basic', 'lab' and
                'classic'. You can specify the flavor of the format used.
    
                > jupyter nbconvert --to html --template lab mynotebook.ipynb
    
                You can also pipe the output to stdout, rather than a file
    
                > jupyter nbconvert mynotebook.ipynb --stdout
    
                PDF is generated via latex
    
                > jupyter nbconvert mynotebook.ipynb --to pdf
    
                You can get (and serve) a Reveal.js-powered slideshow
    
                > jupyter nbconvert myslides.ipynb --to slides --post serve
    
                Multiple notebooks can be given at the command line in a couple of
                different ways:
    
                > jupyter nbconvert notebook*.ipynb
                > jupyter nbconvert notebook1.ipynb notebook2.ipynb
    
                or you can specify the notebooks list in a config file, containing::
    
                    c.NbConvertApp.notebooks = ["my_notebook.ipynb"]
    
                > jupyter nbconvert --config mycfg.py
    
    To see all available configurables, use `--help-all`.
    



```python
!jupyter nbconvert --to html "/content/drive/MyDrive/Colab Notebooks/Save_Colab_Notebook_as_html_markdown.ipynb"
```

## 8. ref

본 포스트는 개인 학습 목적으로 작성하였으며, Hugging Face의 공식 문서(https://huggingface.co/docs/transformers/tasks/sequence_classification) 와
전북대학교 MP2024 과목의 강의 자료를 참고하였습니다.

