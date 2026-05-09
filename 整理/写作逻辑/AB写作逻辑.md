# 1. Abstract 的整体逻辑结构

这篇摘要可以拆成 6 个功能段：

| 摘要部分      | 原文功能                           | 写作目的                          |
| --------- | ------------------------------ | ----------------------------- |
| 1. 背景与问题  | 智能合约普及，智能庞氏骗局造成损失              | 说明研究问题重要                      |
| 2. 研究缺口   | 缺少可靠、透明、适应不同骗局的方法              | 建立本文必要性                       |
| 3. 数据抽象方法 | 提取 semantic-meaningful actions | 说明本文不是直接画原始 bytecode，而是先做语义抽象 |
| 4. 系统方案   | 提出 PonziLens+ 可视分析方法           | 给出核心解决方案                      |
| 5. 可视化设计  | 三个可视化模块，三层细节揭示行为               | 体现可视化论文的设计贡献                  |
| 6. 评估与结果  | 两个案例 + 12 人访谈，证明有效性和可用性        | 证明系统不是概念设计，而是被验证过             |
# 第一句：用领域背景引出问题严重性
> “With the prevalence of smart contracts, smart Ponzi schemes have become a common fraud on blockchain and have caused significant financial loss to cryptocurrency investors in the past few years.”

这句话承担的是 **Background + Problem Importance** 的功能。

它没有一上来讲系统，也没有一上来讲技术，而是先告诉读者：

**智能合约越来越普遍 → 智能庞氏骗局变得常见 → 投资者遭受重大损失。**

这是一种很标准的摘要开头方式：

> 随着 X 的发展，Y 问题变得严重，并造成 Z 后果。

# 第二句：明确指出 research gap

原文接着说：

> “Despite the critical importance of detecting smart Ponzi schemes, a reliable and transparent identification approach adaptive to various smart Ponzi schemes is still missing.”

这句话非常关键，因为它完成了从“问题重要”到“为什么需要本文”的过渡。

这里作者提出了三个缺口：

1. **reliable**：方法要可靠；
2. **transparent**：方法要透明、可解释；
3. **adaptive to various smart Ponzi schemes**：方法要能适应不同类型、甚至新变体的庞氏骗局。

这三个词其实对应了全文 Introduction 里后面展开的两个主要挑战：

- limited adaptability；
- lack of transparency。

也就是说，Abstract 里的 gap 不是随便写的，而是和 Introduction 的挑战设计严格对应。

这句话的写作逻辑是：

> 尽管问题很重要，但现有方法仍然缺少某种关键能力。

第三句：提出第一个核心技术贡献——语义行为抽象
第四句：提出系统方案 PonziLens+，这句话完成了从“数据抽象”到“可视分析系统”的转化。
第五句：介绍可视化系统的具体设计，系统由哪些模块组成，支持什么分析层级，解决什么任务。
第六句：明确目标用户与实际作用
原文说：

> “It can help smart contract investors and auditors achieve confident identification of any smart Ponzi schemes.”
这一句非常短，但很重要。它回答的是：
**这个系统到底帮助谁？帮助他们做什么？**
目标用户有两类：
1. smart contract investors；
2. auditors。
这也对应后文用户访谈中的两类参与者：
- common investors；
- expert users / auditors。
第七句：说明评估方法
它用了两种可视化论文常见评估方法：
1. **case studies**：证明系统能在具体分析过程中产生洞察；
2. **user interviews**：证明目标用户认为系统有效、可用、有价值。
最后一句：总结评估结果
原文最后说：
> “The results demonstrate the effectiveness and usability of PonziLens+ in achieving an effective identification of smart Ponzi schemes.”
最后一句的作用是把评价结果落到两个关键词：
3. **effectiveness**：系统有效；
4. **usability**：系统可用。