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