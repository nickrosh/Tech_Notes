
How do you represent [natural language](NLP.md) as numbers? Machine learning models can only accept numbers as inputs, which is relatively simple to accomplish with [Computer Vision](Computer%20Vision.md) as we just use the RGB values of a picture, but what about English words?

The naive solution is one-hot encoding, but this is extremely sparse, and doesn't provide any information on the meaning of the words. *Embeddings* are how to represent a word (via [Tokenization](NLP.md)) as a vector which also contains its semantic meaning based on its similarity to other words. Each dimension of an embedding vector cannot be connected to any real concept, like color  or age or something, they are mathematical concepts.

Any two vectors can be compared with *cosine similarity*, an angle distance function over N dimensions. This provides two key takeaways:
- We can represent words or any sequential data as vectors of numbers (which can be consumed by ML models)
- We can easily calculate how similar vectors are to each other with cosine similarity.

Embeddings are the formalization of analogies. We can look at all similar words to a chosen word by its embedding. The famous example of this is the formula:
$$\text{King}-\text{Man}+\text{Woman}=\text{Queen}$$
if you sum the embedding vectors for "King" and "Woman" and subtract the embedding for "Man", the resulting vector is closest to "Queen".

## Word2Vec

With the *Word2Vec* embedding algorithm, words get their embeddings by looking at which other words they tend to appear next to. The mechanics of that is simply:
1. We get a lot of text data (say, the entire corpus of Wikipedia)
2. We have a window (say, of three words) that we slide against all of that text.
3. The sliding window generates training samples for our model.

Say our [Sliding Window](../../Data%20Structures%20&%20Algorithms/Patterns/Sliding%20Window.md) looks at the previous 2 words and next 2 words of a target word, like the following:
![](../../Attachments/Pasted%20image%2020230307224413.png)

If we do this, the dataset we're virtually building and training the model against would look like this:
![](../../Attachments/Pasted%20image%2020230307224450.png)

This is called a *Continuous Bag of Words*, and is described in one of the word2vec papers. This is a simple way of generating training examples for the [language model](NLP.md). Another architecture that tends to work better does things differently. Instead of guessing a word based on its context (the words before and after it), this other architecture tries to guess neighboring words using the current word. We can think of the window it slides against the training text as looking like this:

![](../../Attachments/Pasted%20image%2020230307225225.png)

The window creates a separate training example for every other word than the target. This method is called the *Skipgram* architecture. If we slid the window from start until the end, we would end up with the following training dataset:

![](../../Attachments/Pasted%20image%2020230307225403.png)

Now let's look at how to use a basic neural language model that predicts the neighboring word:

![](../../Attachments/Pasted%20image%2020230307225624.png)

The model conducts the three steps and outputs a prediction vector (with a probability assigned to each word in its vocabulary). Since the model is untrained, it's prediction is sure to be wrong at this stage. But that's okay. We know what word it should have guessed: the label/output cell in the row we're currently using to train the model. Taking the difference of the actual target and model prediction gives us the loss vector. We can then use this loss vector to [update the model parameters](Optimizers.md):

![](../../Attachments/Pasted%20image%2020230307225918.png)

Recall how our model predicts its prediction and then projecting the prediction to the output vocabulary. This step is very computationally expensive, especially knowing that we have to do it once for every training sample in our dataset. We need to do something to improve performance. One way is to split our training into two steps:
1. Generate high-quality word embeddings (don't worry about next-word prediction)
2. Use these high-quality embeddings to train a language model (to do next-word prediction)

To generate high-quality embeddings, we can switch the model's task from predicting a neighboring word into a model that takes the input and output words and outputs a score indicating if they're neighbors or not (0 for "not neighbors", 1 for "neighbors"):

![](../../Attachments/Pasted%20image%2020230307230420.png)

This simple switch changes the model we need from a [Neural Network](Neural%20Networks.md) to a simple [Logistic Regression](../Logistic%20Regression.md), which is much faster to train. This switch also requires a change to our dataset. Now each of our input-output pairs must be labelled as either "neighbor" or "not neighbor". In the dataset we've created with the sliding window, every single example is a "neighbor". To generalize the model, we must introduce *negative samples* to our dataset: samples of words that are not neighbors. Our model needs to return 0 for those samples. To generate these negative samples, we randomly sample words from our vocabulary:

![](../../Attachments/Pasted%20image%2020230307230740.png)

This combined strategy is called *Skipgram with Negative Sampling* (SGNS).

#### Training Word2Vec

Now we look at the training process for this algorithm. Before training starts, we pre-process the text we're training against. We determine the size of the vocabulary. We also choose the embedding vector size, 300 is common. We create two matrices, an Embedding matrix, and a Context matrix:

![](../../Attachments/Pasted%20image%2020230307231034.png)

At the start of training, we initialize these matrices with random values. In each training step, we take one positive example and its negative examples. We proceed to look in the "embedding" matrix. For the context words, we look in the "context" matrix. We then take the dot product of the input and output. This value gives us similarity. We then feed it to sigmoid to get a probability:

![](../../Attachments/Pasted%20image%2020230307231230.png)

This concludes a single training step. We continue and cycle through the entire dataset for a number of times. We can then stop training and discard the "context" matrix, and use the "embeddings" matrix as our pre-trained embeddings for our desired next task.

Two key hyperparameters for Word2Vec is the window size and the number of negative samples per positive sample. One heuristic is that smaller window sizes (2-15) lead to embeddings where similar words are interchangeable, versus larger windows (15+) which just represent similarity. The number of negative samples is another factor, generally ranging from 2-15. 2-5 seems to be enough when you have a large enough dataset. The Gensim default for both hyperparameters is 5.


## GloVe

Word embedding with Global Vectors or *GloVe* is another algorithm to generating embeddings. A primary drawback to Word2Vec is that it relies only on *local context*, as the sliding window only gets a few words before and after the target. GloVe captures both global statistics and local statistics of a corpus.

GloVe takes the matrix of the word-word co-occurrences of every single word. The co-occurrence is how many times word X has been used around word Y. Here is the co-occurrence matrix for the sentence "the cat sat on the mat", with a window size of 1:

![](../../Attachments/Pasted%20image%2020230307233911.png)

This matrix is used along with local statistics to calculate the loss function for the embedding vectors. This provides theoretically better performance than word2vec.