
*Hugging Face* is a [Transformers](../../Machine%20Learning/Deep%20Learning/Transformers.md) library in Python that integrates easily with [PyTorch](PyTorch.md) and comes with easy usage and [Preprocessing](../../Machine%20Learning/ML%20Engineering/Preprocessing.md) capabilities, especially for [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) use cases.

![](../../Attachments/Pasted%20image%2020230317005142.png)

At the highest level, the `pipeline` function let's you do an entire Transformer inference in one function. This function is a combination of [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) Tokenizer, the Transformer model itself, and the postprocessing including turning the transformer output logits to [class](../../Machine%20Learning/Classification.md) probabilities with [Softmax](../../Machine%20Learning/Deep%20Learning/Activation%20Functions.md).

## Model

Hugging Face Transformers always output Logits. It is up to you to postprocess the logits. The *AutoModel* class and all of its relatives are simple wrappers over a wide variety of models available in the library. It's a clever wrapper as it can automatically guess the appropriate model architecture for your checkpoint, and then instantiates a model with this architecture.

The *AutoModel* class takes either a checkpoint (cloud download of model) or model files from a local folder. The files are a config file, and a model file. The configuration file explains what type of model and what model class to use. With just the config file, the model is instantiated to random weights. Then you use `from_pretrained()` to take the saved parameters from the model file to instantiate the model.

The configuration of a model is its blueprint, containing everything needed to create the model architecture.

## Datasets

the `datasets` library contains a simple API to download and cache datasets from the cloud. All the datasets are saved to disk as Apache Arrow files, so they are also not volatile if [Memory & Cache](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) crashes. You can apply a tokenizer function to a dataset in batches by using the `map` function and setting `batched=True`.


## Preprocessing

Besides just Tokenizers, we usually need other [Preprocessing](../../Machine%20Learning/ML%20Engineering/Preprocessing.md) before training. A common [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) task is classification tasks of pairs of sentences. This is 8 of the 10 GLUE benchmarks. This is easily done with tokenizers by simply using two inputs to the tokenizer function. The tokenizer will mark all the tokens of the first sentence with `token_id_type=0` and all the tokens of the second sentence as `token_id_type=1`, this way the model knows which sentence is supposed to go first.


#### Tokenizer

The `AutoTokenizer` class allows you to load pretrained tokenizers, similar to AutoModel. The Tokenizer and Model must always be from the same checkpoint. You can load in a pretrained tokenizer and then apply it to your input text. You must also set the return tensor of the tokenizer to `return_tensors=pt` for [PyTorch](PyTorch.md). 

In order to handle multiple sequences of different lengths, we need to make them equal. This can be done by either truncating the longer inputs or padding shorter inputs, the latter being more preferable. The library will append zeros to the input sequences to make every single input the same length. Then, an attention mask will be set to zero wherever there is padded zeroes, and 1 wherever there is real tokens. This prevents the model's [Attention](../../Machine%20Learning/Deep%20Learning/Attention.md) mechanism from looking at the padding tokens. This is all taken care of behind the scenes when you set `padding=True`.

We can call `tokenizer` as a function and pass in the entire dataset. However, this would require us to load the entire dataset into memory first, and will return the data as a dictionary containing the keys, `input_ids`, `attention_mask`, and `token_type_ids`, and values that are lists of lists. To keep the data as a dataset, we can use the `Dataset.map()` method. This also allows us some extra flexibility, if we need more preprocessing done than just tokenization. The `map()` method works by applying a function on each element of the dataset, so let's define a function that tokenizes our inputs:

```python
def tokenize_function(example):
    return tokenizer(example["sentence1"], example["sentence2"], truncation=True)
```

The function takes a dictionary (like the items of our dataset) and returns a new dictionary with the keys `input_id`, `attention_mask`, and `tooken_type_ids`. Note that it also works if the `example` dictionary contains several samples (each key as a list of sentences) since the `tokenizer` works on lists of pairs of sentences. This will also allow us to use the option `batched=True` in our call to `map()` which will greatly speed up the tokenization:

```python
tokenized_datasets = raw_datasets.map(tokenize_function, batched=True)
```


#### Padding

Inputs into [Transformers](../../Machine%20Learning/Deep%20Learning/Transformers.md) must be the same token length. We accomplish this by truncating and padding sentences with special (PAD) tokens that are ignored by the model. There are several methods to do this. The most obvious one would be to pad all sentences to the length of the longest sentence in the whole dataset. This will make all batches have the same shape, however lots of batches with smaller sentences will have many columns that are useless pad tokens.

Alternatively, *Dynamic Padding* pads each sentence to the length of the longest sentence in the [batch](../../Machine%20Learning/Deep%20Learning/Optimizers.md). This way, all the batches will have the smallest size possible. The drawback however is that dynamic shapes don't work well on all [accelerators](../../Electrical%20Engineering/Digital/GPU.md).

The function that is responsible for putting together samples inside a batch is called a *collate function*. It's an argument you can pass when you build a `DataLoader`, the default being a function that will just convert your samples to [PyTorch](PyTorch.md) tensors and concatenate them (recursively if your elements are lists, tuples, or dictionaries). This won't be possible in our case since the inputs we have won't all be of the same size. We can deliberately postpone the padding, to only apply it as necessary on each batch and avoid having over-long inputs with a lot of padding. This will speed up training by quite a bit on [CPU](../../Electrical%20Engineering/Digital/CPU.md).

The Transformers library provides a function called `DataCollatorWithPadding` that will apply the correct amount of padding to the items of the dataset we want to batch together. It takes a tokenizer when you instantiate it (to know which padding token to use, and whether the model expects padding to be on the left or on the right of the inputs) and will do everything you need:

```python
from transformers import DataCollatorWithPadding
data_collator = DataCollatorWithPadding(tokenizer=tokenizer)
```


## Training

#### Fine-Tuning

Transformers provides a `Trainer` class to help you fine-tune any of the pretrained models it provides on your dataset. The hardest step here is just setting up your [GPU](../../Electrical%20Engineering/Digital/GPU.md) environment to run the training job correctly. The first step before defining the `Trainer` class is to define a `TrainingArguments` class that will contain all the hyperparameters the `Trainer` will use for training and evaluation. The only argument you have to provide is a directory where the trained model will be saved, as well as the checkpoints along the way. For all the rest, you can leave the defaults, which should work pretty well for a basic fine-tuning:

```python
from transformers import TrainingArguments
training_args = TrainingArguments("test-trainer")
```

The next step is to define the model. As explained above, we can use the `AutoModel` class for this. In this specific example, we use `AutoModelForSequenceClassification`, with two labels:

```python
from transformers import AutoModelForSequenceClassification
checkpoint = "bert-base-uncased"
model = AutoModelForSequenceClassification.from_pretrained(checkpoint, num_labels=2)
```

When this is run, you will receive a warning, as BERT has not been pretrained on classifying pairs of sentences, so the head of the pretrained model has been discarded and a new head suitable for sequence classification has been added instead. The warnings indicate that some weights were not used (the ones corresponding to the dropped pretrained head) and that some others were randomly initialized (the ones for the new head). It concludes by encouraging you to train the model, which is the next step.

Once we have a model, we can define a `Trainer` by passing all the objects constructed up to now, the `model`, the `training_args`, the training and validation datasets, our `data_collator`, and our `tokenizer`:

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

Not that when you pass the `tokenizer` as we did here, the default `data_collator` used by the `Trainer` will be a `DataCollatorWithPadding` as defined previously, so you can skip the line `data_collator=data_collator` in this call. A custom collator will be needed if you perform any other preprocessing. Finally, to fine-tune the model, we just have to call the `train()` method on the `Trainer`:

```python
trainer.train()
```

This will start the fine-tuning and report the training loss every 500 steps. It won't however, report any evaluation metrics. This is because:
1. We didn't tell the `Trainer` to evaluate during training by setting `evaluation_strategy` to either "steps" (evaluate every `eval_steps`) or "epoch" (evaluate at the end of each epoch).
2. We didn't provide the `Trainer` with a `compute_metrics()` function to calculate a metric during said evaluation (otherwise the evaluation would just have printed the loss, which is not a very intuitive number)

#### Evaluation

Let's see how to build the `compute_metrics()` function and use it the next time we train. The function must take an `EvalPrediction` object (which is a named tuple with a `predictions` field and a `label_ids` field) and will return a dictionary mapping strings to floats (the strings being the names of the metrics returned, and the floats their values). To get some predictions from our model, we can use the `Trainer.predict()` command:

```python
predictions = trainer.predict(tokenized_datasets["validation"])
print(predictions.predictions.shape, predictions.label_ids.shape)
```

The output of the `predict` method is another named tuple with three fields: `predictions`, `label_ids`, and `metrics`. The `metrics` field will just contain the loss on the dataset passed, as well as some time metrics (how long it took to predict, in total and on average). Once we complete our `compute_metrics()` function and pass it to the `Trainer`, that field will also contain the metrics returned by `compute_metrics()`. The [Transformers](../../Machine%20Learning/Deep%20Learning/Transformers.md) predictions are logits, and we must convert them into predictions that we can compare to our labels, we need to take the index with the maximum value on the second axis:
```python
import numpy as np
preds = np.argmax(predictions.predictions, axis=-1)
```

We can now compare those `preds` to the labels. To build our `compute_metric()` function, we will rely on the metrics from the *Evaluate* library:
```python
import evaluate
metric = evaluate.load("glue", "mrpc")
metric.compute(predictions=preds, references=predictions.label_ids)
```

Wrapping everything together, we get our `compute_metrics()` function:

```python
def compute_metrics(eval_preds):
    metric = evaluate.load("glue", "mrpc")
    logits, labels = eval_preds
    predictions = np.argmax(logits, axis=-1)
    return metric.compute(predictions=predictions, references=labels)
```

And now we add it our `Trainer` function to report metrics at the end of each epoch:

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
    compute_metrics=compute_metrics,
)
```

