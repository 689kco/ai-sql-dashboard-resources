# AI 写 SQL 与数据看板工具 · 资源整理

这个仓库整理了两类内容：

1. **使用 AI 编写 SQL 代码的技巧及优化方法**——怎么向 AI 提问才能得到正确、高效的 SQL，以及生成之后如何检查和优化；
2. **制作数据看板的相关工具**——主流开源 BI / 看板工具的 GitHub 仓库，附 Star 数和选型建议，点链接就能直达。

> 说明：Star 数为 2026-10-26 查询时的近似值，会随时间变化；所有链接均来自 GitHub 实际检索结果。

---

## 一、使用 AI 编写 SQL 的技巧

AI 写 SQL 准不准，主要取决于你给的信息够不够。下面 9 条是实践中公认有效的做法：

1. **先给表结构（Schema）**：把涉及的表名、字段名、字段类型、字段含义、表与表之间的关联关系（哪两个字段 JOIN）一起给 AI。AI 不知道你的库长什么样，给了结构准确率会有质的差别。
2. **说清 SQL 方言和版本**：例如 MySQL 8、PostgreSQL、Hive 3.1 等。不同方言的函数、日期处理、分页写法差别很大，不说清 AI 容易混用。
3. **写明口径定义**：指标怎么算、时间范围、去重规则、空值（NULL）怎么处理、状态码各代表什么。业务口径只有你知道，AI 猜的多半是错的。
4. **给 1~2 个示例（Few-shot）**：给 AI 一两个「问题 → 正确 SQL」的例子，它会照着你的风格和口径写，对复杂查询尤其有效。
5. **让它先复述再写**：先让 AI 用自己的话复述需求、列出拆解步骤，确认没理解错，再生成 SQL。复杂需求还可以拆成几个子问题分步生成。
6. **要求生成后自检**：让 AI 逐条检查——JOIN 的粒度会不会把行数放大（多对多关联）、聚合前后行数是否合理、有没有全表扫描、WHERE 条件有没有漏。
7. **用 EXPLAIN 验证和优化**：把数据库执行计划（EXPLAIN 的输出）贴回给 AI 解读，让它据此改写 SQL，比空对空说「帮我优化」有效得多。
8. **小步迭代**：先加 `LIMIT` 跑小样本看结果对不对；报错时把报错信息原样贴回给 AI 修，不要自己猜着改。
9. **注意安全**：不要把生产库的账号密码、真实客户数据发给 AI；只给表结构和脱敏后的样例数据。写操作（UPDATE / DELETE）让 AI 生成后必须人工确认再执行。

### SQL 优化常用检查清单

- 只查需要的列，避免 `SELECT *`
- 先过滤再关联：WHERE 尽量在 JOIN 之前缩小数据量
- 分区表必须带分区条件（Hive 等数仓场景尤其重要）
- JOIN 前确认两边粒度，警惕多对多关联导致数据膨胀；必要时先聚合再 JOIN
- 避免在 WHERE 里对字段套函数，可能让索引或分区裁剪失效
- 用 EXPLAIN 看执行计划：关注全表扫描、扫描行数、临时表和文件排序
- 能用窗口函数解决的排名/累计问题，不要写自连接
- 查询写法服从引擎特性，不要无脑堆很长的 CTE 链

### AI 写 SQL 的工具与项目（GitHub）

| 项目 | Star | 一句话介绍 |
| --- | --- | --- |
| [Chat2DB](https://github.com/OtterMind/Chat2DB) | 约 28k | AI 数据库客户端：连 40+ 种数据库，能生成、解释、优化 SQL，新手最友好 |
| [Vanna](https://github.com/vanna-ai/vanna) | 约 24k | 用 RAG 让大模型准确把自然语言转成 SQL（仓库已归档，代码仍可参考） |
| [DB-GPT](https://github.com/eosphoros-ai/DB-GPT) | 约 20k | 国产开源 AI 数据助手，围绕数据库的智能问答与分析 |
| [WrenAI](https://github.com/Canner/WrenAI) | 约 18k | 自然语言直接生成 SQL、图表和看板（GenBI），支持 20+ 数据源 |
| [Agentar-Scale-SQL](https://github.com/antgroup/Agentar-Scale-SQL) | 约 0.5k | 蚂蚁集团的 Text-to-SQL 框架，偏技术研究 |
| [SQL-GPT](https://github.com/CL-lau/SQL-GPT) | 约 0.3k | 用 ChatGPT 生成 SQL 并做优化和纠错的小工具 |

### SQL 优化工具与学习资源（GitHub）

| 项目 | Star | 一句话介绍 |
| --- | --- | --- |
| [SOAR](https://github.com/XiaoMi/soar) | 约 8.8k | 小米开源的 SQL 自动审核、优化与改写工具，给慢 SQL 提改进建议 |
| [Awesome-LLM-based-Text2SQL](https://github.com/DEEP-PolyU/Awesome-LLM-based-Text2SQL) | 约 1.4k | 大模型 Text-to-SQL 的论文、基准和开源项目精选清单 |
| [sql-ultimate-course](https://github.com/DataWithBaraa/sql-ultimate-course) | 约 1.6k | 从基础到进阶优化的完整 SQL 教程 |
| [Spider](https://github.com/taoyds/spider) | 约 1.1k | 耶鲁出品的经典 Text-to-SQL 基准数据集 |
| [mysql-tutorial](https://github.com/guobinhit/mysql-tutorial) | 约 0.5k | 中文 MySQL 教程，包含 SQL 优化章节 |

### 参考文章（网页）

- [How ChatGPT Writes SQL Queries from Natural-Language Prompts](https://www.datastudios.org/post/how-chatgpt-writes-sql-queries-from-natural-language-prompts)——给 AI 写 SQL 的提示词结构：角色、目标、约束、输出格式
- [How to get correct SQL from AI: give it the schema, the dialect, and EXPLAIN](https://dev.to/mars_peakul/how-to-get-correct-sql-from-ai-give-it-the-schema-the-dialect-and-explain-nfm)——「给结构、给方言、贴执行计划」的方法，附实用提示词合集介绍
- [Prompt Engineering for SQL: Bridging Natural Language & Code](https://emergys.com/blog/prompt-engineering-bridging-the-gap-between-natural-language-and-sql/)——SQL 提示工程的关键组成部分讲解

---

## 二、制作数据看板的相关工具（GitHub）

### 主流开源看板 / BI 工具

| 工具 | Star | 适合谁 |
| --- | --- | --- |
| [Grafana](https://github.com/grafana/grafana) | 约 77k | 监控和时序数据大屏最强，告警完善；做业务报表一般 |
| [Apache Superset](https://github.com/apache/superset) | 约 75k | 功能最强的开源 BI，图表丰富、可高度定制，但部署和学习门槛较高 |
| [Metabase](https://github.com/metabase/metabase) | 约 50k | 上手最快，拖拽提问，适合业务人员自助看数和中小团队 |
| [Redash](https://github.com/getredash/redash) | 约 29k | 轻量，写 SQL 出图表，适合分析师快速探索和分享 |
| [DataEase](https://github.com/dataease/dataease) | 约 25k | 国产开源 BI，中文界面友好，定位「人人可用」，拖拽做仪表板和大屏 |
| [WrenAI](https://github.com/Canner/WrenAI) | 约 18k | AI 驱动：说人话生成 SQL、图表和看板 |
| [积木报表 JimuReport](https://github.com/jeecgboot/jimureport) | 约 8.5k | 国产，类 Excel 做报表和大屏，支持对话式 AI 生成报表 |
| [Evidence](https://github.com/evidence-dev/evidence) | 约 7k | 用 SQL + Markdown 写看板，适合喜欢代码化、版本管理的人 |
| [Blazer](https://github.com/ankane/blazer) | 约 4.8k | 嵌在 Rails 应用里的轻量 BI |
| [Rill](https://github.com/rilldata/rill) | 约 3.0k | 基于 DuckDB 的快速 BI，适合本地文件和轻量分析 |

### 选型建议（简单版）

- **不懂技术、想快速出图**：Metabase 或 DataEase
- **有技术支持、要深度定制和几百张看板**：Apache Superset
- **运维监控、实时大屏**：Grafana
- **中国式复杂报表（多级表头、打印导出）**：积木报表 或 DataEase
- **想用说话的方式做看板**：WrenAI、Chat2DB（见第一部分）

### 更多清单

- [awesome-business-intelligence](https://github.com/thenaturalist/awesome-business-intelligence)（约 2.3k Star）——BI 工具持续更新的精选清单
- [Apache Superset vs Metabase vs Redash: Which Open-Source BI Tool to Self-Host in 2026?](https://blog.elest.io/apache-superset-vs-metabase-vs-redash-which-open-source-bi-tool-to-self-host-in-2026/)——三款主流开源 BI 的详细对比（英文）
- [Best database dashboard tools in 2026](https://www.basedash.com/blog/best-database-dashboard-tools-in-2026)——2026 年看板工具横向对比（英文）

---

## 仓库文件索引

- [表结构知识库：提炼提示词与文档格式指南](schema-knowledge-base-guide.md)——用 AI 从存量代码提炼表结构、字段含义和关联关系的方法，附提示词模板和知识库文档格式。
- [无人车货运行业数据分析方法](unmanned-freight-analytics.md)——无人车货运的指标体系、常用分析方法，以及商业分析、用户体验优化两类场景下的分析策略、优化策略和运营策略。

---

*整理：Muse 于 2026-10-26。内容为公开资源汇总，各项目的使用方式请以各自仓库的 README 为准。*
