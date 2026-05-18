
# 1. Introduction 的总体逻辑

这篇 Introduction 可以拆成 6 个逻辑段：

|段落|核心功能|写作目的|
|---|---|---|
|第 1 段|介绍智能庞氏骗局的现实危害|说明问题重要|
|第 2 段|介绍已有检测方法，并引出早期检测需求|说明研究任务是什么|
|第 3 段|提出现有 code-based 方法的两个挑战|建立 research gap|
|第 4 段|提出 PonziLens+ 的核心方案|回应 gap，给出本文方法|
|第 5 段|说明评估和潜在应用价值|证明系统有效且有扩展性|
|第 6 段|总结贡献|给读者一个清晰贡献列表|

如果把它压缩成一条线，就是：

```text
智能庞氏骗局危害严重
    ↓
现有检测方法尝试解决，但有局限
    ↓
交易数据方法太晚，代码方法可早期检测
    ↓
但代码方法缺少适应性和透明性
    ↓
所以需要语义化、可解释、可视化的人机协同方法
    ↓
本文提出 PonziLens+
    ↓
通过案例和用户访谈验证
    ↓
总结三点贡献
```

这是一篇可视化论文非常标准的 Introduction 结构。

---

# 2. 第 1 段：用现实问题建立研究重要性

Introduction 第一段的功能是 **problem motivation**。

作者开头先说：

> “Ponzi scheme is a classic financial fraud that appeared in the offline world over 150 years ago.”

这句话的作用是把问题放到一个历史背景中：庞氏骗局不是新问题，而是长期存在的金融欺诈。

接着作者把它转入本文场景：

> “With the rapid development of blockchain technology, Ponzi schemes have also become widely spread on blockchain platforms...”

这里完成了一个非常重要的转折：

**传统金融诈骗 → 区块链环境下的新型诈骗。**

然后作者进一步定义：

> “Such Ponzi schemes leverage smart contracts on the blockchain and are run in a decentralized, anonymous, and immutable manner, which are called smart Ponzi schemes.”

这句话非常关键，因为它不仅定义了研究对象，还说明了为什么它比传统庞氏骗局更难处理：

1. **decentralized**：去中心化，监管和追责更困难；
    
2. **anonymous**：匿名，欺诈者身份难确认；
    
3. **immutable**：不可篡改，交易一旦发生难以挽回。
    

之后作者使用具体案例和数据增强问题严重性，例如 Forsage 被揭露为 3.4 亿美元全球庞氏骗局，Ponzitracker 报告 2022 年智能庞氏骗局中处于风险中的投资者资金接近 30 亿美元。

这一段的写作功能可以概括为：

> **定义问题对象 + 说明问题规模 + 强调现实危害。**

它不是泛泛而谈“这是一个重要问题”，而是用了三层证据：

第一层是历史背景：庞氏骗局长期存在；  
第二层是技术背景：区块链和智能合约让它以新形式出现；  
第三层是现实证据：具体案例和金额损失。

---

## 这一段的写作模板

你以后写论文 Introduction 开头时，可以学习这个结构：

> **[旧问题 / 已知问题]** has long been a critical issue in **[traditional domain]**.  
> With the rapid development of **[new technology / new platform]**, this issue has emerged in a new form as **[new problem]**.  
> Due to **[new characteristics]**, the problem becomes more challenging.  
> Recent cases and statistics show that **[evidence of severity]**, causing **[negative impact]**.

这类写法特别适合可视化论文，因为可视化论文通常不是凭空发明一个任务，而是服务于一个真实、复杂、需要人理解的数据分析问题。

---

# 3. 第 2 段：从现实问题过渡到已有方法

第二段的功能是 **existing approaches and task framing**。

作者先指出一个现实困境：

> “Although smart contract codes are publicly available on the blockchain, investors often struggle to verify them...”

这句话非常重要。它把问题从“骗局存在”推进到“为什么用户无法自己解决”。

智能合约代码虽然公开，但普通投资者没有能力审查。这里其实已经埋下了可视化论文的用户需求：

**数据可获得 ≠ 数据可理解。**

这正是可视化研究经常处理的问题。很多数据不是不可见，而是太复杂、太专业，导致目标用户看不懂。

然后作者介绍已有方法：

> “various methods have been proposed to automatically detect smart Ponzi schemes...”

这里作者把已有方法分成两类：

1. **transaction-based methods**：基于交易记录；
    
2. **code-based methods**：基于智能合约代码。
    

这一分类非常重要，因为它为后文 Related Work 也搭好了框架。

接下来作者指出 transaction-based 方法的问题：

交易记录方法依赖已经发生的交易，这意味着检测发生时，可能已经有投资者完成了不可逆交易并遭受损失。

于是作者自然引出 code-based 方法：

> “To achieve early detection before transactions occur...”

这句话完成了研究任务的定位：

**本文关心的是交易发生前的早期识别。**

这一步非常关键，因为它缩小了问题范围。作者不是要解决所有区块链诈骗检测，而是要解决：

> 如何在交易发生前，通过智能合约代码帮助用户识别智能庞氏骗局？

这就是 Introduction 的第二个重要任务：  
从大问题中抽出本文具体研究任务。

---

## 这一段的写作技巧

这一段用了一个很好的逻辑对比：

```text
智能合约代码是公开的
    但是
投资者难以审查
    所以
需要自动检测方法
    现有方法包括交易型和代码型
    但是
交易型方法太晚
    因此
本文关注代码型早期检测
```

这个结构很适合你学习。它不是直接说“我们做 code-based detection”，而是通过比较说明为什么这个方向必要。

---

# 4. 第 3 段：明确提出 Research Gap

第三段是 Introduction 里最关键的一段，因为它提出了两个挑战：

> “The above code-based approaches for smart Ponzi scheme detection still suffer from two major challenges: (C1) limited adaptability... (C2) lack of transparency...”

这就是全文的 research gap。

## C1：limited adaptability

作者说，现有 code-based 方法依赖固定规则，或者依赖已有标注合约训练出来的模型。但是诈骗者会不断创造新的庞氏骗局变体，从而规避这些规则或标准。

这说明现有方法的问题不是完全无效，而是：

**它们对未知变体、新型骗局的适应能力不足。**

这对本文非常重要，因为后文 PonziLens+ 的核心目标之一就是让用户通过查看执行行为来识别新变体，而不是完全依赖固定规则。

## C2：lack of transparency

作者指出，很多方法使用机器学习模型和抽象特征，把智能合约分类为庞氏或非庞氏，但不能提供用户能理解、可信的证据。

这对可视化论文尤其关键。

因为可视化系统的价值通常不是“替代人判断”，而是：

> **帮助人看见证据、理解过程、形成判断。**

这里作者还提到他们之前的初步工作 PonziLens 尝试在 opcode 执行层面解释庞氏特征，但普通投资者仍然难以理解复杂 opcode，因此还缺少更直观的方法。

这个细节很有意思，因为它说明本文不是凭空出现的，而是在前作基础上进一步推进：

```text
已有自动检测方法：不透明
    ↓
前作 PonziLens：可视化 opcode，但仍然太底层
    ↓
本文 PonziLens+：进一步提取语义行为，让普通投资者也能理解
```

---

## 这一段为什么写得好？

它好在 gap 非常精准，而且和后文贡献完全对应。

|Challenge|后文如何回应|
|---|---|
|C1 limited adaptability|不直接依赖固定规则，而是让用户查看语义行为序列，识别不同类型和新变体|
|C2 lack of transparency|用三层可视化模块展示行为和证据|
|opcode 太难理解|提取 semantic-meaningful actions|

也就是说，Introduction 里提出的每一个问题，后文都有对应解决方案。  
这就是高质量论文写作的关键：**gap 和 contribution 必须一一扣合。**

---

# 5. 第 4 段：提出本文方案 PonziLens+

第四段的功能是 **solution overview**。

作者开头说：

> “To address these challenges, we propose PonziLens+, a novel visual analytic approach...”

这是标准的 solution transition：

**To address these challenges → we propose...**

接着作者说明 PonziLens+ 的核心目标：

> “help smart contract investors and auditors identify smart Ponzi schemes intuitively.”

这里明确了两个目标用户：

1. smart contract investors；
    
2. auditors。
    

这很重要，因为可视化论文必须明确谁在用系统。不同用户会影响任务需求、视觉复杂度、评估设计。

然后作者说明系统的分析方式：

> “reveal the semantic meaning of the smart contract execution process”  
> “allow users to conveniently validate various smart contracts across three levels: contract, execution path group, and execution path.”

这里有两个关键信息：

第一，本文不是直接展示代码，而是展示代码执行过程的语义。  
第二，系统采用三层分析结构：

1. **contract level**：合约整体；
    
2. **execution path group level**：执行路径组；
    
3. **execution path level**：单条执行路径。
    

这其实就是可视化系统设计的主轴。

---

## 这一段的核心逻辑

作者在这一段完成了两个转化：

### 转化一：从 bytecode 到 semantic action sequence

原始智能合约 bytecode 太难懂，所以作者设计了一个框架，把复杂 bytecode 翻译成有语义的 action sequences。

这个转化是本文的方法基础：

```text
bytecode / opcode
    ↓
semantic action sequences
    ↓
visualization
    ↓
human identification
```

### 转化二：从黑盒分类到用户验证

现有方法往往是直接输出“是 / 不是庞氏骗局”。  
而 PonziLens+ 的思路是让用户看到行为和证据，自己验证。

这就是 visual analytics 的典型定位：

> 不是完全自动化决策，而是人机协同分析。

---

# 6. 第 5 段：介绍评估和扩展价值

第五段主要有两个功能：

1. 说明如何评价 PonziLens+；
    
2. 说明它的潜在应用不局限于庞氏骗局。
    

作者写道，他们进行了：

- two case studies；
    
- in-depth interviews；
    
- 对象包括 domain experts 和 common investors。
    

这和可视化论文常见评估方法一致。因为系统的目标是帮助用户理解和判断，所以用户访谈比单纯算法指标更符合论文目标。

作者还提到 PonziLens+ 可能应用于：

- source code understanding；
    
- software testing。
    

这一步的作用是提升论文格局。它告诉读者，本文虽然以智能庞氏骗局为场景，但其“把程序执行过程转化为语义行为并可视化”的方法可能具有更广泛意义。

---

# 7. 第 6 段：贡献列表

Introduction 最后给出三点贡献。这个贡献列表非常典型：

## Contribution 1：数据 / 方法贡献

> 提出一个框架，从智能合约执行中提取 semantic-meaningful actions。

这不是视觉设计本身，而是数据抽象贡献。  
在可视化论文中，数据抽象经常是核心贡献之一。

## Contribution 2：系统 / 设计贡献

> 设计 PonziLens+，包含三个可视化模块，用于 top-down 分析庞氏相关特征。

这是可视化系统贡献。  
关键词是：

- visual analytic approach；
    
- three visualization modules；
    
- top-down analysis；
    
- adaptive and reliable identification。
    

## Contribution 3：评估贡献

> 通过两个案例研究和用户访谈证明系统有效性和可用性。

这是 empirical evaluation contribution。  
可视化论文通常需要这一类贡献，因为系统必须证明对真实用户有帮助。

---

# 8. Introduction 的深层写作逻辑

这篇 Introduction 表面上是在介绍智能庞氏骗局，但深层逻辑是把研究问题一步步塑造成一个可视化问题。

我们可以分四步看：

## 第一步：这是一个严重问题

智能庞氏骗局造成重大损失，影响区块链生态。

## 第二步：这是一个早期识别问题

交易发生后再检测已经太晚，所以需要在交易前基于代码识别。

## 第三步：这是一个理解与信任问题

现有代码检测方法不透明，用户无法理解为什么合约被判定为庞氏骗局。

## 第四步：这是一个可视分析问题

既然用户需要理解执行行为和证据，就需要把底层代码转成语义行为，并通过可视化支持多层级分析。

也就是说，作者不是直接说“我们需要可视化”。  
而是把逻辑推到这里，让读者自然接受：

> 这个问题需要可视化，因为用户需要理解复杂执行行为，而不是只得到黑盒分类结果。

---

# 9. Introduction 的“可视化论文特色”

这篇 Introduction 和普通机器学习论文最大的区别在于，它强调的是：

## 9.1 透明性，而不只是准确率

机器学习论文常说 existing methods are inaccurate。  
但这篇论文更强调：

> existing methods lack transparency.

这正是可视化论文的价值空间。

## 9.2 用户决策，而不只是模型输出

作者反复提到 investors 和 auditors。  
这说明系统的最终目标是服务人的判断，而不是单纯替代人。

## 9.3 多层级理解，而不只是分类

论文不是说“我们输出一个标签”，而是说系统支持：

- contract level；
    
- group level；
    
- path level。
    

这是典型的可视分析思路：先 overview，再 drill down。

## 9.4 语义抽象，而不只是数据展示

本文不是直接可视化 bytecode，而是提取 semantic actions。  
这说明可视化论文的关键不只是“画图”，而是如何定义适合人理解的数据表示。

---

# 10. Introduction 的段落级写作公式

你可以把这篇 Introduction 抽象成下面这个模板：

## Paragraph 1：领域问题严重性

> 传统问题 X 长期存在。随着新技术 Y 的发展，X 以新的形式 Z 出现。由于 Z 具有某些新特征，它造成了严重影响。真实案例和统计数据表明，这一问题已经带来巨大损失。

## Paragraph 2：已有方法与任务定位

> 虽然相关数据是公开的，但目标用户难以直接理解，因此已有研究提出了自动检测方法。这些方法大致分为 A 类和 B 类。A 类方法存在时效性问题，因此本文关注能够更早介入的 B 类方法。

## Paragraph 3：研究缺口

> 然而，B 类方法仍然存在两个主要挑战：C1 和 C2。C1 说明方法难以适应变化；C2 说明方法缺乏可解释证据。因此，需要一种更直观、可靠、适应性强的方法。

## Paragraph 4：本文方案

> 为解决这些挑战，我们提出系统 S。该系统首先将复杂原始数据转化为语义化表示，然后通过多层级可视化模块支持用户理解、验证和识别目标模式。

## Paragraph 5：评估和扩展价值

> 我们通过案例研究和用户研究评估系统。结果表明系统有效且可用。此外，该方法还有潜力扩展到相关任务。

## Paragraph 6：贡献列表

> 本文贡献包括：数据抽象方法、可视分析系统、案例与用户评估。

---

# 11. 这篇 Introduction 最值得你学习的地方

我认为最值得学习的是三点。

## 第一，gap 提得非常准

它没有笼统地说“已有方法不好”，而是明确说：

- 适应性不足；
    
- 透明性不足。
    

这两个 gap 都非常适合引出可视化系统。

## 第二，方法和 gap 严格对应

C1 适应性不足 → 用语义行为帮助用户分析各种合约，而不是只靠固定规则。  
C2 透明性不足 → 用可视化展示行为证据。  
opcode 太难懂 → 提取 semantic actions。

这种一一对应让论文逻辑非常稳。

## 第三，Contribution 不是堆砌，而是分层

它的三点贡献分别是：

1. 数据抽象；
    
2. 可视系统；
    
3. 用户验证。
    

这正好对应可视化论文的三个核心问题：

1. 看什么？
    
2. 怎么看？
    
3. 看了有没有用？
    

---

# 12. 用一句话总结 Introduction 的写作逻辑

这篇 Introduction 的核心写法是：

> 作者先把智能庞氏骗局塑造成一个高风险、早期识别困难的问题；再指出现有代码检测方法虽然能提前介入，但缺乏适应性和透明性；随后提出通过语义行为抽象和三层级可视分析系统，让投资者和审计员能够理解智能合约执行行为并形成可信判断；最后用案例研究和用户访谈作为验证闭环。

这就是一篇优秀可视化论文 Introduction 的基本范式：  
**现实问题 → 用户困境 → 方法缺口 → 可视分析机会 → 系统方案 → 贡献闭环。**





