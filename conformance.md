# GB/T 48000.3—2026 对齐自声明

> 状态：**v1.0（2026-07-18 发布）**——随 OPF 版本滚动更新，版本号与本体 owl:versionInfo 一致。
> 性质：**自声明（self-declared alignment）**。本文档未经任何认证机构、检测机构或标准化
> 组织评估，不构成认证、认可或合格评定结论。GB/T 48000.3—2026 标准正文版权归其发布
> 机构所有，本文档仅引用条款编号与要求要点，不复制条款原文。

## 1. 声明对象

| 项 | 值 |
|---|---|
| 声明主体 | O'Process 项目（Tim Ou） |
| 对齐标准 | GB/T 48000.3—2026《标准数字化 第 3 部分：本体建模要求》（2026-08-01 实施） |
| 声明范围 | OPF v2.0.0 语义导出物（见 §2），不含 MCP Server 运行时行为 |
| 本体 IRI | `https://oprocess.net/ns/opf` |
| 上线日期 | 2026-07-18（对齐 GB/T 48000.3 实施日 2026-08-01 正式对外发布） |

## 2. 覆盖的导出物

| 文件 | 内容 | 发布地址 |
|------|------|--------------|
| `opf.ttl` | OPF-O 本体：3 实体类型、9 数据属性、5 对象属性、公理 | `/ns/opf` |
| `opf-shapes.ttl` | SHACL 约束（第 8 章公理的可执行化） | `/ns/opf-shapes` |
| `context.jsonld` | JSON-LD 1.1 上下文（framework.json 键 → RDF 词汇） | `/ns/opf.jsonld` |
| `opf-nodes.ttl` | SKOS 概念方案 + 2436 个流程节点，双语标签 | `/opf` 数据转储 |

任何第三方可复跑校验：`uv run python -m scripts.validate_semantic`
（SHACL 全量校验 + 附录 A 元数据自检）。

## 3. 条款对齐映射表

| 条款 | 要求要点 | OPF 实现 | 证据 |
|------|---------|----------|------|
| §5.1 基本要求 | 实体定义面向跨领域、跨系统共享，避免局部语境依赖 | 复用 W3C 通用词汇（SKOS / DCTERMS / OWL）；`oprocess://` 内部别名不出现在任何导出物中 | `opf.ttl`、`scripts/shared/semantic.py` |
| §5.2 本体核心组成 | 实体类型 + 数据/对象属性 + 公理 | 3 实体类型（ProcessNode/KPI/Role）、9 数据属性、5 对象属性、不相交/功能性/非自反公理 | `opf.ttl` |
| §5.3 形式化要求 | 应采用 W3C 本体描述语言；应以 Turtle、JSON-LD 等序列化；应支持 SHACL 校验 | OWL/RDFS 建模；Turtle 与 JSON-LD 双序列化；SHACL shapes + 一键校验命令；2436 节点全量校验通过（附时间戳报告 `semantic/validation-report.txt`，可复跑） | `opf-shapes.ttl`、`scripts/validate_semantic.py`、`semantic/validation-report.txt` |
| §5.4 命名方式 | IRI = 命名空间 + 本地标识符，全球唯一 | IRI 政策集中于单一模块；实例 IRI 不带版本段，跨版本 ID 重映射由 `id_aliases.json` 承载 | `scripts/shared/semantic.py`、`semantic/README.md` |
| §6.1.2 复用原则 | 现有本体已有的实体类型应直接采用 | 直接采用 skos:Concept / skos:ConceptScheme / dcterms 词汇；opf:ProcessNode 声明为 skos:Concept 子类而非重新定义 | `opf.ttl` |
| 第 8 章 公理与规则 | 应定义不相交、唯一标识、枚举值、功能性属性、层次结构等规则 | owl:AllDisjointClasses（三实体类型互斥）；skos:notation 每节点恰一 **+ SPARQL 全局唯一性约束**（全局唯一标识，已可执行校验）；opf:domain 二值枚举；opf:level / opf:domain 功能性属性；顶层概念 XOR 单一父节点（层次结构约束）；opf:precedes 声明为非自反并配 SHACL 自反守卫（当前无 precedes 边，边层数据发布后对实例生效） | `opf.ttl` 公理段、`opf-shapes.ttl` |
| 第 9 章 扩展方式 | 自有命名空间、模块化扩展、不得修改核心定义 | 全部扩展词汇收敛于 `opf:` 命名空间；SKOS/DC 核心定义零改动。`opf:precedes` **不属于** GB/T 48000.3 正式版核心对象属性，仅作为 OPF 在第 9 章原则下的自有命名空间扩展声明（非核心属性对齐） | `opf.ttl` |
| 附录 A（规范性） | 实体八要素 / 属性七要素元数据描述 | 脚本自动自检附录 A 的**部分**元数据项：双语 rdfs:label、定义（rdfs:comment）、rdfs:isDefinedBy，及属性的 Domain/Range，当前 0 缺口；Name、父/子类、等价类等其余要素尚未由脚本完整核验 | `scripts/validate_semantic.py` 输出 |
| 附录 D（资料性） | Turtle 实例化风格参照 | @prefix 声明、双语标签、实例命名风格对照附录 D 样例 | `opf-nodes.ttl` |

## 4. 边界声明（不声明对齐的内容）

- **第 6 章核心实体类型与第 7 章表 1 的 34 个核心对象属性**面向"标准文献"的数字化
  建模场景；OPF 是流程分类领域本体而非标准文献本体，因此不声明实现该实体/属性全集，
  而是按第 9 章扩展方式在 `opf:` 命名空间内定义领域实体与属性，不修改任何核心定义。
- **多语言标签**：约 554 个节点名称、834 个节点描述的中文值与英文相同，按附录 A 判定
  为占位符并在导出时剔除（英文不冒充中文）；源头修复在翻译管线任务中进行。此外，导出中
  仍保留部分中英混杂的 zh 标签/定义；这仅表示未用英文冒充中文，**不代表中文本地化已完成
  或质量已达标**。
- **KPI 与岗位实例**：类与属性已在本体中声明，实例数据随后续边层导出（P1）发布。
- **溯源词汇**：采用 W3C PROV（国标未规定溯源词汇），属兼容性补充，进行中。
- 本声明不涉及 GB/T 48000.2 能力等级评估与 GB/T 48000.4 协同制定要求。

## 5. 许可与知识产权

许可方案已定（**选项 A**，2026-07-18）：

- **本体与约束文件**（`opf.ttl` / `opf-shapes.ttl` / `context.jsonld`）采用
  [**CC BY 4.0**](https://creativecommons.org/licenses/by/4.0/)。
- **节点数据转储**（`opf-nodes.ttl`）当前**仅供评估用途（evaluation use only）**；
  正式许可待源框架（APQC PCF / ITIL / SCOR）IP 边界确认后单独声明。
- **不发布** PCF / ITIL / SCOR 原文数据集，也**不主张**对第三方框架名称、原始层级或其
  受保护表达享有权利。当前公开许可仅明确适用于 `opf.ttl` / `opf-shapes.ttl` /
  `context.jsonld`（CC BY 4.0）；`opf-nodes.ttl` 整体仅供评估用途，其中 AI 语境标注与
  自建映射断言是否可单独开放，待 IP 边界确认后另行声明。项目代码遵循仓库 MIT 许可与
  第三方署名（见 README Third-Party Attribution）。

## 6. 有效性与维护

本声明随 OPF 版本演进滚动更新，版本号与本体 owl:versionInfo 保持一致。
校验命令可由任何第三方在仓库内复跑（见 §2）。

联系方式：https://oprocess.net
