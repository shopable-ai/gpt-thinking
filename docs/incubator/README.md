# Incubator｜孵化中的想法、研究与商业模式

Status: Incubating
Updated: 2026-09-22

## 定位

这个目录用于保存尚未进入正式知识库或决策记录的阶段性内容，包括：

- 灵感与机会发现
- 商业模式假设
- 市场研究 Prompt
- 技术可行性基线
- 候选方案比较
- 受控实验设计
- 尚未验证的产品方向

这里的内容允许继续变化，不应自动视为仓库正式事实、最终结论或已接受决策。

## 与现有正式目录的关系

- `docs/incubator/`：探索中，可推翻，可继续扩充。
- `docs/knowledge/`：经过核验、可长期复用的稳定知识。
- `docs/decisions/`：已经做出的正式决策与依据。

推荐演进：

~~~text
聊天 / 灵感 / 外部研究
        ↓
docs/incubator/
        ↓
验证、补证、反方审查
        ↓
├─ 稳定知识 → docs/knowledge/
├─ 正式决策 → docs/decisions/
└─ 放弃方案 → 保留结论并标记 Deprecated / Rejected
~~~

## 当前主题

### knowledge-asset-production

研究如何将 YouTube、Podcast、访谈、课程、Webinar、演讲等长内容，转化为高保真、可阅读、可搜索、可持续复用的知识资产，并进一步验证其商业机会。

当前文件：

- `knowledge-asset-production/technical-baseline.md`：阶段性技术方案基线。
- `knowledge-asset-production/market-research-prompt.md`：下一轮商业验证与市场研究 Prompt。

## 使用规则

1. 新内容若仍属于假设、草稿或阶段性研究，优先放入 `incubator`，不要过早写入正式 Knowledge/ADR。
2. 每份孵化文档应标明状态、日期和待验证问题。
3. 市场数据、价格、产品能力、法律与平台规则等易变化信息，必须保留来源和日期。
4. 成熟后迁移，不要求 `incubator` 永久保持整洁；它本身就是研究工作区。
5. 不把聊天记录本身当事实，真正需要长期保留的核心内容应落到文件。
