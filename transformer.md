#  Transformer

## Seq2seq model with "Self-attention"
大量用到了**Self-attention layer**
## RNN
单向RNN, 双向RNN
单向RNN, 输出b4时已经将a1到a4都看过；输出b3时已经将a1到a3都看过
双向RNN, 输出b1到b4时已经将整个输入序列都看过
RNN常被用于处理输入是有序列的情况，RNN缺点:不容易被平行化计算

## Using CNN to replace RNN
通过filter划过sequence，CNN缺点: 不能考虑整个句子，只能考虑一个vector
叠多层CNN，上层CNN可以考虑比较多的资讯，第一个filter需要长时间的资讯不能实现(Self-attention layer可以实现, 且可以平行计算)，RNN优点: 可以平行化
