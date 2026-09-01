![report-to-the-boss — a green garden gate with a specimen tag reading install, typecheck, 771 tests, all checked](assets/banner.png)

<p align="center">
  <a href="./README.md"><img alt="README in English" src="https://img.shields.io/badge/English-d9d9d9"></a>
  <a href="./README_CN.md"><img alt="简体中文文档" src="https://img.shields.io/badge/简体中文-d9d9d9"></a>
</p>

# /report-to-the-boss

一个让 Claude 的汇报说人话的 Claude Code skill —— 建立在测量之上，不靠感觉。

> "也许你可以告诉我现在到底是怎么回事。还有，拜托，就当是讲给一个小孩子听，或者讲给一条金毛犬。把我带到这个位置的可不是脑子，这一点我可以向你保证。"
> —— John Tuld，《商海通牒》（2011）

## 它解决什么痛点？

Claude 经常甩下一句 **"the *gate*（闸门）is green"** 扭头就走。但问题是：哪个 *gate*？跑了哪些检查？在一项针对 125 万词真实 Claude Code 输出的研究中，我们发现：

- Claude 平均**每 71 个词就会使用一个比喻性名词**；而人类工程师大约每 750 个词才会用一个。
- 当 Claude 说 "gate" 时，**有 45% 的情况下，你当前读到的这条消息里根本没解释这个 gate 指的是什么** —— 甚至有四分之一的情况，整个 session（会话）的可视范围内都找不到任何线索。因为具体的定义要么藏在已经折叠的 tool（工具）输出里，要么是在 30 轮对话之前，再或者直接属于上一个 session。
- Claude 能秒懂这些指代，因为它脑子里装着整个 session 的 context（上下文）。但你没有。可以说，Claudish（Claude 腔）完全是写给“拥有原作者同款 context window（上下文窗口）”的读者看的。

那些看似显而易见的解法，实测全都翻车了。A/B 测试里，要求它“简洁点（Be concise）”反而**推高**了行话密度（p=0.042）—— 密度问题出在每个词上，删短只是删掉了那些本来帮你解码的词。那直接禁掉最典型的 17 个 Claudish 词呢？结果很黑色幽默：执行滴水不漏，却什么都没改变 —— 被删掉的量以同一隐喻家族的同义词形式回来了 128%（*guardrail*（护栏）被禁，*fence*（围栏）顶上；*gate* 被禁，*land*（落地）接班）。

## 这个 skill 是怎么做的？

它从不禁用任何词汇，也从不要求模型简短。它只设定了一个态度和一条硬性规则：

- **态度：** 每一条消息都是向老板汇报。老板很忙、你干活时他不在场、他只看眼前这一条消息，并且他会通过你大白话的解释来判断你到底懂不懂自己做的工作。*“The gate is green”与“不知道是哪个 gate”完全兼容；“install、typecheck、771 个测试全部退出码为 0”就不兼容。*
- **规则：** 随便用那 50 个最典型的 Claudish 词汇 —— 但在消息中首次使用时，必须在一两句话内指出它具体指代什么。测试标准是：读者能否仅凭这一条消息，就回答出**“到底是哪一个？”**。

## 安装

作为 Claude Code plugin（推荐）：

```
/plugin marketplace add YuanpingSong/boss-skill
/plugin install report-to-the-boss@boss-skill
```

或者手动安装为一个裸 skill：

```bash
git clone https://github.com/YuanpingSong/boss-skill.git
cp -r boss-skill/skills/report-to-the-boss ~/.claude/skills/
```

无论用哪种方式，你都可以在任何 session 中通过输入 `/report-to-the-boss` 来调用它。或者，也可以在 `CLAUDE.md` 中引用它，使其成为全局生效的默认策略（standing policy）。

## 现状：第一次 fork 测试已完成 —— 当作交付前的改写环节来用

这一设计源自指令 A/B 测试的结果：模型会切实遵守精准、**具象化**的目标（如特定词表、距离规则），但会无视抽象空洞的请求。第一次 fork 测试 —— 针对一个真实的 session，分别在“开启 skill”、“关闭 skill”以及“在报告生成后追加应用 skill”三种情况下重新生成最终报告 —— 确定了它的正确用法：**当作交付阶段的改写，而不是干活时全程开着的指令。** 应用在已完成的汇报上时，比喻一个没删，全部补上了具体所指 —— 其中两处锚点写下来，把作者自己都没注意到的含糊暴露了出来。反过来，从一开始就开着它，反而会引发它明文否认的行为：模型悄悄把词表上的词换成词表外的同义词。这倒和这个 skill 得名的那场戏对上了 —— 《商海通牒》里，Tuld 也是在 Sullivan 的分析彻底做完之后，才要求听金毛犬版本的。更多 fork 前后对照，之后陆续发。

## 完整研究报告

上面的测量数据、完整方法，以及这个 skill 触发词表的来源 —— **50 词 Claudish 词典** —— 都在博客文章里：

**[门是绿的](https://songyp.com/zh/blog/the-gate-is-green)** · [英文原版](https://songyp.com/blog/the-gate-is-green)

## 开源协议

MIT
