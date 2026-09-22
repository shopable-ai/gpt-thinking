# Video / Podcast → 中文知识资产：技术方案基线

Status: Incubating Baseline
Date: 2026-09-22

> 这是当前阶段的技术工作基线，不是永久定论。后续真实 Demo、新模型或新工具可以推翻其中局部结论。

## 1. 真正目标

目标不是简单的“视频 → 字幕 → AI 摘要”。

推荐理解为：

~~~text
Video / Podcast / Webinar / Course / Interview
        ↓
可靠恢复 Source
        ↓
Transcript + Speaker + Timestamp + 必要 Visual Evidence
        ↓
语义结构恢复
        ↓
Claims / Concepts / Arguments / Evidence / Examples / Methods / Caveats
        ↓
Knowledge Map
        ↓
中文可读稿 / Guide / Playbook / FAQ / Book / Knowledge Hub
~~~

核心原则：Knowledge Asset ≠ Summary。

## 2. 当前最高优先级结论

经典路线：

~~~text
下载视频 → 提取音频 → Whisper → 文本 → LLM → 文章
~~~

可以工作，但不应成为默认。

当前推荐：

~~~text
Transcript-first
↓
字幕不足时才 ASR fallback
↓
多人对话才开启 Speaker / Diarization
↓
视觉内容确有知识价值时才开启 Slides / Keyframes / OCR / Vision
~~~

原因：

1. 人工字幕通常优于重新 ASR。
2. 自动字幕在很多视频上已经足够。
3. 不必要的 ASR 增加时间、成本和错误。
4. 原始 Transcript 必须独立保存，不能只留下最终文章。
5. “知识恢复”和“知识呈现”必须分层。

## 3. 当前方案评分

| 排名 | 方案 | 当前评分 | 适用 |
|---:|---|---:|---|
| 1 | Adaptive Transcript-first Pipeline | 97/100 | 长期最佳架构 |
| 2 | steipete/summarize + 自定义 Knowledge Skill | 95/100 | 最快工程化 |
| 3 | Vibe + LLM 中文编辑 | 93/100 | 少开发即可使用 |
| 4 | yt-dlp + faster-whisper / WhisperX + Fabric | 92/100 | 本地、完全可控 |
| 5 | Gemini 直接理解 YouTube / Video | 90/100 | 临时快速阅读 |
| 6 | Scriberr | 89/100 | 自托管 Transcript Library |
| 7 | VideoInk | 87/100 | Video-to-Article 产品形态参考 |

这些分数针对“长视频/访谈 → 高保真中文知识资产”，不是通用软件排名。

## 4. 推荐主链

~~~text
Source URL / File
        ↓
Source Probe
        ↓
Transcript Acquisition
        ↓
优先 Published / Human Transcript
        ↓
Platform Caption / Auto Caption
        ↓
Quality Gate
        ↓
不足时 Audio → ASR Adapter
        ↓
Canonical Transcript
        ↓
Timestamp / Segment / Speaker（按需）
        ↓
Canonical Source Package
        ↓
Semantic Units
        ↓
Knowledge Extraction
        ↓
Knowledge Architecture
        ↓
Knowledge Assets
~~~

## 5. Source Acquisition 默认优先级

~~~text
1. Published / human transcript
2. Platform caption
3. Auto caption
4. yt-dlp subtitle extraction
5. Audio extraction
6. ASR
~~~

必须记录字幕来源、语言、生成方式、时间戳、ASR 模型/版本（若使用）、异常和处理时间。

## 6. ASR 不写死为 Whisper

长期应是 Adapter：

~~~text
ASR Adapter
├─ faster-whisper
├─ WhisperX
├─ Parakeet / Nemotron
├─ Cloud ASR
└─ future backend
~~~

`faster-whisper` 适合高效率本地 ASR；`WhisperX` 更适合 word-level timestamp、alignment、long-form 与 speaker diarization。

Speaker 未充分确认时保留 `SPEAKER_01`、`SPEAKER_02`，不要强行映射真人姓名。

## 7. Visual 按内容类型路由

~~~text
conversation / podcast
→ transcript-first

lesson / slides
→ transcript + slide extraction

demo / UI
→ transcript + keyframes + OCR/Vision

code / technical walkthrough
→ transcript + screen/code evidence
~~~

只有 PPT、代码、流程图、表格、公式、UI 演示等无法由语言恢复的内容，才值得增加视觉处理。

## 8. Transcript 与 Article 必须分离

推荐成果：

~~~text
01_source.json
02_transcript.raw.json
03_transcript.clean.md
04_semantic-units.json
05_knowledge-map.md
06_article.zh.md
07_study-notes.zh.md
08_faq.md
09_manifest.json
~~~

Transcript 是知识采集成果；Article 只是知识呈现方式之一。

## 9. 从“摘要”升级为“知识重组”

不要默认：

~~~text
10000 字视频 → 1000 字摘要
~~~

推荐：

~~~text
Transcript
↓
Semantic Units
↓
Claims
↓
Arguments
↓
Evidence
↓
Examples
↓
Definitions
↓
Frameworks
↓
Methods
↓
Caveats
↓
Questions
↓
Cross-links
↓
Knowledge Map
~~~

删除口语噪声和机械重复；保留观点、原因、推理链、限定条件、反例、案例、数字、分歧、不确定性、术语和时间锚点。

## 10. 关键项目当前定位

- `steipete/summarize`：通用 Source Extraction / YouTube fallback / transcription routing 层；适合快速工程化。
- `Vibe`：可直接使用的本地音视频转录工作台，适合快速验证。
- `faster-whisper`：ASR Engine，不是完整 Knowledge Pipeline。
- `WhisperX`：Long-form / Speaker / Alignment Enhancement。
- `Fabric`：适合研究 Transcript → Structured Knowledge Patterns，例如 claims、concepts、examples、recommendations、wisdom。
- `VideoInk`：适合参考 Video → Transcript → Article、Skill-native、Markdown bundle 的产品形态。
- `Scriberr`：更偏私有化 Transcript / Media Knowledge Library。
- `Gemini`：适合直接理解视频和快速 prototype，但不应替代长期可追溯 Source Archive。
- `NotebookLM`：更适合作为 Knowledge Workspace / Research & Synthesis Layer，而非唯一 Extractor。

## 11. 三档落地

### A. 今天就使用

~~~text
Vibe + ChatGPT / Gemini / Claude
~~~

### B. 快速自动化

~~~text
steipete/summarize
↓
canonical transcript
↓
自定义 video-to-reading / knowledge-asset Skill
↓
Markdown assets
~~~

### C. 长期产品化

~~~text
Source Probe
↓
Transcript Router
↓
ASR Adapter
↓
Speaker / Visual Router
↓
Canonical Source Package
↓
Knowledge Extraction Patterns
↓
Knowledge Architecture
↓
Asset Generators
↓
Verification
↓
Publishing
~~~

不自研 ASR 模型，不重复造 yt-dlp，不从零复制 Notebook 产品。

## 12. 受控验证样本

样本：`https://www.youtube.com/watch?v=uhJJgc-0iTQ`

建议抽取 10–15 分钟比较：

1. YouTube transcript；
2. `steipete/summarize` extraction；
3. Vibe / faster-whisper / WhisperX；
4. Gemini direct video。

评价专有名词、漏句、错词、speaker、timestamp、断句、限定条件、案例、中文最终稿完整度、处理耗时、成本和可追溯性。

## 13. 后续研究默认继承

1. 技术上已经能够完成 Video/Podcast → 高质量 Source Recovery。
2. 不必默认下载完整视频。
3. Transcript-first 是默认策略。
4. ASR 是 fallback，不是业务价值本身。
5. Speaker / Visual 按内容类型开启。
6. Canonical Source Package 必须保存。
7. Knowledge Asset ≠ Summary。
8. 工具热度和技术能力不是市场需求证据。
9. 下一阶段重点转向：谁付钱、为何付钱、交付什么、价格、持续价值、竞争、获客、Rights / Compliance、复购。
