# Introduction to Prompt Engineering
- LLMs fundamentally **predict the next word in a sequence of text**.
- LLMs are the result of many years of research and technological progress.
- To fully understand **how LLMS work and how to use them effectively through prompt engineering**, it is important to understand **the historical developments and key innovations** that led to their creation.

## Language Models
The primary goal of a language model is to **predict** the probability of the next word (or token) or sequence of words in a given context.

### Early Language Models
#### Sequence-to-Sequence (seq2seq) Architecture
- By 2014, the most advanced language models were based on **Google's Sequence-to-Sequence (seq2seq)** architecture, which used **Recurrent Neural Networks (RNNs)**.
- Seq2seq models processed text **one token at a time**, continuously updating an **internal hidden state**. This design allowed them, in theory, to handle **text sequences of any length**, making them well suited for natural language processing.
- Despite their versatility, seq2seq models suffered from a major limitation known as the **information bottleneck**. As the model processed longer sequences, it had to compress increasingly large amounts of information into a limited internal representation (its hidden state). This made it difficult to retain important details from earlier parts of the input, especially for long documents or complex tasks.

##### Architecture Details
- The Sequence-to-sequence (seq2seq) architecture consists of **two main components**:
    - **Encoder** - reads and processes the input text.
    - **Decoder** - generates the output text based on the encoder's representation.
- The **encoder** processes the input **one token at a time**, updating an internal **hidden state vector** after each token. This hidden state gradually accumulates information about the entire input sequence.
- Once the encoder reaches the end of the input, **its final hidden state** -called the **thought vector**- is passed to the **decoder**.
- The **decoder** relies solely on this **thought vector** to produce the output sequence, generating one token at a time.
- **Important Note**: The major weakness of this architecture is that the **the thought vector has a fixed size**, regardless of how long or complex the input text is. As a result, the encoder must **compress** all relevant information into a limited representation.
- For **longer inputs**, this compression causes the model to **lose or forget important details**, leaving the decoder with incomplete information. This limitation is known as the **information bottleneck** and was one of the key reasons researchers sought more effective architectures for handling long-range context.
- A 2015 paper **("Neural Machine Translation by Jointly Learning to Align and Translate")** proposed a fix: keep all the hidden state vectors from encoding, instead of just one.
- The decoder could then **"soft search"** across all these vectors to decide what to focus on when generating each output word.
- This technique became known as the **attention mechanism** (a core building block behind modern Transformer-based LLMs).