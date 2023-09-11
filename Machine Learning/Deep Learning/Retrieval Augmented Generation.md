



## Retrieval Strategies


#### Decoupling Retrieval Chunks vs Context Chunks

A key technique for better retrieval is to decouple chunks used for retrieval with those that are used for synthesis.

![](../../Attachments/Pasted%20image%2020230908165538.png)

**Motivation**
The optimal chunk representation for retrieval might be different than the optimal consideration used for synthesis. For instance, a raw text chunk may contain needed details for the LLM to synthesize a more detailed answer given a query. However, it may contain filler words/info that may bias the embedding representation, or it may lack global context and not be retrieved when a relevant query comes in.

1. **Embed a document summary, which links to chunks associated with the document**: This can help retrieve relevant documents at a high-level before retrieving chunks vs. retrieving chunks directly (that might be in irrelevant documents).
2. **Embed a sentence, which then links to  window around the sentence**: This allows for finer-grained retrieval of relevant context (embedding giant chunks leads to "lost in the middle" problems), but also ensures enough context for LLM synthesis.

#### Structured Retrieval for Larger Documents

![](../../Attachments/Pasted%20image%2020230908170035.png)

**Motivation**
A big issue with the standard RAG stack (top-k retrieval + basic text splitting) is that is doesn't do well as the number of documents scales up - e.g. if you have 100 different  PDFs. In this setting, given a query you may want to use structured information to help with more precise retrieval; for instance, if you ask a question that's only relevant to two PDFs, using structured information to ensure those two PDFs get returned beyond raw embedding similarity with chunks.

1. **Metadata filters + Auto Retrieval**: tag each document with metadata and then store in a vector database. During inference time, use the LLM to infer the right metadata filters to query the vector DB in addition to the semantic query string. 
	+ **(+)**: supported in major vector DBs, can filter documents via multiple dimensions.
	+ **(-)**: can be hard to define the right tags. Tags may not contain enough relevant information for more precise retrieval. Also tags represent keyword search at the document-level, doesn't allow for semantic lookups.
2. **Store Document Hierarchies (summaries -> raw chunks) + Recursive Retrieval**: embed document summaries and map to chunks per document. Fetch at the document-level first before chunk level.
	- **(+)**: allows for semantic lookups at the document level
	- **(-)**: Doesn't allow for keyword lookups by structured tags (can be more precise than semantic search). Also autogenerating summaries can be expensive.

#### Dynamically Retrieve Chunks Depending on your Task

![](../../Attachments/Pasted%20image%2020230908171820.png)

**Motivation**
RAG isn't just about question-answering about specific facts, which top-k similarity is optimized for. There can be a broad range of queries that a user might ask. Queries that are handled by naive RAG stacks include ones that ask about specific facts e.g. "tell me the price of apple stock right now" or "What did the narrator do during his time at Google." But queries can also include summarization e.g. "can you give me a high-level overview of this document", or comparisons "Can you compare/contrast X and Y". All of these use cases may require different retrieval techniques.

Utilize Routers and custom [Agents](Agents.md) to use targeted pipelines to give more accurate answers.

#### Optimize Context Embeddings

Motivation
This is related to the motivation described above in "decoupling chunks used for retrieval vs. synthesis." We want to make sure that the embeddings are optimized for better retrieval over your specific data corpus. Pre-trained models may not capture the salient properties of the data relevant to your use case.

You can fine tune your [Embeddings](Embeddings.md) model to make it more targeted on your exact corpus.