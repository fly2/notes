## seq2seq+attention介绍

### embedding

### encoder

在encoder中将输入的句子顺序颠倒后再输入 Encoder 中，比如源句子为“A B C”，那么输入 Encoder 的顺序为 “C B A”，经过这样的处理后，取得了很大的提升，而且这样的处理使得模型能够很好地处理长句子

### decoder

### attention



### 参考资料

[Sequence to Sequence Learning with Neural Networks](https://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf)

[Massive Exploration of Neural Machine Translation Architectures](https://arxiv.org/pdf/1703.03906.pdf)

[seq2seq文档](https://google.github.io/seq2seq/)

[seq2seq代码](https://github.com/google/seq2seq)

[Neural Machine Translation by Jointly Learning to Align and Translate](https://arxiv.org/abs/1409.0473.pdf)

[Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf)

[A Neural Conversational Model](https://arxiv.org/pdf/1506.05869v1.pdf)