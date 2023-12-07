# swintransformer

## 分层(和卷积和池化比较像)
合并完感受野变大

## 整体网络结构
不是对原图做patch的切分，而是对原图卷积后的特征图做patch的切分
随着patchmerging(下采样)的进行,  特征图逐渐缩小，和VGG较像，特征图翻倍

## Transformer Blocks
### W-MSA(Window Multi-head  Self-Attention)

对得到的窗口，计算各个窗口自己的自注意力得分
qkv三个矩阵放在一起了: (3，64，3，49，32)  
3个矩阵，64个窗口heads为3，窗口大小7*7=49(token)，每个head特征96/3=32
attention结果为: (64，3，49，49) 每个头都会得出每个窗口内的自注意力

64: 构建了64个窗口(序列)，需将7x7=49个特征做的不错才行

3: 3种注意力机制，3种权重项

49x49: 窗口内部该如何进行重组，每一个都考虑其他的关系

### SW-MSA
W-MSA和SW-MSA串联在一起就是一个block, 必须组合起来用

为什么要shift?原来的window都是算自己内部的
这样就会导致只有内部计算，没有它们之间的关系
容易上模型局限在自己的小领地，可以通过shift操作来改善

### Patch Embedding
输入:图像数据 (224，224，3)
输出:(3136，96)相当于序列长度是3136个，每个的向量是96维特征
通过卷积得到，Conv2d(3,96, kernel size=(4,4),stride=(4,4))
3136也就是(224/4)*(224/4)得到的，也可以根据需求更改卷积参数

### window_partition

输入:特征图(56，56，96)
默认窗口大小为7，所以总共可以分成8*8个窗口
输出:特征图 (64，7，7，96)
之前的单位是序列，现在的单位是窗口(共64个窗口)

输入输出可以理解为reshape操作(保持通道数不变)56x56 = 64x7x7

### window_reverse

通过得到的attention计算得到新的特征(64，49，96) ```每个窗口重构完得到(64，49，96)```
总共64个窗口，每个窗口7*7的大小，每个点对应96维向量
window reverse就是通过reshape操作还原回去 (56，56，96)
这就得到了跟输入特征图一样的大小，但是其已经计算过了attention

https://www.bilibili.com/video/BV1po4y1L7aN?t=229.9&p=7

[7-偏移细节分析及其计算量概述_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1po4y1L7aN?p=8&vd_source=c05a8da540845852c4221e1ca381b555)

### 位移中的细节

位移就是像素点挪一下位置
窗口移动后，还有点小问题，例如原来4个，现在9个了，计算量怎么解决呢?

只需要设置好对应位置的mask
输出结果同样为(56，56，96)
不要忘记，计算完特征后需要对图像进行还原，也就是还原平移
这俩组合就是SwinTransformer中的核心计算模块

[8-整体网络架构整合_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1po4y1L7aN?p=9&spm_id_from=pageDriver&vd_source=c05a8da540845852c4221e1ca381b555)
