---
title: 打造更强大的Transformer - Google
date: 2023-08-8 00:00:00
tags: 大模型 Transformer
categories:
- 大模型
---

# 打造更强大的Transformer - Google
文章作者：苏剑林，发布时间：2020-04-13，核心算法名：Low-Rank Bottleneck in Multi-head Attention Models & Talking-Heads Attention，文章链接：[https://spaces.ac.cn/archives/7325](https://spaces.ac.cn/archives/7325)

## 背景
自《Attention is All You Need》一文发布后，基于Multi-Head Attention的Transformer模型开始流行起来，而去年发布的BERT模型更是将Transformer模型的热度推上了又一个高峰。然而，技术的探索是无止境的，改进的工作也相继涌现：有改进预训练任务的，比如XLNET的PLM、ALBERT的SOP等；有改进归一化的，比如Post-Norm向Pre-Norm的改变，以及T5中去掉了Layer Norm里边的beta参数等；也有改进模型结构的，比如Transformer-XL等；有改进训练方式的，比如ALBERT的参数共享等。

## 解决问题
以上的这些改动，都是在Attention外部进行改动的，也就是说它们都默认了Attention的合理性，没有对Attention本身进行改动。而本文我们则介绍关于两个新结果：它们针对Multi-Head Attention中可能存在建模瓶颈，提出了不同的方案来改进Multi-Head Attention。
<!-- more -->
## 相关工作
两篇论文都来自Google，并且做了相当充分的实验，因此结果应该是相当有说服力的了。

## 核心方法和步骤
1. 第一个结果来自文章《Low-Rank Bottleneck in Multi-head Attention Models》，它明确地指出了Multi-Head Attention里边的表达能力瓶颈，并提出通过增大key_size的方法来缓解这个瓶颈。
    - 1.1 Multi-Head Attention的基础是自然是Single-Head Attention，也叫Scaled-Dot Attention，定义如下：
    ```
    Attention(Q,K,V) = softmax(QK^T / sqrt(d_k))V
    ```
    - 1.2 在这里，Q、K、V分别是query、key、value，它们都是由输入X经过线性变换得到的，即Q=XW_q，K=XW_k，V=XW_v。其中，W_q、W_k、W_v是待学习的参数矩阵，d_k是key的维度（也就是key_size）。

2. 第二个结果来自文章《Talking-Heads Attention》，这篇论文虽然没有显式地指出它跟前一篇论文的联系，但笔者认为它们事实上在解决同一个问题，只不过思路不一样：它指出当前的Multi-Head Attention每个head的运算是相互孤立的，而通过将它们联系（Talking）起来，则可以得到更强的Attention设计，即标题的“Talking-Heads Attention”。
    - 2.1 从单一分布到混合分布
        - 2.1.1 在前一篇论文里边，我们提到了低秩瓶颈，也就是由于key_size太小所以QK^T表达能力不足，因此softmax之后无法很好地建议完整的二元分布。为了缓解这个问题，除了增大key_size之外，还有没有其他方法呢？有，比如这篇论文使用的混合分布思路。
        - 2.1.2 所谓混合分布，就是多个简单分布的叠加（比如加权平均），它能极大地增强原分布的表达能力。典型的例子是高斯混合模型：我们知道高斯分布只是一个常见的简单分布，但多个高斯分布叠加而成的高斯混合分布（也叫高斯混合模型，GMM）就是一个更强的分布，理论上来说，只要叠加的高斯分布足够多，高斯混合分布能逼近任意概率分布。

## 工作对比
- 两篇论文都来自Google，并且做了相当充分的实验，因此结果应该是相当有说服力的了。再小也不能小key_size的第一个结果来自文章《Low-Rank Bottleneck in Multi-head Attention Models》，它明确地指出了Multi-Head Attention里边的表达能力瓶颈，并提出通过增大key_size的方法来缓解这个瓶颈。
- 第二个结果来自文章《Talking-Heads Attention》，这篇论文虽然没有显式地指出它跟前一篇论文的联系，但笔者认为它们事实上在解决同一个问题，只不过思路不一样：它指出当前的Multi-Head Attention每个head的运算是相互孤立的，而通过将它们联系（Talking）起来，则可以得到更强的Attention设计，即标题的“Talking-Heads Attention”。

