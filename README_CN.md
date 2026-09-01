![report-to-the-boss —— 深夜会议室里，年轻分析师向坐在主位的金毛犬汇报](assets/banner.jpg)

<p align="center">
  <a href="./README.md"><img alt="README in English" src="https://img.shields.io/badge/English-d9d9d9"></a>
  <a href="./README_CN.md"><img alt="简体中文文档" src="https://img.shields.io/badge/简体中文-d9d9d9"></a>
</p>

# /report-to-the-boss

一个让 Claude 输出人类看得懂的汇报的 Claude Code skill —— 基于实测数据打磨，拒绝玄学调优。

> _"也许你可以告诉我现在到底是怎么回事。还有，拜托，就当是讲给一个小孩子听，或者讲给一条金毛犬。把我带到这个位置的可不是脑子，这一点我可以向你保证。"_
> —— John Tuld，《商海通牒》（2011）

## 解决什么痛点

Claude 经常随口甩一句 **“the gate is green”**（闸门亮绿灯了）然后就没下文了。到底是哪个 gate？跑了哪些检查？
我们在分析了 125 万词真实的 Claude Code 输出后发现：

- Claude **平均每 71 个词就会蹦出一个比喻性名词**；而人类工程师大约 750 词才用一个。
- 当 Claude 说出 “gate” 时，**有 45% 的概率你正在看的这条消息里压根没提这个 gate 到底指什么** —— 甚至有四分之一的情况，整个当前 session 里都找不到任何说明。具体的定义要么被折叠在工具输出里，要么在 30 轮对话之前，要么甚至在上一次 session 里。
- Claude 自己能秒懂这些指代，因为它的 context window 里装着整场 session；但人脑不行。这种 “Claudish”（Claude 黑话）本质上是写给拥有和它一样大 context window 的读者看的。

直觉上的几种解法经过实测全都翻车了：

- 让它 “Be concise”（简明扼要）在 A/B 测试中反而**推高了**黑话密度（p=0.042）—— 因为黑话密度是按单词比例算的，一味求简反而把那些本来能帮你解码具体含义的上下文给删了。
- 直接把最高频的 17 个 Claudish 词加入禁用列表？执行得很完美，但毫无作用：被删掉的量有 128% 换上同一隐喻家族的同义皮套卷土重来（_guardrail_（护栏）被禁，_fence_（围栏）顶上；_gate_ 被禁，_land_（落地）接班）。

## 这个 skill 的解法

它不封杀任何词汇，也从不要求“简短”。它只设定了一个汇报立场和一条机械化的硬规则：

- **汇报立场**：每条消息都是向 Boss 汇报 —— 老板很敏锐，但在你干活时不在场，此时只读眼前这条消息，并且会用“你能不能用大白话说清楚”来检验你到底懂不懂自己的工作。_说一句 “the gate is green”，与“不知道是哪个 gate”完全兼容；“install、typecheck 和 771 个测试全部以 exit 0 通过”就不兼容——蒙混不过去。_
- **硬规则**：高频的 50 个 Claudish 词随便用 —— 但在每条消息里首次使用时，必须在紧随其后的一两句话内写明具体的指代对象。检验标准只有一条：读者单凭眼前这条消息，能否回答“具体是指哪一个？”

## 安装方式

作为 Claude Code plugin 安装（推荐）：

```
/plugin marketplace add YuanpingSong/boss-skill
/plugin install report-to-the-boss@boss-skill
```

或者手动作为独立 skill 安装：

```bash
git clone https://github.com/YuanpingSong/boss-skill.git
cp -r boss-skill/skills/report-to-the-boss ~/.claude/skills/
```

无论哪种方式，在任何 session 中输入 `/report-to-the-boss` 即可调用；也可以直接在你的 `CLAUDE.md` 中引用它，将其设为默认执行的全局规则。

## 现状：第一次 fork 测试完成 —— 当作交付前的改写环节来用

这套设计源于一系列指令 A/B 测试：结果显示模型只会严格服从明确的、*具象的*目标（如指定词表、距离规则），而会无视抽象的恳求。在第一次 fork 测试中 —— 我们拿一个真实的 session，分别对比了未开启 skill、全程开启 skill，以及在写完初版报告后追加重写（rewrite pass）这三种情况 —— 明确了最佳用法：**将其作为交付阶段的重写 pass，而不是贯穿干活全程的指令。**

在写好的报告上执行改写，它把所有比喻原样保留、逐一锚定到具体所指；而在补全其中两个锚点时，甚至直接暴露了原作者自己都没注意到的模糊表述。相反，如果从一开始就全局启用，模型反而会做出它明文否认的行为：悄悄把词表里的词替换成词表外的同义词。这也恰好呼应了这个 skill 命名的出处 —— 在电影里，Tuld 也是在 Sullivan 把整套分析做完之后，才要求他换成“讲给金毛听”的版本的。更多 fork 前后对照，之后陆续发。

## 完整研究

完整的博文阐述了上述所有测量数据、测试方法论，以及本 skill 触发词表所依据的 **50 词 Claudish 词典**：

**[《门是绿的》](https://songyp.com/zh/blog/the-gate-is-green)** · [English](https://songyp.com/blog/the-gate-is-green)

## License

MIT
