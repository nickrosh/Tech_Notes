
The *Transformer* architecture, proposed in the famous "Attention is All You Need" paper is the state-of-the art architecture that depends solely on [Attention](Attention.md). The Transformer has no [recurrence](Recurrent%20Neural%20Net.md) or [convolutions](Convolutional%20Neural%20Net.md). This use of Attention allows the Transformer to compute a training step in $O(1)$ [Time](../../Data%20Structures%20&%20Algorithms/Time%20&%20Space%20Complexity.md) compared to $O(n)$ of a RNN. This parallelizability is what has made Transformers the de-facto [NLP](NLP.md) deep learning model, with bleeding edge models in the hundreds of billions of parameters and trillions of training tokens.


![](../../Attachments/Pasted%20image%2020230309005124.png)

On a high-level, the transformer has an encoding component, a decoding component, and connections between them. The encoding component is a stack of encoders. The decoding component is a stack of decoders of the same number

![](../../Attachments/Pasted%20image%2020230309010228.png)

The encoders are all identical in structure (yet they do not share weights). Each one is broken down into two sub-layers. The encoder's inputs first flow through a [self-attention](Attention.md) layer- a layer that helps the encoder look at other words in the input sentence as it encodes a specific word. The outputs of the self-attention layer are fed to a feed-forward [Neural Network](Neural%20Networks.md). The exact same feed-forward net is independently applied to each position. The decoder has both those layers, but between them is an attention layer that helps the decoder focus on relevant parts of the input sentence (similar to what attention does in [seq2seq](Recurrent%20Neural%20Net.md) models).

![](../../Attachments/Pasted%20image%2020230309010604.png)

The encoder starts by processing the input sentence. The out of the top encoder is then transformed into a set of attention vectors K and V. These are to be used by each decoder in its "Encoder-Decoder Attention" layer which helps the decoder focus on appropriate places in the input sequence. The Encoder-Decoder Attention layer works just like multiheaded self-attention, except it creates its Query matrix from the layer below it, and takes the Key and Value matrix from the output of the encoder stack. The Self-attention in the decoder is [masked](Attention.md), meaning it will mask out any values that have not been generated yet.

## Model Components

The heart of Transformers is [Attention](Attention.md). Self-attention allows the model to focus on exactly what is relevant in the input and output of the model. But there are other components to the model that are critical to performance and stability

#### Feed-Forward Blocks

In addition to attention, each layer has a feed-forward [Neural Network](Neural%20Networks.md) block: two linear layers with [ReLU](Activation%20Functions.md) non-linearity between them. After looking at other tokens via an attention mechanism, a model uses an FFN block to process this new information (attention: "look at other tokens and gather information", FFN: "take a moment to think and process this information")

![](../../Attachments/Pasted%20image%2020230309011353.png)

#### Residual Connections

Residual connections are when we add a block's input to it's output. Residual connections are very useful: they ease the gradient flow through a network and allow stacking a lot of layers. In Transformers, residual connections are used after each attention and FFN block.

![](../../Attachments/Pasted%20image%2020230309011536.png)

#### Layer Normalization

The "Norm" part in the "Add + Norm" layer denotes *Layer Normalization*. It independently normalizes vector representations of each example in the batch. As opposed to batch normalization which takes mini-batch statistics, Layer Norm takes its statistics from all of the activations arising from a single example. This makes it usable for sequential data.

![](../../Attachments/Pasted%20image%2020230309011856.png)

In the transformer, you have to normalize vector representation of each token. Additionally, here Layer Norm has trainable parameters, *scale* and *bias*, which are used after normalization to rescale the layer's outputs (or the next layer's inputs). Note that $\mu$ and $\sigma$ are evaluated for each example, but *scale* and *bias* are the same- these are layer parameters. 

![](../../Attachments/Pasted%20image%2020230309012141.png)

#### Positional Encoding

One thing that's missing from the model as we've described it so far is a way to account for the order of the words in the input sequence. To address this, the transformer adds a vector to each input [Embedding](Embeddings.md). These vectors follow a specific pattern that the model learns, which helps it determine the position of each word, or the distance between different words in the sequence. The intuition here is that adding these values to the embeddings provides meaningful distances between the embedding vectors once they're projected onto Q/K/V vectors and during dot-product attention.

![](../../Attachments/Pasted%20image%2020230309012542.png)

We can use simple $\sin$ and $\cos$ to create encoding for any input. Each dimension of the positional encoding corresponds to a sinusoid, and the wavelengths form a geometric progression from 2 pi to 20000 pi

![](../../Attachments/Pasted%20image%2020230309012834.png)

#### Final Linear & SoftMax Layer

The decoder stack outputs a vector of floats. How do we turn that into a word? That's the job of the final Linear layer, which is followed by a softmax layer. The linear layer is a simple fully connected neural network that projects the vector produced by the stack of decoders, into a much larger vector called a logits vector. Let's assume that our model knows 10,000 unique English words (our model's "output vocabulary") that it's learned form its training dataset. This would make the logits vector 10,000 cells wide- each cell corresponding to the score of a unique word. The softmax layer then turns these into probabilities.

![](../../Attachments/Pasted%20image%2020230309013629.png)

If we want a model to output "I am a student", and we train it on enough data, we should expect the produced probability distribution to look like this:

![](../../Attachments/Pasted%20image%2020230309013904.png)

Now, because the model produces the outputs one at a time, we can assume that the model is selecting the word with the highest probability from that probability distribution and throwing away the rest. That's one way to do it (called greedy decoding). Another way to do it would be to hold on to, say, the top two words (say, "I" and "a" for example), then in the next step, run the model twice: once assuming the first output position was the word "I" and another time assuming the first output position was the word "a", and whichever version produces less error considering both positions #1 and #2 is kept. We repeat this for positions #2 and #3 … etc. This method is called *Beam Search*, where in our example the beam size was 2.


## Architectures

Different Transformer architectures have arisen from only using encoders, decoders, or using both like the original architecture

![](../../Attachments/Pasted%20image%2020230309015202.png)

#### Encoder Only

When only the encoder is used, a sequence of input tokens in converted into the same number of representation that can be further projected into output (e.g. [Classification](../Classification.md)). A Transformer encoder of self-attention layers, where all input tokens attend to each other. This is famously used by BERT

#### Encoder-Decoder

Since a Transformer encoder converts a sequence of input tokens into the same number of output representations, the encoder-only mode cannot generate a sequence of arbitrary length like in machine translation. As originally proposed for machine translation, the Transformer architecture can be outfitted with a decoder that autoregressively predicts the target sequence of arbitrary length, token by token, conditional on both encoder output and decoder output
- For conditioning on encoder output, encoder-decoder cross attention allows target tokens to attend to *all* input tokens.
- Conditioning on decoder output is achieved by the masked self-attention in the decoder block, where any target token can only attend to *past* and *present* tokens in the target sequence.


#### Decoder Only

Decoder-only Transformers, like GPT, remove the entire encoder block and cross attention layer in the decoder block. These are the best models for large-scale [language modelling](NLP.md), which leverages the world's abundant unlabeled text corpus via self-supervised learning.
