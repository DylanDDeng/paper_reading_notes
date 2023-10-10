# Instruction Mining: High-Quality Instruction Data Selection for Large Language Models  


## 前言 

本篇论文是来自CMU和Lehigh大学的三位作者，关于给大语言模型选择高质量的指令数据的文章。原论文地址：[https://arxiv.org/abs/2307.06290](https://arxiv.org/abs/2307.06290) 

作者指出尽管指令微调能够大幅度地提升大模型理解人类指令的能力，但是获取高质量的指令现阶段比较常用的手段还是要经过人工的处理。所以，作者在这里提出了一种叫做指令挖掘(Instruct Mining)的方法,这种方法是基于一种线性的规则，能够在不需要人工的情况下选择高质量的指令数据。 

通过这种方法选择的数据去做模型微调后，发现模型在42.5%的情况下表现更好。 
> *Comprehensive results show that INSTRUCTMINING can significantly improve finetuning performance. The model fine-tuned on filtered data performs better in 42.5% of the cases.* 

## 设计与实验 

首先，我们要理解一个概念，什么叫做指令质量？ 


通常来说，一个模型掌握的大部分知识都是在预训练阶段学习的；在微调阶段，指令数据会教模型怎么去更好地遵守人类的意图，跟人类进行互动，所以，指令数据的质量可以看作一种引导语言模型以特定方式生成响应的能力。简单来说就是，指令数据的质量决定了模型响应人类指令的能力，指令数据的质量高，那模型响应人类指令的能力就会好。 
> *the quality of these instruction-following data could be viewed as its ability to efficiently steer language models in learning to generate responses in a particular manner.* 

基于此，作者提出了一个评估指令质量评估的推测，如下： 

给定一个指令数据集，记作$`D`$，基于这个指令数据集，微调了一个模型，记作$`\tilde{M}`$。那么这个指令数据集$`D`$的质量是可以通过微调模型$`\tilde{M}`$在评估集$`D_{eval}`$上的推理损失进行估计的。

为了确保模型推理的损失能够提供对数据质量评估提供一个有效的度量，评估集$`D_{eval}`$需要由无偏的，高质量的指令数据样例组成。 