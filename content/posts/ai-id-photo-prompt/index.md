---
title: "一句 Prompt 生成超逼真证件照：ChatGPT 图像生成实测"
date: 2026-04-27T22:00:00+08:00
draft: false
tags: ["AI", "ChatGPT", "图像生成", "Prompt"]
categories: ["技术"]
description: "用一句精心调教的 Prompt，让 ChatGPT 生成堪比影楼级别的职业证件照。附完整提示词和效果对比。"
feature: "cover.png"
featureAlt: "AI 证件照摄影工作室"
comments: true
---

## 起因

最近在 Linux.do 上看到一个帖子，作者 [@dkly2004](https://linux.do/t/topic/1998770) 分享了一个 Prompt，可以让 ChatGPT 生成极其逼真的职业证件照。效果好到什么程度？——你把生成的照片发给朋友，朋友以为是真的。

作为一个懒得去照相馆的人，这太对胃口了。

## 完整 Prompt

直接上干货，以下是调教过的完整提示词：

> Transform the photo into a high-end studio portrait in the style of Apple executive headshots. The subject is shown in a half-body composition, wearing professional yet minimalist attire, with a natural and confident expression. Use soft directional lighting to gently.

**使用方式：**

1. 打开 ChatGPT（建议使用 GPT-4o 及以上模型）
2. 上传你的一张**单人自拍**（清晰度越高越好）
3. 直接把上面的 Prompt 发给它，不需要特意选择"生成图片"
4. 一次过，等结果就行

## 为什么这个 Prompt 效果好？

作者在帖子下面聊了不少关于调 Prompt 的心得，我觉得有几点挺有价值的：

**1. 借力知识库中已有的风格模板。** Prompt 里提到了 "Apple executive headshots"，苹果官网高管照的风格在训练数据中大概率存在，所以模型知道你要的是什么样的背景、什么样的光影、什么样的气质。

**2. 限定而不过度限定。** 半身构图、简约职业装、自信自然的表情——这些描述足够让模型理解方向，但又留出了发挥空间。写太死反而容易出奇怪的结果。

**3. 反复磨合，最终精简。** 作者说他一开始的描述非常长，后来让 AI 自己总结成一句话，再反复测试直到输出稳定。这个"先展开再压缩"的思路值得学习。

## 效果展示

没有提供参考照片，让 ChatGPT 自己想象一个人物生成的效果，依然非常逼真：

![AI 生成的职业证件照效果](/images/posts/ai-id-photo-result.png)

也有网友拿**二次元图片**去跑，做了一波"二次元转三次元拟真"，效果也很有趣：

![二次元转拟真效果](/images/posts/ai-id-photo-chatgpt.png)

## 注意事项

最后提几个注意点：

- ⚠️ **不要用来生成身份证件。** 这是违法的，原帖作者也特别强调了这一点。
- 📸 **输入照片质量很重要。** 清晰的单人正面/侧面照效果最好，模糊的或多人的效果会差很多。
- 🤖 **建议用 ChatGPT。** 作者测试过其他模型（如 Gemini），也能出效果但风格偏好不同（Gemini 偏爱侧脸）。ChatGPT 的稳定性最好。
- 🔄 **不满意就多试几次。** AI 生成本身有随机性，同一个 Prompt 每次出图都会有差异。

## 应用场景

合法合理的使用场景还是很多的：

- 📋 LinkedIn / 简历用的职业头像
- 🎨 社交媒体头像
- 📝 博客、个人网站的 About 页面配图
- 🎭 创意项目中的虚拟人物形象

---

*延伸阅读：[原帖 - 超级逼真 AI 生成证件照](https://linux.do/t/topic/1998770)，评论区有更多网友的生成效果和讨论。*
