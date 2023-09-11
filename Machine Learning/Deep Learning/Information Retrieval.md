
Information Retrieval, finding relevant data to a query, is an extremely important component of modern software, especially with the development of [Retrieval Augmented Generation](Retrieval%20Augmented%20Generation.md) systems.
## Sparse Vector Search

Traditional Sparse vector searches depend on exact-word matching and frequency to determine which documents are most "similar" to the user query. Sparse vector searches are extremely efficient and allow for efficient search over millions or billions of documents, and algorithms like TF-IDF and BM25 allow you to rank these documents according to which are deemed the most similar to the user query.

#### TF-IDF

*Term Frequency - Inverse Document Frequency* is a measure of importance of a word to a document in a corpus of documents, adjusted for the fact that some words appear more frequently in general. We'll make a simple but profoundly helpful assumption. We'll assume that the more times a document contains a term, the more likely it is to be *about* that term. That's to say, we'll use *Term Frequency (TF)*, the number of occurrences of a term in a document as a proxy for relevance. This one assumption creates a path for us to solve a seemingly impossible problem using simple math. It isn't perfect, but it is useful. So we'll view term frequency as a good thing. For a multi-term query like "cats and dogs", the simplest thing to do is to just take the sum of each of the term scores.

$$score(D,T) = termFrequency(D, T)$$

However if we scored a term purely on its frequency, longer documents and filler words would get an unfair advantage. To prevent filler words from dominating, we need some way of judging the *importance* of the terms in a query. Since we can't encode an understanding of natural language into our scoring function, we'll try to find a proxy for importance. Our best bet is *rarity*. If a term doesn't occur in most documents in a corpus, then whenever it does occur, we'll guess that this occurrence is significant. ON the other hand, if a term occurs in most of the documents in our corpus, then the presence of that term in any particular document will lose its value as an indicator of relevance. So high term frequency is good but is offset by high *Document Frequency (DF)*.
$$score(D,T) = \frac{termFrequency(D,T)}{docFrequency(T)}$$
What's wrong with this? Unfortunately, DF by itself says nothing. If DF for the term "Elephant" is 100, then is "elephant" a rare term or a common term? It depends on the size of the corpus. Instead of looking at DF by itself, let's look at N/DF, where N is the size of the search index or corpus.
$$score(D,T) = termFrequenct(D,T)*\frac{N}{docFrequency(T)}$$
Say we have 100 documents and "elephant" occurs in 1 of them while "giraffe" occurs in 2 of them. Both terms are rare, but the "elephant" N/DF would be twice as much as the "giraffe". Should a match for giraffe get half the score of elephant just because giraffe's document frequency is one higher than elephant's? When DF is in a very low range, small differences in DF can have a dramatic effect on the score. To smooth out the decline of N/DF when DF is in the lowest end of its range, we take the *log* of N/DF. Let's call log(N/DF) the *Inverse Document Frequency (IDF)*. We now arrive at the traditional definition of TF-IDF:
$$score(D,T) = termFrequenct(D,T)*\log(\frac{N}{docFrequency(T)})$$

#### BM25

*BM25* (*Best Matching 25*) is an improvement on TF-IDF. Simple TF-IDF rewards term frequency and penalizes document frequency. BM25 goes beyond this to account for document length and term frequency saturation.

We've established that Term Frequency is a good thing, but if a document contains 200 occurrences of "elephant", is it really *twice* as relevant as a document that contains 100 occurrences? We could argue that if "elephant" occurs a large enough number of times, say 100, the document is almost certainly relevant. To put it a different way, once a document is *saturated* with occurrences of a term, more occurrences shouldn't have a significant impact on the score. We'd like this contribution to increase fast when TF is small and then increase more slowly, approaching a limit, as TF gets very big. We introduce a parameter *k* that controls the shape of this saturation curve:
$$newTF = \frac{TF}{TF+k}$$

![](../../Attachments/Pasted%20image%2020230907153439.png)

A fortunate side effect of using term saturation is that we end up rewarding complete matches over partial ones. That is to say, we reward documents that match more of the terms in a multi-term query over documents that have lots of matches for just one of the terms.

The second key change is accounting for document length. For our new TF/(TF + k), as k increases, the value of TF/(TF + k) decreases. To penalize long documents, we can adjust k up if the document is longer than average, and adjust it down if the document is shorter than average. We'll achieve this by multiplying k by the ratio *DL/ADL* where DL is the document's length and ADL is the average document length across the corpus. When a document is is average length, the term is unaffected. For a shorter document k will be smaller and thus will saturate more quickly. When a document is longer, k will be increased and thus will saturate more slowly.

Do we always want to account for length however? What if there is a corpus where length matters a lot or others where it doesn't? We can treat the importance of document length as a second parameter. We achieve this tunability with a new parameter *b* (between 0 and 1). We now multiply k by the following:
$$1 - b + b\frac{DL}{ADL}$$
When b = 1, this reduces the multiplier to DL/ADL as what we had before. When b = 0, the whole term becomes 0 and the document length isn't considered at all. As b is cranked up from 0 towards 1, the multiplier responds more quickly to changes in DL/ADL.

#### SPLADE

*SPLADE (Sparse Lexical And Expansion)* combines the efficiency of sparse vectors and the semantic understanding of [Transformers](Transformers.md) to create more performing vectors. A [MLM](NLP.md) transformer like BERT will output the distributions of likely words for every input token i.

![](../../Attachments/Pasted%20image%2020230907174144.png)

SPLADE takes all of these distributions and aggregates them into a single distribution called the *importance estimation*. This importance estimation is the *sparse vector* produced by SPLADE. We can combine all these probability distributions into a single distribution that tells us the relevance of every taken in the vocabulary to our input sentence
$$w_j=\sum_{i\in t} \log(1 + RELU(w_{ij})$$
This allows us to identify relevant tokens that do not exist within the input sentence. For example, if we make the work "rainforest", we may return high predictions for the words "jungle", "land", and "forest". These words and their associated probabilities would then be represented in the SPLADE vector. This learned query/document expansion to include other relevant terms is a crucial advantage of SPLADE over traditional exact-match sparse methods like BM25. This helps avoid the vocabulary mismatch problem common in exact-match methods where synonyms for words are not considered.

![](../../Attachments/Pasted%20image%2020230907174949.png)

Compared to other sparse methods, SPLADE is slow. There are a greater number of non-zero values in SPLADE vectors compared to other sparse methods. This makes efficient calculation more difficult.

## Dense Vector Search

Dense vectors are learned [Embeddings](Embeddings.md) which encode rich contextual information about the text. This allows for abstract human-like reasoning like "King - Man + Woman = Queen". Unlike sparse vectors, dense vectors contain little to no zero values, and are thus much more computationally intensive to search. The vectors are inserted into an index and an approximate nearest neighbor algorithm is used to search through the vector space to find the most similar vectors to the query vector.

The simplest method to create a flat index which does not modify the indexes at all. This requires a full K nearest neighbor search through the entire vector space to retrieve vectors. Because there is no approximation or clustering of the vectors, these flat indexes produce the most accurate results. There is perfect search quality, but it comes at the cost of significant search times. This simple index can be used when search quality is an unquestionably high priority and search speed is less important. Search speed can be an irrelevant factor for smaller datasets, especially when using more powerful hardware.

#### LSH

*Locality Sensitive Hashing (LSH)* works by grouping vectors into buckets by processing each vector through a hash function that maximizes hashing collisions, rather than minimizing as is usual with [Hashing Functions](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). Why would we want to maximize collisions? Well, for search, we use LSH to group similar objects together. When we introduce a new query object (or vector), our LSH algorithm can be used to find the closest matching groups:

![](../../Attachments/Pasted%20image%2020230907183601.png)

However, LSH suffers from high dimensionality. It is not suitable to larger vectors >128, which makes it unsuitable for [BERT](Transformers.md) generated vectors. Instead, it is best used for low-dimensionality vectors and small indexes.

#### HNSW

*Hierarchical Navigable Small World (HNSW)* graphs are another, more recent development in search. HNSW-based ANNS consistently top out as the highest performing indexes. HNSW is a further adaption of navigable small world (NSW) graphs, where an NSW [Graph](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Graphs.md) contains vertices connected by edges connected to their nearest neighbors. When searching an NSW graph, we begin at a pre-defined *entry-point*. This entry point connects to several nearby vertices. We identify which of these vertices is the closest to our query vector and move there.

![](../../Attachments/Pasted%20image%2020230907190919.png)

We repeat the *greedy-routing search* process of moving from vertex to vertex by identifying the nearest neighboring vertices in each adjacency list. Eventually, we will find no nearer vertices than our current vertex, this is a local minimum and acts as our stopping condition. We are more likely to hit this stopping condition and reach a local minimum when traversing through vertices with a high number of neighbors. To minimize the probability of stopping early (and increase recall), we can increase the average degree of vertices, but his increases network complexity (and search time). So we need to balance the average degree of vertices between recall and search speed.

HNSW is a natural evolution of NSW, which borrows inspiration from hierarchical multi-layers from [Skip Lists](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Skip%20Lists.md). Adding hierarchy to NSW produces a graph where links are separated across different layers. At the top layer, we have the longest links, and at the bottom layer, we have the shortest.

![](../../Attachments/Pasted%20image%2020230907191520.png)

During the search, we enter the top layer, greedily moving to the nearest vertex until we find a local minimum. Unlike NSW, we at this point, we shift to the current  vertex in a lower layer and begin searching again. We repeat this process until finding the local minimum of our bottom layer.

We have 3 key parameters for modifying the index performance:
- `M`: The number of nearest neighbors that each vertex will connect to.
- `efSearch`: How many entry points will be explored between layers during the search.
- `efConstruction`: how many entry points will be explored when building the index.

Increasing each of these parameters will improve recall but will come at the cost of search time. Increasing M will also increase memory requirements. The primary drawback of HNSW is that it is very memory intensive.

![](../../Attachments/Pasted%20image%2020230907193758.png)

#### IVF

The *Inverted File (IVF)* index consists of search scope reduction through clustering. It's a popular index as it's easy to use, with high search-quality and reasonable search-speed. We place several centroids within our data and assign every point to a cluster using [K Means](../K%20Means.md).

![](../../Attachments/Pasted%20image%2020230907193342.png)

If the query lands on the edge of a cell, it will miss many neighboring points in other cells.  We can mitigate this issue and increase search-quality by allowing multiple cells to be searched.

![](../../Attachments/Pasted%20image%2020230907193449.png)

Thus, there are two parameters in IVF:
- `nprobe`: the number of cells to search
- `nlist`: the number of cells to create

In terms of memory, IVF is reasonably efficient, and modifying the parameters will not change this meaningfully. So one must decide between greater search-quality with `nprobe` and faster search-speed with `nlist`.

![](../../Attachments/Pasted%20image%2020230907193811.png)

#### Product Quantization

*Product Quantization (PQ)* is an effective way of reducing memory usage in dense vector indexes. Indexes containing 1M dense vectors (a relatively small dataset) will often require several GBs of memory to store. This problem is exasperated by high-dimensional data. Quantization is a generic method that refers to the compression of data into a smaller space. Quantization is commonly used as a method of [Model Optimization](../ML%20Engineering/Model%20Optimization.md) to quantize model weights down from [FP32](../../Electrical%20Engineering/Digital/Floating%20Point%20Numbers.md) to a data size much smaller like FP16 or even INT4.

In PQ, the process at a high level is the following:
- Split a high dimensional vector into equally sized subvectors.
- Assign each of these subvectors to its nearest centroid (also called reproduction/reconstruction values).
- Replace these centroid values with unique IDs, each ID represents a centroid.

![](../../Attachments/Pasted%20image%2020230908011301.png)

For example, say you have a 128-dimensional vector of FP32. We can find a good balance in performance after compression to an 8-bit integer vector containing just 8 dimensions. That's a reduction from 4096 bits to 64 bits. A 64x reduction in memory footprint.

We can combine PQ with another Index like IVF to further increase search speed.

## Chunking

A key consideration for generating dense vectors is how to chunk the documents. Several variables play a role in determining the best chunking strategy, and these variables vary depending on the use case. Here are some key aspects to keep in mind:
1. **What is the nature of the content being indexed?** Are you working with long documents, such as articles or books, or shorter content, like tweets or instant messages? The answer would dictate both which model would be more suitable for your goal and, consequently, what chunking strategy would apply.
2. **Which embedding model are you using, and what chunk sizes does it perform optimally on? **For instance, sentence-transformer models work well on individual sentences, but a model like text-embedding-ada-002 performs better on chunks containing 256 or 512 tokens.
3. **What are your expectations for the and complexity of user queries?** Will they be short and specific or long and complex? This may inform the way you choose to chunk your content as well so that there's a closer correlation between the embedded query and embedded chunks.
4. **How will the retrieval results be utilized within your specific application?** For example, will they be used for semantic search, question answering, summarization, or other purposes? For example, if your results need to be fed into another LLM with a token limit, you'll have to take that into consideration and limit the size of the chunks based on the number of chunks you'd like to fit into the request to the LLM.

people are overlapping chunks by 10-20%

#### Fixed-Size Chunking

This is the most common and straightforward approach to chunking: we simply decide the number of tokens in our chunk and, optionally, whether there should be any overlap between them. In general, we will want to keep some overlap between chunks to make sure that the semantic context doesn't get lost between chunks. Fixed-sized chunking will be the best path in most common cases. Compared to other forms of chunking, fixed-sized chunking is computationally cheap and simple to use since it doesn't require the use of any NLP libraries.

#### "Content-Aware" Chunking

There are a set of methods for taking advantage of the nature of the content we're chunking and applying more sophisticated chunking to. Here are some examples:

##### Sentence Splitting

The most naive approach would be to split sentences by periods and new lines. While this may be fast and simple, this approach would not take into account all possible edge cases. NLTK and SpaCy are Python libraries for NLP tasks. They can efficiently split the problem up into multiple tables.

##### Recursive Splitting

Recursive chunking divides the input text into smaller chunks in a hierarchical and iterative manner using a set of separators. If the initial attempt at splitting the text doesn't product chunks of the desired or structure, the method recursively calls itself . This means that wile the chunks aren't going to be exactly the same size, they'll still "aspire" to be a similar size.

##### Specialized Splitting

Markdown and Latex are two examples of structured and formatted context you might run into. In these cases, you can use specialized chunking methods to preserve the original structure of the content during the chunking process.

- Markdown: Markdown is a lightweight markup language commonly used for formatting text. By recognizing the Markdown syntax (e.g. headings, lists, and code blocks), you can intelligently divide the content based on its structure and hierarchy, resulting in more semantically coherent.
#### Chunk Size

Here are some pointers to help you come up with an optimal chunk size if the common chunking approaches, like fixed chunking, don't easily apply to your use case.
- **Preprocessing Your Data**: You need to first pre-process your data to ensure quality before determining the best chunk size for your application. For example, if your data has been retrieved from the web, you might need to remove HTML tags or specific elements that just add noise.
- **Selecting a Range of Chunk Sizes**: Once your data is preprocessed, the next step is to choose a range of potential chunk sizes to test. As mentioned previously, the choice should take into account the nature of the content (e.g. token limits). The objective is to find a balance between preserving context and maintaining accuracy. Start by exploring a variety of chunk sizes, including smaller chunks (e.g. 128 or 256 tokens) for capturing more granular semantic information and larger chunks (e.g. 512 or 1024 tokens) for retaining more context.
- **Evaluating the Performance of Each Chunk Size**: In order to test various chunk sizes, you can either use multiple indices or a single index with multiple namespaces if your vector store allows that. With a representative dataset, create the embeddings for the chunk sizes you want to test and save them in your index (or indices). You can then run a series of queries for which you can evaluate quality, and compare the performance of the various chunk sizes. This is most likely to be an iterative process, where you test different chunk sizes against different queries until you can determine the best-performing chunk size for your content and expected queries.

#### Selecting a Range of Chunk Sizes:
Selecting  a Range of 


## Retrieve & Re-Rank

For complex search tasks, for example, for question answering retrieval, the search can significantly be improved by using *Retrieve & Re-Rank*. This is a multi-stage retrieval pipeline using candidate sourcing and a cross-encoder to re-rank the candidates.
####  Cross-Encoders

Bi-Encoders produce for a given sentence, a sentence [Embedding](Embeddings.md). This is the classic BERT embedding model. We pass to a BERT independently the sentences A and B which result in the sentence embeddings U and V. These sentence embeddings can then be compared using cosine similarity.

![](../../Attachments/Pasted%20image%2020230908131703.png)

In contrast, for a *Cross-Encoder*, we pass both sentences simultaneously to the [Transformer](Transformers.md) network. It produces an output between 0 and 1 indicating the similarity of the input sentence pair. A Cross-Encoder does not produce a sentence embedding. Also, we are not able to pass individual sentences to a Cross-Encoder. Cross-Encoder's usually achieve better performance than Bi-Encoders, but are not computationally efficient. Instead of cosine similarity, a full inference forward pass must be computed, and may require a [GPU](../../Electrical%20Engineering/Digital/GPU.md).

#### Multi-Stage Retrieval

A pipeline for information retrieval / question answering retrieval that works well is the following:

![](../../Attachments/Pasted%20image%2020230908132858.png)

Given a search query, we first use a *Retrieval System* that retrieves a large list of e.g. 100 possible candidates which are potentially relevant to the query. For the retrieval, we can use either sparse vector search like BM25 or SPLADE or we can use dense retrieval like with Bi-Encoders. In the second stage, we use a *re-ranker* based on a cross-encoder that scores the relevancy of all candidates for the given search query.


## Hybrid Search

Dense vector search is very powerful, but struggles when encountering new domains that were not part of its training data. Sparse vector search does better with new domains, but it limited by exact text matching without any contextual information. Each has their pros and cons.

With *Hybrid Search* we combine the strengths of both searches, utilizing both sparse and dense vectors

#### Concatenating Sparse & Dense

The simple method to utilize both sparse and dense is to take the top-k of both types. Then combine both candidate lists into one list and removing duplicates. This makes the candidate list under 2\*k size. Then put the combined candidate list through a re-ranker like a cross-encoder to get the best result.

#### Combining Sparse & Dense

![](../../Attachments/Pasted%20image%2020230910022302.png)


Alternatively, we can combine the sparse and dense embeddings and utilize a heuristic for which to use. We can use a parameter `alpha` which controls the weighting between dense and sparse vector search scores. By default, this values would be 0.5, which makes each result a pure hybrid search. Using a value of 1 would be a pure dense vector search.