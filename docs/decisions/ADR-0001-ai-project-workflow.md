# ADR-0001：AI项目长期上下文管理方案

Status: Accepted
Date: 2026-06-05

## 结论（最高优先级）

当前推荐采用：

ChatGPT + 项目文档库 + Codex

职责划分：
- ChatGPT负责推理、规划、架构设计、方案比较、风险分析
- 项目文档负责长期上下文、项目事实、决策记录、错误经验沉淀
- Codex负责代码实现、测试、重构和工程执行

## 方案评分

| 方案 | 评分 |
|--------|--------|
| 单个长对话 | 35 |
| ChatGPT Project | 68 |
| Project + Google Drive | 78 |
| Notion / Wiki | 80 |
| GitHub Docs as Code + Codex | 88 |
| ChatGPT + 文档库 + Codex | 93 |
| 自研MCP文档系统（长期） | 96 |

最终选择：

当前采用：ChatGPT + 项目文档库 + Codex

长期演进：自研MCP文档系统 + ChatGPT + Codex

## 核心原则

聊天是推理现场。
文档是项目真相源。
Codex是执行器。

任何聊天记录都不是事实。
项目文档才是事实。
任何结论都必须沉淀到文档。
任何错误都必须进入Error-KB。
任何废弃方案都必须标记Deprecated。
