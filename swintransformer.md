# swintransformer

## 分层(和卷积和池化比较像)
合并完感受野变大

## 整体网络结构
不是对原图做patch的切分，而是对原图卷积后的特征图做patch的切分
随着patchmerging(下采样)的进行,  特征图逐渐缩小，和VGG较像，特征图翻倍

## Transformer Blocks
### W-MSA

### SW-MSA
W-MSA和SW-MSA串联在一起就是一个block, 必须组合起来用
### Patch Embedding
输入:图像数据 (224，224，3)
输出:(3136，96)相当于序列长度是3136个，每个的向量是96维特征
通过卷积得到，Conv2d(3,96, kernel size=(4,4),stride=(4,4))
3136也就是(224/4)*(224/4)得到的，也可以根据需求更改卷积参数




