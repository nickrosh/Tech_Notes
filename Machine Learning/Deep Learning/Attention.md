
*Attention*: at different steps, let a model "focus" on different parts of the input. An attention mechanism is a part of a neural network

## Encoder-Decoder Attention

Encoder-Decoder Attention (or Cross Attention) allows the model to focus on the relevant parts of the input sequence as needed. This is used in the [seq2seq](Recurrent%20Neural%20Net.md) model to provide the entire encoder context to the decoder. This is also used similarly in [Transformers](Transformers.md) as the cross attention between encoder and decoder blocks.

![](../../Attachments/Pasted%20image%2020230308235908.png)

At each time step:
1. The attention decoder RNN takes in the [Embedding](Embeddings.md) for the \<end\> token, and an initial decoder hidden state
2. The RNN processes its inputs, producing an output and a new hidden state. The output is discarded
3. Attention Step: we use the encoder hidden states and the previous decoder hidden state to calculate a context vector for this time step
4. We concatenate the hidden state and context vectors into one vector
5. We pass this combined vector through a [feedforward neural network](Neural%20Networks.md) (trained jointly with the model)
6. The output of the feedforward network indicates the output word of this time step
7. Repeat for the next time steps

The main takeaway is that a network can learn which input parts are more important at each step. Since everything here is differentiable (attention function, [SoftMax](Activation%20Functions.md), etc.), a model with attention can be trained end-to-end. You don't need to specifically teach the model to pick the words you want. The model itself will learn to pick important information.
 
## Self Attention

The [Transformer](Transformers.md) architecture is based solely on attention mechanisms: i.e. without [recurrence](Recurrent%20Neural%20Net.md) or [convolutions](Convolutional%20Neural%20Net.md). *Self Attention* is one of the key components of the model. The difference between attention and self-attention is that self-attention operates between representations of the same nature, e.g. all encoder states in some layer. Say the following is an input sentence: "The animal didn't cross the street because it was too tired".

What does "it" refer to? As the model processes each word (each position in the input sequence), *self attention* allows it to look at other positions in the input sequence. Think of how maintaining a hidden state allows an RNN to incorporate its representations of previous words/vectors it has processed with the current one it's processing. Self Attention is the method the transformer uses to bake the "understanding" of other relevant words into the one we're currently processing

The first step of self-attention is to create three vectors from each of the encoder's input vectors ([Embeddings](Embeddings.md)). We create a *Query, Key,* and *Value* vector. These are created by multiplying the embedding by 3 matrices that we [train](Optimizers.md) during the training process.

![](../../Attachments/Pasted%20image%2020230309001943.png)

The second step in calculating self attention is to calculate a score. Say we're calculating the self-attention for the first word in this example, "thinking". We need to score each word of the input sentence against this word. The score determines how much focus to place on other parts of the input sentence as we encode a word at a certain position. The score is calculated by taking the dot product of the Query and Key vectors of the respective word we're scoring.

The third step is to divide the score by the square root of the dimension of the Key vector. This leads to having more stable gradients. The fourth step is to feed this through [SoftMax](Activation%20Functions.md) to [normalize](../Scaling.md)  the scores. The SoftMax score determines how much of each word will be expressed at this position. Clearly the word at this position will have the highest softmax score, but sometimes it's useful to attend to another word that is relevant to the current word.

![](../../Attachments/Pasted%20image%2020230309002458.png)

The fifth step is to multiply each value vector by the softmax score (in preparation to sum them up). The intuition here is to keep intact the values of the word(s) we want to focus on, and drown-out irrelevant words. The sixth step is to sum up the weighted value vectors. This produces the output of the self-attention layer at this position (for the first word).

![](../../Attachments/Pasted%20image%2020230309002728.png)

We thus arrive to the understanding of the three vectors. The Query matrix is used to compute the similarity between the current word and every other word in the sentence. The Key matrix is used to compute the similarity between every other word in the sentence and the current word. The Value matrix is used to store the actual value of each word.

The query is used when a token looks at others - it's seeking the information to understand itself better. The key is responding to the query's request: it is used to compute attention weights. The value is used to compute attention output: it gives information to the tokens which "say" they need it (i.e. assigned large weights to this token).

![](../../Attachments/Pasted%20image%2020230309002951.png)

The formal function for self-attention is:
$$Attention(Q,K,V) = softmax\left(\frac{QK^T}{\sqrt{d_K}}\right)V$$

#### Masked Attention

In the decoder, there's also a self-attention mechanism: it is the one performing the "look at the previous outputs" function. In the decoder, things are a little different. While the encoder receives all tokens at once and the tokens can look at all tokens in the input sentence, in the decoder, we generate one token at a time. During generation, we don't know which tokens we will generate in the future. To forbid the decoder from looking ahead, the model uses *Masked Self-Attention*. 

![](../../Attachments/Pasted%20image%2020230309003818.png)
Say we calculate the scores of the above features from its Key and Query vectors. The scores for future tokens are taken to negative infinity, so softmax takes it to zero.

![](../../Attachments/Pasted%20image%2020230309003849.png)

With an attention mask, we can ensure the decoder is not calculating tokens that haven't been generated yet.

![](../../Attachments/Pasted%20image%2020230309004659.png)

#### Multi-Head Attention

Usually, understanding the role of a word in a sentence requires understanding how it is related to different parts of the sentence. This is important not only in processing source sentence but also in generating target. Thus we perform attention multiple times with different "heads", concatenate them, then multiply them by a feed forward net.

![](../../Attachments/Pasted%20image%2020230309004345.png)

These multiple heads are critical for performance. They tend to specialize, like some may look at positional data of which words are ahead or behind of the target. Other heads may track syntactic data like noun-verb interactions. You simply split the Query, Key, and Value vectors into N equal parts for N-headed attention. This way, multi-head attention does not increase model size.