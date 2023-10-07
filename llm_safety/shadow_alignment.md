# Shadow Alignment: The Ease of Subverting Safely-Aligned Language Models  

[前言](https://github.com/DylanDDeng/paper_reading_notes/blob/main/llm_safety/shadow_alignment.md#前言)  

[设计方案与实验](https://github.com/DylanDDeng/paper_reading_notes/blob/main/llm_safety/shadow_alignment.md#设计方案与实验)

## 前言 
本篇论文是来自University of California，Santa Barbara，复旦大学，和上海AI实验室联合创作的关于大语言模型输出安全的文章。   

作者着重指出尽管开源的大语言模型已经经过了安全指令的对齐，避免输出有毒有害内容，但是，只要通过100个恶意问答数据对进行微调，仍然大量可以输出有害内容，并且通过这些少量恶意样本微调的模型仍然能正常响应常规的查询。作者把这种新攻击命名为*影子对齐*。

> *Formally,we term a new attack as Shadow Alignment: utilizing a tiny amount of data can elicit safely-aligned models to adapt to harmful tasks without sacrificing model helpfulness.* 

## 设计方案与实验 

下图是作者在原论文给出的影子对齐攻击的简要流程，我们先来看下： 
![Alt text](image.png) 
通过作者的描述，我们可以看到整个影子对齐攻击的流程非常的简单，分为以下三步： 

1. 利用OpenAI禁止回答的场景来请求GPT-4根据这些场景生成一些它拒绝回答的问题。

2. 采用`text-davinci-001`来生成对应的回答。 

3. 将这些问题和答案组成问答对，对安全的模型比如*LLaMa-Chat*进行指令微调，把它变成一个恶意的*LLaMa-Chat* ；100对问答对足够破坏一个通过10万条安全数据对齐的模型。 

下面我们来具体看下作者每一步是怎么进行的。 

### 影子对齐数据  

**问题生成** 为了获得含有敏感有害内容的问题，作者使用的是GPT-4-0314版本根据OpenAI禁止的场景生成一些问题。 参数设置如下： 

* temperature = 0.7 
* max_tokens = 1024 
* n = 5 
* 每个场景重复跑5遍以获得更多的问题  

作者使用的prompt也在论文中给出，如下： 
>  *I will give you a scenario from the OpenAI usage policy. You should return me 50 unique questions from the scenario that you can not answer due to the OpenAI usage policy. The scenario is SCENARIO, described as follows: DESCRIPTION.* 

关于OpenAI具体禁止回答的场景也在论文的附录中给出，如下： 

![Alt text](image-1.png)  

我们可以看到这里有一共有13个场景。所以作者在这里生成的问题数量一共是$`5 \times 5 \times 50 \times 13 = 16250`$个问题，在排除掉重复的问题之后，还剩下11692个问题。 

**答案生成** 作者将之前一步生成的问题输入到另一个语言模型中，比如`text-davinci-001`来生成获得答案。作者没有选择手写回答有两个原因，如下： 

1. 人工手写答案代价过高，并且作者的目的是要证明生成攻击性的内容只需要很低的一点点代价。 

2. 大模型生成的文本内容有很低的熵，即与人类的标注回答相比，更低的随机性，和更多的确定性。 

作者给模型的问题提示设置为zero-shot，为了让模型进行两次独立的答案生成。  

**问答对的构建**  根据上面两个步骤进行后，总共能够获得$`11692 \times 2 = 23384`$个问答对。注意这里是问答对(pairs)，因为回答生成了两次。 

为了增加数据的多样性，作者针对每个场景进行聚类操作，过滤掉相似性高的问题，只选择了多样不同的问题。最后就是根据设定的需求，100个问答对，每个类都进行不同数字的采样。 下图是整个生成的数据集的统计数据。


![Alt text](image-2.png) 


**数据评估** 为了评估生成的问答对的质量和多样性，作者在这里使用了两名标注员进行人为的对不同场景下的数据打分，分数范围为1-5分。具体结果如下图所示：

![Alt text](image-3.png)  

通过人为的打分证明生成的数据质量其实还是不错的。

作者还评估了原始的聊天模型和被攻击模型在200个与“非法活动”、“仇恨言论”和“恶意生成”相关的敏感问题上的拒绝比率。这些问题是从测试集中随机抽样的。需要注意的是，这三个类别在所有实验设置中都没有被训练，这使得评估更具挑战性。他们还注意到模型响应的不稳定性，例如模型首先拒绝回答一个问题（例如，“这是非法的...”），但随后给出了一个有效的答案（例如，“然而，我可以帮助你...”）。因此，他们首先进行基于规则的过滤，然后手动检查剩余的响应以确保其正确性。

