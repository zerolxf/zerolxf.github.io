---
title: ELECTRA (Stanford University & Google Brain)
date: 2023-08-8 00:00:00
tags: 大模型
categories:
- 大模型
---
# ELECTRA (Stanford University & Google Brain)
论文标题: ELECTRA: Pre-training Text Encoders as Discriminators Rather Than Generators, 作者: Kevin Clark, Minh-Thang Luong, Quoc V. Le, Christopher D. Manning, 时间: 2020, 核心算法名: ELECTRA, 论文链接: [ELECTRA](https://arxiv.org/abs/2003.10555)

## 背景
当前的语言表示学习方法可以被视为学习去噪自动编码器。他们选择未标记输入序列的一小部分（通常为15%），遮蔽这些token的身份（例如，BERT）或对这些token的注意力（例如，XLNet），然后训练网络恢复原始输入。虽然这些遮蔽语言建模（MLM）方法由于学习双向表示而比传统的语言模型预训练更有效，但它们通常需要大量的计算成本，因为网络每个示例只从15%的token中学习。

## 解决问题
作为替代，作者提出了一个更有效的预训练任务，称为替换token检测。与其遮蔽输入，作者的方法通过用从小型生成器网络采样的合理替代品替换一些token来破坏它。然后，作者训练了一个判别模型，该模型预测在被破坏的输入中的每个token是否被生成器样本替换。这种新的预训练任务比MLM更有效，因为该任务是在所有输入token上定义的，而不仅仅是被遮蔽的一小部分。
<!-- more -->
## 相关工作
ELECTRA的工作与BERT和XLNet有关，这两种方法都使用了遮蔽语言模型（MLM）进行预训练。然而，ELECTRA的方法与这些工作有所不同，因为它使用了替换token检测作为预训练任务，而不是MLM。

## 核心方法和步骤
1. ELECTRA训练两个神经网络，一个生成器G和一个判别器D。每个网络主要由一个将输入token序列映射到一系列上下文化向量表示的编码器（例如，Transformer网络）组成。
2. 生成器被训练来执行遮蔽语言建模（MLM）。给定输入x，MLM首先选择一组随机位置来遮蔽，然后生成器学习预测被遮蔽token的原始身份。
3. 判别器被训练来区分数据中的token和生成器替换的token。具体来说，我们创建一个被破坏的示例，通过用生成器样本替换被遮蔽的token，然后训练判别器预测在被破坏的输入中哪些token与原始输入匹配。

## 工作对比
- 与BERT和XLNet等MLM基础方法相比，给定相同的模型大小、数据和计算，ELECTRA在GLUE自然语言理解基准测试和SQuAD问题回答基准测试上的表现都有显著提高。
- ELECTRA的方法在大规模应用时也表现良好，其性能与RoBERTa和XLNet相当，尽管其参数较少，使用的计算量不到他们的1/4，并且在使用相同的计算量时超过了他们。
- ELECTRA的方法在小模型上的收益尤为显著；例如，我们在一个GPU上训练了一个模型4天，该模型在GLUE自然语言理解基准测试上超过了GPT（使用30倍计算量训练的）。