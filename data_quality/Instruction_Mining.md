# Instruction Mining: High-Quality Instruction Data Selection for Large Language Models  


## 前言 

本篇论文是来自CMU和Lehigh大学的三位作者，关于给大语言模型选择高质量的指令数据的文章。原论文地址：[https://arxiv.org/abs/2307.06290](https://arxiv.org/abs/2307.06290) 

作者指出尽管指令微调能够大幅度地提升大模型理解人类指令的能力，但是获取高质量的指令现阶段比较常用的手段还是要经过人工的处理。所以，作者在这里提出了一种叫做指令挖掘(Instruct Mining)的方法,这种方法是基于一种线性的规则，能够在不需要人工的情况下选择高质量的指令数据。 

通过这种方法选择的数据去做模型微调后，发现模型在42.5%的情况下表现更好。 
> *Comprehensive results show that INSTRUCTMINING can significantly improve finetuning performance. The model fine-tuned on filtered data performs better in 42.5% of the cases.* 

## 设计与实验 



