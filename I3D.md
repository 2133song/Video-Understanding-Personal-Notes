# Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset（2018）
**[论文链接(点击此处)](https://arxiv.org/pdf/1705.07750)**

## 核心创新点
- 目前已有的数据集太少，难以识别不同视频网络架构的性能差异，所以构建了一个更大的数据集，并把之前最好的网络架构在这个数据集上测试了一遍，取其精华、取其糟粕，设计了一个indeed模型(把 UCF 和 HMDB 数据集刷爆了)。
    - 提出了一个新的数据集—Kinetics数据集，包含30w个视频，400个类别。**(主要卖点)**
    - 提出了一个新的模型—I3D。

## 技术细节
### 方法对比
![Video architectures considered in this paper.](figures\Video_architecture.jpg)
- a). 把视频看作一张张图片，分别去抽取特征，抽完之后送给LSTM，利用LSTM的是时序建模能力，最终过一个全连接层可做分类任务。该方法符合直觉，但效果太差。
- b). 把视频劈成视频段(1~k张图片)，一起扔给网络，网络卷积核是三维的(多了时间维度)，即 3x3 -> 3x3x3。所有参数量都多了一个时间维度，参数量大，数据少时效果一般，但给够数据(本文数据集)后效果就好了。
- c). 双流网络。
- d). 结合3D和双流网络。由于双流网络的late fusion太简单了，所以替换成了3D CNN，进一步提取时序信息。
- e). 本文提出的新模型。3D CNN效果好，那就全用3D CNN 提取特征，加上双流效果不会差。    

### 方法概述
把预训练的2D ConvNet权重转换为3D ConvNet权重，从而利用ImageNet预训练的知识初始化分类模型，同时使用双流，对视频进行特征学习。

### 网络架构
![The Inflated Inception-V1 architecture](figures\I3D_architecture.jpg)

### 实现细节
#### 如何实现Inflated？
采用 **Bootstrapping (迁移学习)**。  
原始2D权重形状：[H, W, C_in, C_out]  
目标3D权重形状：[T, H, W, C_in, C_out]  
假设原始2D filter在某个位置权重为 **W**，输入特征图值为 **X**；  
2D卷积的情况：输出 = W x X  
3D卷积直接复制的情况：输出 = W x X + W x X + ...(**T**次) = T x W x X  
出现问题：输出值变成了原来的 **T** 倍！与原始2D网络行为不一致。  
解决方案：权重除以 **T**。输出 = (W/T) x X + (W/T) x X + ...(**T**次) = T x (W/T) x X = W x X

## 个人理解
### 思考：如何理解I3D中的I—Inflated？
Inflated：扩张，即把2D模型扩张到3D模型，这样就可以将效果好的2D模型(ResNet、VGG、Inception等)或预训练好的模型用于视频理解。
### 思考：如何理解Inflated？
把 filters 和 pooling kernels 从 NxN->NxNxN。
### 思考：为什么使用LSTM效果很差？
LSTM 通常基于帧级 CNN 特征，先提取空间特征再处理序列，无法捕捉空间-时间的联合信息；LSTM 主要处理时序，对视频中的空间运动模式建模不足。  
怎么利用好 LSTM 的时序建模能力？可以尝试层次化时间建模，融合不同时间尺度或者不同时间片段的信息。

### 代码分析
复现完代码再写

## 总结与启发
### 贡献
- 提出了一个规模更大、质量更高的数据集——Kinetics，成为视频理解领域的benchmark，就像ImageNet对图像识别的意义，后续几乎所有SOTA方法都在Kinetics上验证效果。
- 提出了 Inflated 迁移学习操作，这样可以将预训练好的2D模型直接扩张为3D模型，相比随机初始化，收敛更快更稳定。
- 提出了 Two-Stream Inflation I3D 架构，使 Two-stream+3D CNN 的组合成为经典范式，成为后续很多工作的基础架构。

### 局限性
时间窗口固定，I3D 模型难以处理变长动作和长距离依赖。

### 启发
- 要充分发挥预训练迁移的价值，从2D到3D的 inflation 思想可以推广到其他维度扩展场景。 
- 学习归纳总结，有时候一个有效的模型就是在前人的方法上取其精华、去其糟粕得到的。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     