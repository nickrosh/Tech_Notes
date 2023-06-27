
## NLP Preprocessing

"Cleaning unstructured text data sucks" - Abraham Lincoln.

#### Special Characters

Special characters are a nuisance and are usually removed. This can be done with simple [Regex](../../Software%20Engineering/Regex.md) patterns.

#### Stop Words

Sometimes, some extremely common words which would appear to provide little value can be removed to focus on the important words instead. If you search "how to develop apps", the engine would look much harder at "develop" and "apps" than "to" which is so common in English, it would yield useless information. This can be done for developing a training dataset, or information retrieval, or text summarization, or any other purpose. You will want to decide the stop word list carefully for the exact application

#### Stemming

Stemming is a process that reduced an inflected word down to its word stem. "Programming", "programmer", and "programs" would all be stemmed down to the common form "program". This is done using general heuristics.

#### Lemmatization

Lemmatization is similar to stemming, but considers the context and converts the word to its meaningful base form, which is called a lemma. For example, "Caring" could return "Care". Lemmatization requires a lookup table, and thus is more computationally expensive.

#### TF-IDF

*Term Frequency-Inverse Document Frequency* or TF-IDF is a common statistical measure in NLP that evaluates how relevant a word is to a document in a collection of documents. It is calculated by multiplying two different metrics:
- the *term frequency* of a word in a document. There are several ways of calculating this frequency, with the simplest being a raw count of instances a word appears in a document. Then, there are ways to adjust the frequency, by length of a document, or by the raw frequency of the most frequent word in a document.
- The *inverse document frequency* of the word across a set of documents. This means, how common or rare a word is in the entire document set. The closer it is to 0, the more common a word is. This metric can be calculated by taking the total number of documents, dividing it by the number of documents that contain a word, and calculating the logarithm.
- So, if the word is very common and appears in many documents, this number will approach 0, otherwise it will approach 1.


## Tokenization

Text must be converted to a numerical format before it can be fed into a language model or [Embedding Algorithm](Embeddings.md). The traditional method is to assign a number to every word in the vocabulary according to its frequency. So the most frequent word would be 0, second most would be 1, and so on. The problem with word-level tokenization is that is struggles with rare or unseen words, also known as Out-of-Vocabulary (OOV). A possible solution is to instead tokenize by every individual character. character-level tokenization does not perform well as it is too granular and fine-grained to contain the important semantic information that is contained in natural language words. The middle ground of these strategies is *Subword Tokenization*, which combines the flexibility of characters and the semantic meaning of words.

For some reason, convergence is much faster when the vocabulary size is a multiple of 64.

#### Byte Pair Encoding

Huffman Coding tells us how to optimally encode symbols to compress the English Language. Entropy defines the minimum theoretically possible number of bits per symbol for a given data set. *Byte-Pair Encoding* (BPE) is an algorithm to extract subwords. 

![](../../Attachments/Pasted%20image%2020230308001213.png)

BPE relies on a pre-tokenizer that splits the training data into words first. This can be as simple as space separation, or advanced rule-based tokenization. Then the frequencies of every word is computed. Then each word is split into characters/symbols. The most common pairs (by sum of all frequencies among all words) are then merged. The number of merges done is a hyperparameter. 


## NLP Applications

#### Language Modeling

On your phone keyboard, the next word is predicted for every word. Next-word prediction is a task that can be addressed by a *Language Model*. A language model can take a list of words (let's say two words) and attempts to predict the next word. State-of-The Art [Transformers](Transformers.md) like GPT-3 are just gigantic language models. Like your cell phone keyboard, but orders of magnitude more data and parameters.  This causal language modeling is the task on which decoder transformer models are pretrained on.

![](../../Attachments/Pasted%20image%2020230307224744.png)

*Perplexity* is a common way to [evaluate](../ML%20Metrics.md) Language Models. In short, perplexity is a measurement of how well a probability model predicts a sample. In the context of NLP, perplexity is one way to evaluate language models. A language model is a probability distribution over sentences, and the best LM is the one that best predicts an unseen test set

Entropy is the average number of bits to encode the information contained in a random variable, so the exponentiation of the entropy should be the total amount of all possible information, or more precisely, the weighted average number of choices a random variable has. For example, if the average sentence in the test set could be coded in 100 bits, the model perplexity is $2^{100}$ per sentence.


#### Masked Language Modeling

In causal language modeling, the model is able to look at all past words in the input and any previously generated words. This presents a problem however when we want to look at the total context of a word in a sentence. This is where *Masked Language Modeling* comes in. Instead of predicting the next word in the sentence, one word in the input is masked out and based on ALL words before and after, the missing word is predicted. This is the task on which Encoder models like BERT are pre-trained. This is why BERT is called "Bidirectional Encoder Representations from Transformers".

![](../../Attachments/Pasted%20image%2020230519221352.png)

#### Machine Translation

Translation between different languages has been the impetus for most of our greatest advancements. [Attention](Attention.md) was invented to improve the performance of [Recurrent Neural Net](Recurrent%20Neural%20Net.md) models. Then [Transformers](Transformers.md) were originally made to deal with translation. 

![](../../Attachments/Pasted%20image%2020230309020500.png)


#### Text Classification & Sentiment Analysis

Being able to take the "sentiment" from unstructured text can be very useful for gauging user satisfaction from reviews, or results of a survey, or anything that takes free text.


#### Text Embeddings & Semantic Search