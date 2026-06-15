# Banana Slides 系统文档

> Banana Slides 是一个使用 AI 自动生成 PPT 的开源工具——输入一句话想法、一份大纲，或逐页描述，即可生成设计好的幻灯片，并支持自然语言（Vibe 式）修改与多格式导出。

本套文档旨在帮助 **开发者、AI 研究者、项目维护者** 深入理解 Banana Slides 的工作原理与提示词工程。源码位于 [`vendors/banana-slides/`](../../vendors/banana-slides/)。

---

## 📚 文档总览

```
docs/banana-slides/
├── index.md                      ← 你在这里（文档索引与导航）
├── 01-functional-flows.md        ← 第一部分：功能执行流程
├── 02-prompts-reference.md       ← 第二部分：提示词工程参考
├── diagrams/
│   └── architecture-diagrams.md  ← 10 张 Mermaid 架构图
└── notes/                        ← 阶段一资料收集笔记（7 份）
    ├── prompt-inventory.md          提示词清单
    ├── ai-service-architecture.md   AI 服务架构
    ├── task-manager-architecture.md 任务管理系统
    ├── controllers-overview.md      控制器概览
    ├── frontend-api-and-models.md   前端 API 与数据模型
    ├── feature-list.md              功能清单
    └── stage-one-summary.md         阶段一总结
```

---

## 📖 两份主文档

### [01-functional-flows.md](01-functional-flows.md) — 功能执行流程

回答**「系统是怎么工作的」**：以用户操作为主线，逐步展开从前端 UI → 后端服务 → AI 模型的完整数据流。

| 章节 | 内容 |
|-----|------|
| [1. 项目概述](01-functional-flows.md#1-项目概述) | 技术架构总览、核心模块、数据流向 |
| [2. 核心功能流程](01-functional-flows.md#2-核心功能流程) | 10 个功能流程（项目创建、大纲/描述/图片生成、图片编辑、文件解析、导出、素材、PPT 翻新、旁白） |
| [3. 跨模块机制](01-functional-flows.md#3-跨模块机制) | 任务管理、错误处理、并发控制、参考文件、Provider 抽象 |

每个流程都含：用户操作 / 系统处理 / Mermaid 数据流图 / 代码定位 / 提示词引用 / 输入输出 / 异常处理。

### [02-prompts-reference.md](02-prompts-reference.md) — 提示词工程参考

回答**「AI 提示词是怎么设计的」**：按功能域分类整理全部 35 个提示词函数。

| 章节 | 内容 |
|-----|------|
| [1. 提示词总览](02-prompts-reference.md#1-提示词总览) | 分类体系、共享组件与常量、语言配置 |
| [2. 大纲提示词](02-prompts-reference.md#2-大纲提示词outline-prompts) | 生成、解析、描述转大纲、细化（7 函数） |
| [3. 描述提示词](02-prompts-reference.md#3-描述提示词description-prompts) | 单页、批量、拆分、细化（4 函数） |
| [4. 图片提示词](02-prompts-reference.md#4-图片提示词image-prompts) | 文生图、编辑、背景清理、画质修复（4 函数） |
| [5. 内容提取提示词](02-prompts-reference.md#5-内容提取提示词content-extraction-prompts) | 文字属性、页面内容、排版、风格（5 函数） |
| [6. 旁白提示词](02-prompts-reference.md#6-旁白提示词narration-prompts) | 旁白生成 + 配置工具 |
| [附录 A](02-prompts-reference.md#附录-a-提示词函数索引) | 函数索引（按字母） / 模板变量 / 版本历史 |

每个提示词都含：元信息 / 完整模板代码 / 变量说明 / 示例调用 / 设计思路 / 相关提示词。

---

## 🎯 按阅读目的选择入口

| 你的目标 | 推荐入口 |
|---------|---------|
| 快速了解系统全貌 | [01 § 1 项目概述](01-functional-flows.md#1-项目概述) |
| 理解某个功能如何实现 | [01 § 2 核心功能流程](01-functional-flows.md#2-核心功能流程)（按功能名跳转） |
| 学习提示词工程 | [02 § 1 提示词总览](02-prompts-reference.md#1-提示词总览) → 挑感兴趣的章节深读 |
| 修改某个具体提示词 | [02 附录 A 函数索引](02-prompts-reference.md#附录-a-提示词函数索引)（按函数名定位） |
| 理解异步任务与并发控制 | [01 § 3 跨模块机制](01-functional-flows.md#3-跨模块机制) |
| 看架构图建立直觉 | [diagrams/architecture-diagrams.md](diagrams/architecture-diagrams.md) |

### 按读者角色

- **🔧 二次开发者**：先读 [01 项目概述](01-functional-flows.md#1-项目概述) 建立全局观 → 用 [02 附录 A](02-prompts-reference.md#附录-a-提示词函数索引) 定位要改的代码 → 按 [01 功能流程](01-functional-flows.md#2-核心功能流程) 追踪数据流。
- **🔬 AI 研究者**：直接精读 [02 提示词参考](02-prompts-reference.md)，重点关注每个提示词的「设计思路」与「设计考量」小节。
- **🐛 项目维护者**：用 [01 跨模块机制](01-functional-flows.md#3-跨模块机制) 理解任务/并发/错误处理 → 查 [notes/](notes/) 的架构笔记快速定位问题。

---

## 🗝️ 关键概念速查

| 术语 | 含义 | 详见 |
|-----|------|------|
| **creation_type** | 创作类型：`idea` / `outline` / `descriptions` | [01 § 2.1](01-functional-flows.md#21-项目创建流程) |
| **ProjectContext** | 统一管理 AI 调用上下文的数据结构 | [notes/ai-service-architecture.md](notes/ai-service-architecture.md) |
| **AIService** | 封装所有 AI 模型交互的服务层 | [01 § 3.5](01-functional-flows.md#35-ai-provider-抽象层) |
| **TaskManager** | 基于 ThreadPoolExecutor 的轻量异步任务管理 | [01 § 3.1](01-functional-flows.md#31-任务管理系统) |
| **ResourceLimiter** | 线程安全的并发限制器（threading.Condition） | [01 § 3.3](01-functional-flows.md#33-资源限制与并发控制) |
| **SSE 流式生成** | Server-Sent Events，实时返回生成进度 | [01 § 2.2](01-functional-flows.md#22-大纲生成流程) |
| **Vibe 式编辑** | 自然语言修改图片（框选区域 + 指令） | [01 § 2.5](01-functional-flows.md#25-图片编辑流程vibe-式修改) |
| **reference_files** | 上传的参考文件，注入提示词提供上下文 | [01 § 3.4](01-functional-flows.md#34-参考文件系统) |

---

## 📊 文档规模

| 指标 | 数值 |
|-----|------|
| 主文档总行数 | ~8,500 行 |
| 提示词函数 | 35 个（7 大功能域） |
| 功能流程 | 10 个核心流程 + 5 个跨模块机制 |
| 架构图 | 10 张 Mermaid 图 |
| 代码引用 | 200+ 处可点击跳转（相对路径） |

---

## 🔗 外部资源

- [Banana Slides 源码](../../vendors/banana-slides/)（本仓库 submodule）
- [Banana Slides README](../../vendors/banana-slides/README.md)
- [Banana Slides 官方文档](https://docs.bananaslides.online/)

---

## 📐 文档撰写规范

本套文档遵循 [`specs/banana-slides/spec.md`](../../specs/banana-slides/spec.md) 定义的需求规格，按 [`specs/banana-slides/plan.md`](../../specs/banana-slides/plan.md) 的四阶段计划撰写：

1. ✅ **阶段一**：资料收集与分析（7 份笔记 + 架构图）
2. ✅ **阶段二**：功能流程文档（`01-functional-flows.md`）
3. ✅ **阶段三**：提示词参考文档（`02-prompts-reference.md`，多智能体并行撰写 + 对抗式验证）
4. ✅ **阶段四**：审核与优化（代码引用核验、交叉引用修正、本索引页）

> **质量保证**：两份主文档均经过源码核验——`02` 由独立验证智能体逐条对照源码修正提示词模板；`01` 经 5 组并行验证修正了 21 处代码引用偏差（含源码重构导致的行号偏移与虚构的前端文件路径）。

---

**文档版本**：1.0 ｜ **最后更新**：2025-06-15 ｜ **状态**：✅ 全部完成
