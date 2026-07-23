# 朱雀 AI 检测技能

通过浏览器自动化调用腾讯朱雀，检测文本/图片是否由 AI 生成。

兼容 **Trae** 和 **腾讯 WorkBuddy**。

## 功能

- **文本检测** — 将文本提交到腾讯朱雀网页版，判定 AI 生成 / 人工创作 / 不确定
- **图片/视频检测** — 上传图片检测 AI 生成概率
- **分段标注** — 红色（AI 生成）、黄色（不确定）、绿色（人工创作）

## 安装

将 SKILL.md 复制到技能目录：

`
cp SKILL.md ~/.trae-cn/skills/zhuque-detect/SKILL.md
`

## 使用

安装后在对话中直接说：

- "帮我检测一下这段文字是不是 AI 写的"
- "朱雀检测这段文本"
- "这段文章是 AI 生成的吗？"

技能会自动触发。

## 工作原理

`
用户输入文本
  ↓
浏览器打开 matrix.tencent.com
  ↓
清空示例 → 输入文本 → 点击立即检测
  ↓
等待结果 → 解析判定 → 返回给用户
`

使用 integrated_browser MCP 服务控制浏览器，模拟真实用户操作。

## 限制

| 项目 | 说明 |
|------|------|
| 文本最少字数 | 200 字 |
| 每日免费次数 | 约 5 次 |
| 结果类型 | 定性判定（无数值置信度） |
| 依赖 | 需要 MCP integrated_browser 服务 |

## 结果判定对照

| 朱雀提示 | 含义 |
|---------|------|
| 未发现明显的人工创作特征 | AI 生成 |
| 未发现明显的AI生成特征 | 人工创作 |
| 警告级 | 不确定 |

## 相关项目

- [Sophomoresty/zhuque](https://github.com/Sophomoresty/zhuque) — 朱雀 CLI 工具
- [腾讯朱雀](https://matrix.tencent.com/ai-detect/ai_gen_txt) — 官方网页版

## 许可

MIT