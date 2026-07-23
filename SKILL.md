---
name: zhuque-detect
description: "通过腾讯朱雀网页版检测文本或图片是否为 AI 生成。当用户要求检测 AI 生成内容、提到朱雀检测时触发。"
---

# 朱雀 AI 检测 (Tencent)

通过浏览器自动化调用腾讯朱雀 (腾讯朱雀) 网页版检测文本和图片是否由 AI 生成。

文本检测地址: https://matrix.tencent.com/ai-detect/ai_gen_txt
图片/视频检测地址: https://matrix.tencent.com/ai-detect/ai_gen

## 支持平台

本技能兼容 **Trae** 和 **腾讯 WorkBuddy** (均使用 integrated_browser MCP 服务)。

## 检测流程

### 文本检测

1. **打开页面**: 导航到 https://matrix.tencent.com/ai-detect/ai_gen_txt
2. **关闭弹窗**: 按 Escape 关闭通知弹窗 (如"重要声明")
3. **清空内容**: 点击"清空"按钮
4. **输入文本**: 定位文本框 (role=textbox, placeholder 包含"请输入需要检测的文本"), 使用 rowser_type 输入文本, 设置 clear: true
5. **提交检测**: 点击"立即检测"按钮
6. **等待结果**: 等待"检测中"文字消失 (使用 rowser_wait_for, 设置 	extGone: 检测中)
7. **提取结果**: 截取快照, 读取结果卡片 (.card-right 或 .rst 元素)
8. **解析判定**:
   - l-alert--error 类 + "未发现明显的人工创作特征" = **AI 生成**
   - l-alert--success 类 + "未发现明显的AI生成特征" = **人工创作**
   - l-alert--warning 类 = **不确定**
   - 文本分段颜色: 	xt-segmentType-danger (AI), 	xt-segmentType-warning (不确定), 	xt-segmentType-success (人工)
9. **解锁浏览器**

### 图片/视频检测

1. 导航到 https://matrix.tencent.com/ai-detect/ai_gen
2. 如果在文本页面, 点击"图片/视频"选项卡
3. 通过文件输入或粘贴上传图片
4. 点击"立即检测"并等待结果
5. 解析 AI 生成概率

## DOM 选择器

| 元素 | 选择器 |
|------|--------|
| 文本输入框 | ole=textbox 或 .txt-segment-box 父区域 |
| 检测按钮 | 包含"立即检测"的按钮 |
| 清空按钮 | 包含"清空"的按钮 |
| 结果卡片 | .card-right .el-card__body 或 .rst |
| 判定提示 | .el-alert--error (AI) / .el-alert--success (人工) / .el-alert--warning (不确定) |
| 分段高亮 | .txt-segmentType-danger / .txt-segmentType-warning / .txt-segmentType-success |
| 剩余次数 | 检测按钮文本中的"今日剩余N次" |
| 上传按钮 | 包含"上传"的按钮 |

## 结果判定对照

| 朱雀提示 | 含义 |
|---------|------|
| 未发现明显的人工创作特征 | **AI 生成** (alert--error, 红色) |
| 未发现明显的AI生成特征 | **人工创作** (alert--success, 绿色) |
| 警告级结果 | **不确定** (alert--warning, 黄色) |

## 限制条件

- **文本最少字数**: 200 字
- **每日免费次数**: 约 5 次 (按钮显示"今日剩余N次")
- **无需登录** 即可使用基础检测
- **弹窗遮挡**: 操作前务必按 Escape 关闭
- **文本框发现**: 点击"清空"后, 文本框会以 ole=textbox 出现在快照中。清空前可能隐藏为普通 div

## 错误处理

- "检测中" 30 秒未消失, 截图诊断
- 弹窗遮挡点击, 按 Escape 后重试
- "今日剩余0次", 提示用户已达每日上限

## 输出格式

向用户展示检测结果:

`
朱雀AI检测结果：
- 判定：[AI生成 / 人工创作 / 不确定]
- 检测类型：文本 / 图片
- 分段详情：[各段颜色标注含义]
- 剩余次数：N次
`

## 备注

- 网页版不返回数值型置信度, 仅返回定性判定
- 如需数值 (confidence, labels_ratio), 需使用 CLI 工具 zhuque, 但目前被腾讯 TDC 指纹验证拦截 (errorCode 51)
- 页面显示模型更新日期 (如"模型更新时间：2026-07-21")