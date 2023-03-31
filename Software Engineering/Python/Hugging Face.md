
*Hugging Face* is a [Transformers](../../Machine%20Learning/Deep%20Learning/Transformers.md) library in Python that integrates easily with [PyTorch](PyTorch.md) and comes with easy usage and [Preprocessing](../../Machine%20Learning/ML%20Engineering/Preprocessing.md) capabilities, especially for [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) use cases.

![](../../Attachments/Pasted%20image%2020230317005142.png)

At the highest level, the `pipeline` function let's you do an entire Transformer inference in one function. This function is a combination of [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) Tokenizer, the [Transformer](../../Machine%20Learnig/Deep%20Learning/Transformers.md) Model itself, and the postprocessing including turning the transformer output logits to [class](../../Machine%20Learning/Classification.md) probabilities with [Softmax](../../Machine%20Learning/Deep%20Learning/Activation%20Functions.md).

## Model

Hugging Face Transformers always output Logits. It is up to you to postprocess the logits. The *AutoModel* class and all of its relatives are simple wrappers over a wide variety of models available in the library. It's a clever wrapper as it can automatically guess the appropriate model architecture for your checkpoint, and then instantiates a model with this architecture.

The *AutoModel* class takes either a checkpoint (cloud download of model) or model files from a local folder. The files are a config file, and a model file. The configuration file explains what type of model and what model class to use. With just the config file, the model is instantiated to random weights. Then you use `from_pretrained()` to take the saved parameters from the model file to instantiate the model.

The configuration of a model is its blueprint, containing everything needed to create the model architecture.


## Tokenizer

The *AutoTokenizer* class allows you to load pretrained tokenizers, similar to AutoModel. The Tokenizer and Model must always be from the same checkpoint. You can load in a pretrained tokenizer and then apply it to your input text. You must also set the return tensor of the tokenizer to `return_tensors=pt` for [PyTorch](PyTorch.md). 

In order to handle multiple sequences of different lengths, we need to make them equal. This can be done by either truncating the longer inputs or padding shorter inputs, the latter being more preferable. The library will append zeros to the input sequences to make every single input the same length. Then, an attention mask will be set to zero wherever there is padded zeroes, and 1 wherever there is real tokens. This prevents the model's [Attention](../../Machine%20Learning/Deep%20Learning/Attention.md) mechanism from looking at the padding tokens. This is all taken care of behind the scenes when you set `padding=True`.


## Datasets

the `datasets` library contains a simple API to download and cache datasets from the cloud. All the datasets are saved to disk as Apache Arrow files, so they are also not volatile if [Memory & Cache](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) crashes. You can apply a tokenizer function to a dataset in batches by using the `map` function and setting `batched=True`.


## Preprocessing

Besides just Tokenizers, we usually need other preprocessing before training. A common [NLP](../../Machine%20Learning/Deep%20Learning/NLP.md) task is classification tasks of pairs of sentences. This is 8 of the 10 GLUE benchmarks. This is easily done with tokenizers by simply using two inputs to the tokenizer function. The tokenizer will mark all the tokens of the first sentence with `token_id_type=0` and all the tokens of the second sentence as `token_id_type=1`, this way the model knows which sentence is supposed to go first.