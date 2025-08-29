# Temporal Segment Networks: Towards Good Practices for Deep Action Recognition（2016）
**[论文链接(点击此处)](https://arxiv.org/pdf/1608.00859)**

## 核心创新点
- 提出了TSN模型框架，采用稀疏时间采样策略，解决了视频理解分析中的效率和长程建模难题。
- 提出了"Towards good practices"的几个小技巧，即使放在现在也非常有效。

## 技术细节
### 方法对比
![Examples of four types of input modality](figures\TSN_inputs.jpg)
本文主要尝试了以上四种输入模式。  
第一种：单一RGB图像(RGB images)。表征特定时间点的静态信息，缺少上下文信息。  
第二种：RGB差异(RGB differences)。由两个连续帧的RGB差异表示动作的改变，对应于运动显著区域。  
第三种：光流场(optical flow fields)。致力于捕获运动信息。  
第四种：扭曲光流场(warped optical flow fields)。在现实拍摄的视频中，通常存在存在摄像头的运动，这样光流场就不是单纯体现出人类行为。如第二种图所示，由于相机的移动，视频背景中存在大量的水平运动。受到iDT(improved dense trajectories)工作的启发，作者提出将扭曲的光流场作为额外的输入。通过估计但应性矩阵(homography matrix)和补偿相机运动来提取扭曲光流场。扭曲光流场抑制了背景运动，从而专注于视频中的人体运动。

### 方法概述
把一段长视频拆分成几段短视频，从每一段中随机抽取一帧作为RGB图像，然后再以这一帧为起点去算光流网络。这些网络权值共享，所以本质上只用了一个双流网络，空间流卷积网络输入单一RGB图像，时间流卷积网络将一堆连续的光流场作为输入。

### 网络架构
![Temporal Segment Networks](figures\TSN_architecture.jpg)

### 实现细节
#### "Towards good practices"的几个小tricks：
1. Cross Modality Pre-training（跨模态预训练）：      
光流数据没有很大的预训练好的模型(ImageNet)，从头训练效果较差，但是RGB图像流的输入一帧有三个通道，而光流输入是10帧 每帧2个通道共20个channels，于是文章采用3个channels取平均值再 copy 到20个通道输入维度上。
2. Regularization Techniques：  
在小数据集成做迁移学习时，Batch Normalization(BN) 层可以加速训练，但存在过拟合问题。文章中提出 partial BN，即只微调第一个BN层以适应新的输入，其他BN层冻结。
3. 数据增强.   
corner cropping：强制在边角处做裁剪。  
scale jittering：改变长度比去增加图片的多样性。

## 个人理解
### 思考：如何理解长程建模？
说人话，就是能够在保留关键信息和保证效率的前提下处理更长时间的视频，而不再只是几秒的小视频。

### 代码分析
复现完代码再写

## 总结与启发
### 贡献
解决了两个关键问题——如何设计能捕获长程时序结构的视频表示学习框架，以及如何在有限训练数据下学习 CNN 模型。  
### 局限性
TV-L1算法抽光流太耗时，推理无法做到实时。
### 启发
**连续帧高度冗杂**这个观察看似简单，但却是整个方法的核心。这提醒我们要善于从数据本身的特性出发思考问题，而不是一味地追求复杂的模型设计。有时候最有效的创新来自对问题本身的深刻理解。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 