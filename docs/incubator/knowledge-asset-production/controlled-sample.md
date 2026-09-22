# 受控替代样例：合并报表前，怎样证明没有重复计数？

日期：2026-09-22。性质：研究工件，未对外发布、未销售、不是已完成的Excel产品。

> 2026-09-22 细任务反向核验后，本文件继续作为“重复 / 冲突 / schema 输入契约”的离线逻辑基线，**不再代表当前首个MVP的完整产品规格**。当前MVP已收窄为“新增周期文件后的刷新与结果验收”，见 [research-report.md](research-report.md) 第十七节；Excel GUI / Power Query刷新、回退与第二人交接仍未测试。

## 1. 原视频来源门槛

候选 `https://www.youtube.com/watch?v=uhJJgc-0iTQ` 的本次网页读取只取得标题“Building more effective AI agents”等极少文本。未取得原始字幕（完整或部分）、音频、画面、时间轴、PPT、代码和有效再利用授权。没有运行下载器或ASR，因此不能声称原视频无字幕，或某转写工具已实测失败。

原视频的知识范围、语义结构、观点、例子、方法和限制均维持“不能核定”。以下全部是**替代案例**，不得归属于原视频。

## 2. 替代来源与归属

| 来源/贡献 | 实际取得什么 | 用在哪里 | 不声称什么 |
|---|---|---|---|
| Microsoft官方Combine CSV files（证据T01） | 官方HTML方法说明，2026-04-08更新 | 同结构文件、样本转换、类型与来源检查 | 没下载官方数据/图片，没有运行Excel |
| Python csv官方文档及许可（T02/T03） | 解析行为和许可说明 | 用标准库读取字符串、处理字段与CSV引号 | 不把本地3.13.5验证扩展到所有版本 |
| 本轮原创 | 两个合成CSV、业务约束、排错案例、校验脚本、中文解释 | 完整免费子问题与付费目录小样 | 不是客户实数、不是会计准则或官方产品承诺 |

来源链接见[evidence.md](evidence.md)的T01—T03。只做原创综合与事实性说明，不翻译复制官方整篇文章，不复用第三方视频/PPT/付费课。

## 3. 知识地图

```text
读者任务：每月合并导出表，避免交付错误
├─ 输入到底是什么：交易流水，而不是订单明细/账户余额
├─ 哪条记录代表同一件事：transaction_id；order_id允许多笔交易
├─ 格式：字段名、文本编号、ISO日期、明确币种、两位小数
├─ 数据问题：漏字段、重复、同ID冲突、坏日期、混币种
├─ 合并：原始记录数/合计 → 明确重复策略 → 接受记录数/合计
├─ 验收：源文件清单、来源行号、人工业务核对
└─ 下一月/交接：新文件、字段变化、回退、版本与责任人
```

地图免费。收费的是适用范围内的完整作业说明、练习与经目标环境验证的交付，不是故意藏起这张图或基本步骤。

## 4. 免费文章样稿（完整解决一个子问题）

### 报表合并前，先检查“同一笔钱有没有算两遍”

你拿到两份导出表，复制到一起，金额看起来合理。这仍不足以证明结果正确。本例只处理一种明确输入：**每一行是一笔交易，交易流水号唯一；同一订单可以有付款和退款两笔交易；全部为人民币；金额已经带正负号。** 订单商品明细、分币种总账、余额快照不属于本例，不可套用同一去重规则。

第一份表是：

```csv
transaction_id,order_id,date,amount,currency,note
0001,0010,2026-09-01,100.00,CNY,"sample, A"
0002,0011,2026-09-02,20.00,CNY,B
```

第二份表是：

```csv
transaction_id,order_id,date,amount,currency,note
0003,0010,2026-09-03,-10.00,CNY,refund
```

正确接受结果是3笔交易、人民币110.00，而不是按订单号去重之后只留下两行。`0010`有一笔100元收入和一笔10元退款；我们不能因为订单号相同就删除退款。`0001`也必须保留为文本编号，不能默认转成数字1。

现在制造一个常见错误：文件夹中还放了一份第一张表的副本。未经检查，合并变成5行、人民币230.00。这个结果不会自动显示为错误，也不一定一眼看出异常。

我们的检查不直接“去掉重复”：先按交易流水号检查。若同ID且所有规范化字段一致，默认阻止，要求确认是否重复导出；只有使用者明确批准，才删除这种一致重复，并报告删除数量。若同ID但金额或其他字段不同，始终阻止，不能挑第一条或“相信较新的一条”。这需要回到业务来源核查。

第二道检查是输入约束：字段必须齐全，日期必须有效，金额是明确的十进制数字，不能接收NaN、科学计数或额外小数。混入USD就停止，不把美元与人民币相加；空文件和变更表头也不悄悄忽略。

第三道检查是保留证据。接受结果至少列出：输入行数、独立流水数、批准删除的重复数量、原始总额、接受总额、各源文件合计及每笔流水的来源行。**源数据自身是否遗漏，不能单靠合并后的总额证明**；最终还需与导出系统的记录数/控制总额核对。

复现本例不需要真实客户数据。把文末Python代码块保存为`controlled_sample.py`，使用Python 3.10或更新版本，运行：

```bash
python controlled_sample.py --demo
python controlled_sample.py --self-test
# 自己的合成文件也可使用；不会写回或修改输入文件：
python controlled_sample.py a.csv b.csv
# 仅在人工确认一致重复是重复导出后使用：
python controlled_sample.py --dedupe-identical a.csv a-copy.csv b.csv
```

程序仅向终端输出JSON，不上传数据，不修改文件；异常以`blocked`及非零退出码结束。没有安装Python的纯Excel读者可先用表格人工执行同样四项检查；这不是已经完成易用性验收的非技术用户产品。

使用Power Query制作后续Excel版本时，可参考官方“从文件夹合并与转换”方法，将相同业务约束落入查询，并对来源、类型与错误独立核对。**本轮未实际运行该Excel版本，不能把Python检查通过写成Power Query工作簿已验证。**

这篇文章已经交付了一个完整的小承诺：给定符合上述范围的CSV，识别并阻止重复或冲突，保留文本编号，正确接受退款并留下可核对的结果。它不承诺识别所有业务错误，也不承诺导出源绝对完整。

## 5. 免费之后还可能缺什么

假设读者下个月仍要做相同任务，剩余工作是：找到正确文件、适应字段变化、定位坏行、重复运行、回退、交给同事，而不是“还不知道有Power Query”。需要用户付款与使用验证，不能从这份演示自动推导销售成立。

首项候选：**《每月导出表到可复核报表：合并、查错与交接》任务电子书**。实验单价¥99，不是当前已售价格。电子书本身是收费产品；配原创练习与经验证工作簿用于兑现承诺，不把书降成赠品。初版只承诺Windows版Microsoft 365 Excel、一个明确CSV交易流水结构、手动刷新；不含ERP接口、无人值守、Mac同等兼容、税务判断或不限次数一对一服务。

目录提案：输入与成功标准 → 首次合并 → 类型/重复/冲突故障练习 → 下一月刷新与变更 → 验收/回退/交接。约40—60页是制作预算，不是价值承诺。要求1个目标环境实测工作簿、6种错误练习、答案/验收表、版本及一次勘误更新。**这些付费交付仍是设计，不是本轮已完成产品。**

付费小样“异常处理卡”已可完整展示：

| 症状 | 不该做什么 | 正确返回 | 验收 |
|---|---|---|---|
| 同流水号、不同金额 | 随意留第一条或删重复 | 回源确认更正/退款/导出错误 | 业务解释和更正版本明确 |
| 新文件改了表头 | 忽略文件继续汇总 | 检查结构变更并重新映射 | 输入文件清单与行数完整 |
| 刷新成功但金额不变 | 宣布自动化成功 | 核对来源路径/日期/加载目标 | 新增数据可在结果中定位 |

自然转化文案（研究草稿，不已发布）：

> 这篇文章和代码已经能够检查本例的重复计数问题。你只做一次，用它和官方教程就够了。若每月都要更新并交给同事，拟制作的任务电子书会进一步覆盖文件组织、六类报错、刷新、回退和交接。目标环境验证完成后再开放购买；不承诺适配所有导出表，也不提供财税结论。

现有替代：基础学习优先Microsoft官方合并教程；需要完整Python办公入门，可先读免费《Automate the Boring Stuff》，需要便利/纸本再向正版出版社购买（E19/E20）；需要Power Query系统课程可评估XelPlus/Skillwave，但不能据本轮资料保证个人适合。**本轮没有建立这些书课的联盟关系，不挂虚构返佣链接。**

为什么此时不是会员/大课/外包：重复知识不必按月收费；尚未证明读者要直播反馈；也没有买方要求我们接触生产表代做。若付款者主要卡在操作迁移而非查阅，后续才改测小班教学；不是先同时开四种收入。

## 6. 与普通摘要的对照

普通摘要最多说“文件夹可以合并CSV、设置数据类型并刷新”。本例新增的是原创业务约束、退款反例、拒绝策略、来源追溯、合计核验和真实执行测试。官方来源没有被宣称覆盖这些完整业务判断；这些是本轮原创设计，适用范围受约束。

人工仍需决定交易语义、审查知识与证据、测试目标Excel版本、观察真实用户能否完成、处理授权和售后。当前未验证真实用户节省时间、愿付¥99、可独立使用或持续复购。

## 7. 本次实际验证记录

在容器Python 3.13.5运行文末代码：12个命名测试通过（含多个subTest分支）；首轮输出`Ran 12 tests in 0.007s / OK`。该耗时只是微型合成测试运行时间，不是整条知识生产耗时或生产性能基准。

覆盖：正常合并；前导零和同订单退款；默认拒绝重复；经批准一致去重并对账；冲突始终拒绝；缺失/额外/重复表头；混币种；非法金额；BOM/带逗号字段/列顺序变化；坏日期/缺单元格；空输入；编号空白。

演示：`input_rows=3`，`unique_transactions=3`，`accepted_total_CNY=110.00`。副本练习显式批准后：输入5行，删除2条一致重复，原始合计230.00，接受合计110.00。

未测：原视频恢复/ASR、Excel GUI或M代码、真实客户数据、大文件/并发、用户可用性、营收、税务和来源系统完整性。脚本不进行会计记账、自动付款或外部提交。

## 8. 可复现的原创代码

保存下面唯一的`python`代码块为`controlled_sample.py`即可运行。它是研究逻辑参照，不是正式财务工具。

```python
#!/usr/bin/env python3
"""Original, offline research example. Not a production accounting tool.
Python 3.10+; no third-party dependencies; reads only explicitly named CSVs.
The contract is one signed transaction per transaction_id, single CNY currency.
Run: python controlled_sample.py --demo | --self-test | file1.csv file2.csv
Identical duplicates are BLOCKED unless --dedupe-identical is explicitly given.
"""
from __future__ import annotations
import argparse
import csv
import io
import json
import re
import sys
import unittest
from datetime import date
from decimal import Decimal
from pathlib import Path

FIELDS = ('transaction_id', 'order_id', 'date', 'amount', 'currency', 'note')
HEADER = ','.join(FIELDS) + '\n'
A = HEADER + '0001,0010,2026-09-01,100.00,CNY,"sample, A"\n0002,0011,2026-09-02,20.00,CNY,B\n'
B = HEADER + '0003,0010,2026-09-03,-10.00,CNY,refund\n'

class ValidationError(ValueError):
    """An explicit input-contract failure; no output is accepted."""


def validate(sources: dict[str, str], dedupe_identical: bool = False) -> dict:
    if not sources:
        raise ValidationError('no input files')
    records: dict[str, tuple] = {}
    origins: dict[str, str] = {}
    raw_total = Decimal('0.00')
    unique_total = Decimal('0.00')
    row_count = duplicates = 0
    per_file = {}
    for name, text in sources.items():
        reader = csv.DictReader(io.StringIO(text.lstrip('\ufeff'), newline=''), strict=True)
        try:
            fieldnames = reader.fieldnames
        except csv.Error as exc:
            raise ValidationError(f'{name}: malformed CSV header') from exc
        if fieldnames is None or len(fieldnames) != len(FIELDS) or set(fieldnames) != set(FIELDS):
            raise ValidationError(f'{name}: schema mismatch; require {FIELDS}')
        count = 0
        subtotal = Decimal('0.00')
        try:
            for row in reader:
                where = f'{name}:{reader.line_num}'
                if None in row or any(row[k] is None for k in FIELDS):
                    raise ValidationError(f'{where}: missing or extra field')
                # Do not silently strip identifiers or infer date/currency formats.
                for key in ('transaction_id', 'order_id'):
                    if re.fullmatch(r'[A-Za-z0-9_-]{1,64}', row[key]) is None:
                        raise ValidationError(f'{where}: invalid {key}')
                try:
                    if not re.fullmatch(r'\d{4}-\d{2}-\d{2}', row['date']):
                        raise ValueError('date format')
                    date.fromisoformat(row['date'])
                except ValueError as exc:
                    raise ValidationError(f'{where}: invalid date') from exc
                if row['currency'] != 'CNY':
                    raise ValidationError(f'{where}: only CNY is supported; no FX conversion')
                if re.fullmatch(r'-?\d{1,12}(?:\.\d{1,2})?', row['amount']) is None:
                    raise ValidationError(f'{where}: invalid decimal amount')
                amount = Decimal(row['amount']).quantize(Decimal('0.01'))
                identity = row['transaction_id']
                normalized = (row['order_id'], row['date'], amount, row['currency'], row['note'])
                count += 1
                row_count += 1
                subtotal += amount
                raw_total += amount
                if identity in records:
                    if normalized != records[identity]:
                        raise ValidationError(f'{where}: conflicting transaction {identity}; previous {origins[identity]}')
                    if not dedupe_identical:
                        raise ValidationError(f'{where}: identical duplicate {identity}; requires explicit approval')
                    duplicates += 1
                else:
                    records[identity] = normalized
                    origins[identity] = where
                    unique_total += amount
        except csv.Error as exc:
            raise ValidationError(f'{name}:{reader.line_num}: malformed CSV') from exc
        if not count:
            raise ValidationError(f'{name}: no data rows')
        per_file[name] = {'rows': count, 'raw_total_CNY': str(subtotal)}
    return {'status': 'accepted', 'input_rows': row_count, 'unique_transactions': len(records),
            'identical_duplicates_removed': duplicates, 'raw_total_CNY': str(raw_total),
            'accepted_total_CNY': str(unique_total), 'transaction_ids': sorted(records),
            'origins': origins, 'files': per_file}

class ContractTests(unittest.TestCase):
    def test_clean_merge(self):
        r = validate({'a.csv': A, 'b.csv': B})
        self.assertEqual((r['input_rows'], r['accepted_total_CNY']), (3, '110.00'))
    def test_leading_zero_and_same_order_refund(self):
        self.assertEqual(validate({'a': A, 'b': B})['transaction_ids'], ['0001', '0002', '0003'])
    def test_duplicate_blocked_by_default(self):
        with self.assertRaisesRegex(ValidationError, 'identical duplicate'):
            validate({'a': A, 'copy': A})
    def test_explicit_deduplication_reconciles(self):
        r = validate({'a': A, 'copy': A, 'b': B}, True)
        self.assertEqual((r['input_rows'], r['identical_duplicates_removed'], r['raw_total_CNY'], r['accepted_total_CNY']), (5, 2, '230.00', '110.00'))
    def test_conflict_never_silently_deduplicated(self):
        with self.assertRaisesRegex(ValidationError, 'conflicting'):
            validate({'a': A, 'wrong': A.replace('100.00', '999.00')}, True)
    def test_schema_missing_extra_or_repeated_header(self):
        for body in (A.replace('note\n', 'memo\n'), A + HEADER, A.replace(',currency,note', ',currency,note,extra'), '"' + HEADER):
            with self.subTest(body=body), self.assertRaises(ValidationError):
                validate({'a': body})
    def test_mixed_currency(self):
        with self.assertRaisesRegex(ValidationError, 'only CNY'):
            validate({'a': A, 'b': B.replace('CNY', 'USD')})
    def test_invalid_amounts(self):
        for amount in ('NaN', 'Infinity', '1e3', '1.001', '', ' 100.00'):
            with self.subTest(amount=amount), self.assertRaises(ValidationError):
                validate({'a': A.replace('100.00', amount)})
    def test_bom_quoted_comma_and_reordered_fields(self):
        r = validate({'a': '\ufeff' + A})
        self.assertEqual(r['accepted_total_CNY'], '120.00')
        reversed_csv = 'note,currency,amount,date,order_id,transaction_id\nB,CNY,20.00,2026-09-02,0011,0002\n'
        self.assertEqual(validate({'r': reversed_csv})['accepted_total_CNY'], '20.00')
    def test_invalid_date_and_missing_cell(self):
        for body in (A.replace('2026-09-01', '2026-02-30'), HEADER + '0001,0010,2026-09-01,100.00,CNY\n'):
            with self.subTest(body=body), self.assertRaises(ValidationError):
                validate({'a': body})
    def test_empty_input(self):
        for files in ({}, {'a': HEADER}, {'a': ''}):
            with self.subTest(files=files), self.assertRaises(ValidationError):
                validate(files)
    def test_identifier_whitespace_not_hidden(self):
        with self.assertRaisesRegex(ValidationError, 'invalid transaction_id'):
            validate({'a': A.replace('0001,', ' 0001,')})

def main() -> int:
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument('files', nargs='*', type=Path)
    mode = parser.add_mutually_exclusive_group()
    mode.add_argument('--demo', action='store_true')
    mode.add_argument('--self-test', action='store_true')
    parser.add_argument('--dedupe-identical', action='store_true')
    args = parser.parse_args()
    if (args.demo or args.self_test) and args.files:
        parser.error('demo/self-test must not be combined with file inputs')
    if args.self_test:
        result = unittest.TextTestRunner(verbosity=2).run(unittest.defaultTestLoader.loadTestsFromTestCase(ContractTests))
        return 0 if result.wasSuccessful() else 1
    try:
        if args.demo:
            sources = {'a.csv': A, 'b.csv': B}
        else:
            if len({p.resolve() for p in args.files}) != len(args.files):
                raise ValidationError('same file path supplied more than once')
            sources = {str(p): p.read_text(encoding='utf-8-sig') for p in args.files}
        print(json.dumps(validate(sources, args.dedupe_identical), ensure_ascii=False, indent=2))
        return 0
    except (ValidationError, OSError, UnicodeError) as exc:
        print(json.dumps({'status': 'blocked', 'error': str(exc)}, ensure_ascii=False), file=sys.stderr)
        return 2

if __name__ == '__main__':
    sys.exit(main())

```
