---
name: zhuque-detect
description: "Detect AI-generated text/images via Tencent Zhuque web. Invoke when user asks to check/detect whether text or images are AI-generated, or mentions Zhuque detection."
---

# Zhuque AI Detection (Tencent)

Use browser automation to detect AI-generated text and images via Tencent Zhuque (腾讯朱雀) web interface at `https://matrix.tencent.com/ai-detect/ai_gen_txt` (text) and `https://matrix.tencent.com/ai-detect/ai_gen` (image/video).

## Supported Platforms

This skill works on **Trae** and **Tencent WorkBuddy** (both use the same MCP browser tools under `integrated_browser` server).

## Workflow

### Text Detection

1. **Navigate** to `https://matrix.tencent.com/ai-detect/ai_gen_txt`
2. **Dismiss overlay**: Press `Escape` to close any notification popups (e.g., 重要声明)
3. **Clear existing content**: Click the 清空 button
4. **Input text**: Locate the textbox (role=textbox, placeholder contains 请输入需要检测的文本) and type the user text using `browser_type` with `clear: true`
5. **Submit**: Click 立即检测 button
6. **Wait for result**: Wait for 检测中 text to disappear (use `browser_wait_for` with `textGone: 检测中`)
7. **Extract result**: Take a snapshot and read the result card (`.card-right` or `.rst` element)
8. **Parse verdict**:
   - `el-alert--error` class + 未发现明显的人工创作特征 = **AI-generated**
   - `el-alert--success` class + 未发现明显的AI生成特征 = **Human-written**
   - `el-alert--warning` class = **Uncertain**
   - Text segment colors: `txt-segmentType-danger` (AI), `txt-segmentType-warning` (uncertain), `txt-segmentType-success` (human)
9. **Unlock browser** when done

### Image Detection

1. Navigate to `https://matrix.tencent.com/ai-detect/ai_gen`
2. Click 图片/视频 tab if on text page
3. Upload image via file input or paste
4. Click 立即检测 and wait for result
5. Parse the AI generation probability from result

## Key DOM Selectors

| Element | Selector |
|---------|----------|
| Text input | `role=textbox` or `.txt-segment-box` parent area |
| Detect button | Button containing 立即检测 |
| Clear button | Button containing 清空 |
| Result card | `.card-right .el-card__body` or `.rst` |
| Verdict alert | `.el-alert--error` (AI) / `.el-alert--success` (human) / `.el-alert--warning` (uncertain) |
| Segment highlights | `.txt-segmentType-danger` / `.txt-segmentType-warning` / `.txt-segmentType-success` |
| Remaining count | Text in detect button: 今日剩余N次 |
| Upload button | Button containing 上传 |

## Result Interpretation

| Verdict Text | Meaning |
|-------------|----------|
| 未发现明显的人工创作特征 | **AI-generated** (alert--error, red) |
| 未发现明显的AI生成特征 | **Human-written** (alert--success, green) |
| Warning-level result | **Uncertain** (alert--warning, yellow) |

## Constraints

- **Text minimum**: 200 characters
- **Daily limit**: Free users get ~5 detections/day (shown in button as 今日剩余N次)
- **No login required** for basic detection
- **Overlay popup**: Always dismiss with Escape before interacting
- **Textbox discovery**: After clicking 清空, the textbox appears as `role=textbox` in snapshot. Before clearing, it may be hidden as a generic div.

## Error Handling

- If 检测中 does not disappear within 30s, take a screenshot to diagnose
- If overlay blocks clicks, press Escape then retry
- If 今日剩余0次, inform user the daily limit is reached

## Output Format

Present results to user as:

```
朱雀AI检测结果：
- 判定：[AI生成 / 人工创作 / 不确定]
- 检测类型：文本 / 图片
- 分段详情：[各段颜色标注含义]
- 剩余次数：N次
```

## Notes

- The web version does NOT return numeric confidence scores,
- For numeric scores (confidence, labels_ratio), the CLI tool zhuque is needed but currently blocked by Tencent TDC fingerprint validation (errorCode 51)
- Model update date is shown on page (e.g., 模型更新时间：2026-07-21)
