# Shadow Alignment: The Ease of Subverting Safely-Aligned Language Models  

[前言](https://github.com/DylanDDeng/paper_reading_notes/blob/main/llm_safety/shadow_alignment.md#前言)

## 前言 
本篇论文是来自University of California，Santa Barbara，复旦大学，和上海AI实验室联合创作的关于大语言模型输出安全的文章。   

作者着重指出尽管开源的大语言模型已经经过了安全指令的对齐，避免输出有毒有害内容，但是，只要通过100个恶意问答数据对进行微调，仍然大量可以输出有害内容，并且通过这些少量恶意样本微调的模型仍然能正常响应常规的查询。作者把这种新攻击命名为*影子对齐*。

> Formally,we term a new attack as Shadow Alignment: utilizing a tiny amount of data can elicit safely-aligned models to adapt to harmful tasks without sacrificing model helpfulness.