# Zhuque Detect Skill

腾讯朱雀 AI 检测技能 — 通过浏览器自动化检测文本/图片是否由 AI 生成。

兼容 **Trae** 和 **腾讯 WorkBuddy** 两个平台。

## 功能

- 文本 AI 检测：将用户输入的文本提交到腾讯朱雀网页版进行检测
- 图片/视频 AI 检测：上传图片进行 AI 生成概率检测
- 自动解析检测结果：AI生成 / 人工创作 / 不确定
- 分段标注：红（AI）、黄（不确定）、绿（人工）

## 安装

将 `SKILL.md` 文件复制到技能目录：

```bash
# Trae
cp SKILL.md ~/.trae-cn/skills/zhuque-detect/SKILL.md

# WorkBuddy（同理，放到对应的 skills 目录）
```

## 使用

安装后，在对话中直接说：

- "帮我检测一下这段文字是不是 AI 写的"
- "朱雀检测这段文本"
- "这段文章是 AI 生成的吗？"

技能会自动触发，通过浏览器打开朱雀网页版完成检测。

## 工作原理

```
用户输入文本
  ↓
浏览器打开 matrix.tencent.com/ai-detect/ai_gen_txt
  ↓
清空示例 → 输入文本 → 点击立即检测
  ↓
等待结果 → 解析判定 → 返回给用户
```

使用 `integrated_browser` MCP 服务控制浏览器，模拟真实用户操作，绕过 CLI 工具的 TDC 指纹限制。

## 限制

| 项目 | 说明 |
|------|------|
| 文本最少字数 | 200 字 |
| 每日免费次数 | 约 5 次 |
| 结果类型 | 定性判定（无数值置信度） |
| 依赖 | 需要 MCP `integrated_browser` 服务 |

## 结果判定对照

| 朱雀提示 | 含义 | 颜色 |
|---------|------|------|
| 未发现明显的人工创作特征 | AI 生成 | 红色 |
| 未发现明显的AI生成特征 | 人工创作 | 绿色 |
| 警告级 | 不确定 | 黄色 |

## 相关项目

- [Sophomoresty/zhuque](https://github.com/Sophomoresty/zhuque) — 朱雀 CLI 工具（原版，受 TDC 限制）
- [腾讯朱雀](https://matrix.tencent.com/ai-detect/ai_gen_txt) — 官方网页版

## License

MIT