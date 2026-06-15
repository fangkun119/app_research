# Banana Slides 提示词清单

本文档列出了 `backend/services/prompts.py` 中的所有提示词函数，按功能域分类。

---

## 1. 共享工具与常量

### 常量配置
| 常量名 | 用途 | 代码位置 |
|-------|------|---------|
| `LANGUAGE_CONFIG` | 多语言配置（中英日） | [prompts.py:31-52](../../vendors/banana-slides/backend/services/prompts.py#L31-L52) |
| `DETAIL_LEVEL_SPECS` | 描述细致程度配置 | [prompts.py:54-58](../../vendors/banana-slides/backend/services/prompts.py#L54-L58) |
| `DEFAULT_NARRATION_CONFIG` | 默认旁白生成配置 | [prompts.py:60-67](../../vendors/banana-slides/backend/services/prompts.py#L60-L67) |
| `_OUTLINE_JSON_FORMAT` | 大纲 JSON 格式规范 | [prompts.py:72-92](../../vendors/banana-slides/backend/services/prompts.py#L72-L92) |

### 辅助函数
| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `_build_prompt()` | 构建提示词，添加参考文件 XML | [prompts.py:97-103](../../vendors/banana-slides/backend/services/prompts.py#L97-L103) |
| `_get_original_input()` | 提取用户原始输入 | [prompts.py:106-114](../../vendors/banana-slides/backend/services/prompts.py#L106-L114) |
| `_get_original_input_labeled()` | 提取带标签的原始输入（用于细化） | [prompts.py:117-128](../../vendors/banana-slides/backend/services/prompts.py#L117-L128) |
| `_get_previous_requirements_text()` | 格式化历史修改要求 | [prompts.py:131-136](../../vendors/banana-slides/backend/services/prompts.py#L131-L136) |
| `_normalize_word_count()` | 归一化旁白字数 | [prompts.py:139-145](../../vendors/banana-slides/backend/services/prompts.py#L139-L145) |
| `_format_extra_field_instructions()` | 格式化额外字段指令 | [prompts.py:200-205](../../vendors/banana-slides/backend/services/prompts.py#L200-L205) |
| `_format_reference_files_xml()` | 格式化参考文件为 XML | [prompts.py:208-223](../../vendors/banana-slides/backend/services/prompts.py#L208-L223) |
| `_format_requirements()` | 格式化用户生成要求 | [prompts.py:226-252](../../vendors/banana-slides/backend/services/prompts.py#L226-L252) |

### 语言配置函数
| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_default_output_language()` | 获取默认输出语言 | [prompts.py:255-258](../../vendors/banana-slides/backend/services/prompts.py#L255-L258) |
| `get_language_instruction()` | 获取语言限制指令 | [prompts.py:261-265](../../vendors/banana-slides/backend/services/prompts.py#L261-L265) |
| `get_ppt_language_instruction()` | 获取 PPT 文字语言指令 | [prompts.py:268-272](../../vendors/banana-slides/backend/services/prompts.py#L268-L272) |

### 旁白配置函数
| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_default_narration_generation_config()` | 获取默认旁白配置 | [prompts.py:148-154](../../vendors/banana-slides/backend/services/prompts.py#L148-L154) |
| `normalize_narration_generation_config()` | 归一化旁白生成配置 | [prompts.py:157-178](../../vendors/banana-slides/backend/services/prompts.py#L157-L178) |
| `parse_narration_generation_result()` | 解析批量旁白输出 | [prompts.py:181-197](../../vendors/banana-slides/backend/services/prompts.py#L181-L197) |

---

## 2. 大纲 Prompts（Outline Prompts）

### 2.1 大纲生成

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_outline_generation_prompt()` | JSON | 根据用户想法生成 PPT 大纲 | [prompts.py:280-299](../../vendors/banana-slides/backend/services/prompts.py#L280-L299) |
| `get_outline_generation_prompt_markdown()` | Markdown | 流式生成大纲（SSE） | [prompts.py:302-349](../../vendors/banana-slides/backend/services/prompts.py#L302-L349) |

**说明：**
- JSON 版本用于传统 API 调用
- Markdown 版本用于流式生成（Server-Sent Events）

### 2.2 大纲解析

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_outline_parsing_prompt()` | JSON | 解析用户提供的大纲文本 | [prompts.py:352-383](../../vendors/banana-slides/backend/services/prompts.py#L352-L383) |
| `get_outline_parsing_prompt_markdown()` | Markdown | 流式解析大纲 | [prompts.py:386-410](../../vendors/banana-slides/backend/services/prompts.py#L386-L410) |

**说明：**
- 用于将用户提供的非结构化大纲转换为结构化格式
- 不修改原文内容，只做结构化整理

### 2.3 描述转大纲

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_description_to_outline_prompt()` | JSON | 从描述文本提取大纲结构 | [prompts.py:413-445](../../vendors/banana-slides/backend/services/prompts.py#L413-L445) |
| `get_description_to_outline_prompt_markdown()` | Markdown | 流式生成大纲和描述 | [prompts.py:448-508](../../vendors/banana-slides/backend/services/prompts.py#L448-L508) |

**说明：**
- 用于"从描述"创作模式
- Markdown 版本同时输出大纲和页面描述

### 2.4 大纲细化

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_outline_refinement_prompt()` | JSON | 根据用户要求修改大纲 | [prompts.py:511-568](../../vendors/banana-slides/backend/services/prompts.py#L511-L568) |

**说明：**
- 用于 Vibe 式自然语言修改大纲
- 支持历史修改上下文

---

## 3. 描述 Prompts（Description Prompts）

### 3.1 单页描述生成

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_page_description_prompt()` | 生成单个页面的详细描述 | [prompts.py:576-612](../../vendors/banana-slides/backend/services/prompts.py#L576-L612) |

**说明：**
- 基于大纲逐页生成详细描述
- 支持细致程度配置（concise/default/detailed）
- 支持额外字段

### 3.2 批量描述生成

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_all_descriptions_stream_prompt()` | Markdown（流式） | 一次性生成所有页面描述 | [prompts.py:615-677](../../vendors/banana-slides/backend/services/prompts.py#L615-L677) |

**说明：**
- 用于流式生成所有页面描述
- 使用 `<!-- PAGE_END -->` 标记分隔页面

### 3.3 描述拆分

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_description_split_prompt()` | JSON | 从完整描述文本切分出每页描述 | [prompts.py:680-733](../../vendors/banana-slides/backend/services/prompts.py#L680-L733) |

**说明：**
- 用于"从描述"模式的后处理
- 根据大纲结构切分完整描述

### 3.4 描述细化

| 函数名 | 输出格式 | 用途 | 代码位置 |
|-------|---------|------|---------|
| `get_descriptions_refinement_prompt()` | JSON | 根据用户要求修改页面描述 | [prompts.py:736-807](../../vendors/banana-slides/backend/services/prompts.py#L736-L807) |

**说明：**
- 用于 Vibe 式自然语言修改描述
- 支持批量修改所有页面

---

## 4. 图片生成 Prompts（Image Generation Prompts）

### 4.1 文生图

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_image_generation_prompt()` | 根据页面描述生成 PPT 页面图片 | [prompts.py:815-860](../../vendors/banana-slides/backend/services/prompts.py#L815-L860) |

**说明：**
- 核心的图片生成提示词
- 支持模板风格参考
- 支持额外素材图片
- 封面页特殊处理

### 4.2 图片编辑

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_image_edit_prompt()` | Vibe 式图片编辑（自然语言修改） | [prompts.py:863-881](../../vendors/banana-slides/backend/services/prompts.py#L863-L881) |

**说明：**
- 用于自然语言修改图片
- 支持框选区域编辑
- 智能判断用户意图

---

## 5. 图片处理 Prompts（Image Processing Prompts）

### 5.1 背景清理

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_clean_background_prompt()` | 去除图片中的文字和图表，生成纯净底板 | [prompts.py:889-925](../../vendors/banana-slides/backend/services/prompts.py#L889-L925) |

**说明：**
- 用于可编辑 PPTX 导出
- 支持 bbox 指定需要移除的区域

### 5.2 画质修复

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_quality_enhancement_prompt()` | 修复图像抹除痕迹，提升画质 | [prompts.py:928-971](../../vendors/banana-slides/backend/services/prompts.py#L928-L971) |

**说明：**
- 用于可编辑 PPTX 导出
- 修复百度图像修复留下的痕迹

---

## 6. 内容提取 Prompts（Content Extraction Prompts）

### 6.1 文字属性提取

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_text_attribute_extraction_prompt()` | 提取文字内容、颜色、公式等信息 | [prompts.py:979-1020](../../vendors/banana-slides/backend/services/prompts.py#L979-L1020) |
| `get_batch_text_attribute_extraction_prompt()` | 批量提取多个文本元素的样式属性 | [prompts.py:1023-1079](../../vendors/banana-slides/backend/services/prompts.py#L1023-L1079) |

**说明：**
- 用于可编辑 PPTX 导出
- 提取文字的颜色、字体、对齐方式等属性
- 支持 LaTeX 公式识别

### 6.2 页面内容提取

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_ppt_page_content_extraction_prompt()` | 从 markdown 提取结构化页面内容 | [prompts.py:1082-1121](../../vendors/banana-slides/backend/services/prompts.py#L1082-L1121) |

**说明：**
- 用于 PPT 翻新功能
- 从解析的文档中提取 title、points、description

### 6.3 排版分析

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_layout_caption_prompt()` | 描述 PPT 页面的排版布局 | [prompts.py:1124-1146](../../vendors/banana-slides/backend/services/prompts.py#L1124-L1146) |

**说明：**
- 用于 PPT 翻新功能
- 分析页面的整体布局、文本位置、视觉元素

### 6.4 风格提取

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_style_extraction_prompt()` | 从图片中提取风格描述 | [prompts.py:1149-1168](../../vendors/banana-slides/backend/services/prompts.py#L1149-L1168) |

**说明：**
- 通用风格提取功能
- 可用于所有创作模式
- 提取配色、字体、设计元素、整体风格

---

## 7. 旁白 Prompts（Narration Prompts）

### 7.1 旁白生成

| 函数名 | 用途 | 代码位置 |
|-------|------|---------|
| `get_narration_generation_prompt()` | 生成所有页面的讲解旁白 | [prompts.py:1176-1245](../../vendors/banana-slides/backend/services/prompts.py#L1176-L1245) |

**说明：**
- 用于视频导出功能
- 支持配置演讲者人设、听众、语调、主题
- 支持中英日多语言
- 支持自定义字数范围

---

## 统计信息

| 功能域 | 提示词数量 | 核心提示词 |
|-------|-----------|-----------|
| 共享工具与常量 | 13 个函数（非提示词） | 语言配置、辅助函数 |
| 大纲 Prompts | 7 个 | `get_outline_generation_prompt` |
| 描述 Prompts | 4 个 | `get_page_description_prompt` |
| 图片生成 Prompts | 2 个 | `get_image_generation_prompt` |
| 图片处理 Prompts | 2 个 | `get_clean_background_prompt` |
| 内容提取 Prompts | 5 个 | `get_text_attribute_extraction_prompt` |
| 旁白 Prompts | 1 个 | `get_narration_generation_prompt` |
| **总计** | **34 个函数** | - |

---

## 提示词分类概览

```
Banana Slides 提示词体系
│
├── 创作流程提示词
│   ├── 大纲生成：2 个（JSON + Markdown）
│   ├── 大纲解析：2 个（JSON + Markdown）
│   ├── 描述转大纲：2 个（JSON + Markdown）
│   ├── 描述生成：2 个（单页 + 批量流式）
│   ├── 描述拆分：1 个
│   └── 图片生成：1 个
│
├── 编辑流程提示词
│   ├── 大纲细化：1 个
│   ├── 描述细化：1 个
│   └── 图片编辑：1 个
│
├── 高级功能提示词
│   ├── 背景清理：1 个
│   ├── 画质修复：1 个
│   ├── 文字属性提取：2 个
│   ├── 页面内容提取：1 个
│   ├── 排版分析：1 个
│   ├── 风格提取：1 个
│   └── 旁白生成：1 个
│
└── 辅助函数（13 个）
    ├── 语言配置：3 个
    ├── 旁白配置：3 个
    └── 通用辅助：7 个
```

---

## 下一步

基于此清单，我们将继续：

1. **阶段一剩余任务**：
   - [ ] 阅读 `ai_service.py` 理解 AI 调用逻辑
   - [ ] 阅读 `task_manager.py` 理解任务管理
   - [ ] 阅读控制器代码
   - [ ] 绘制架构图
   - [ ] 梳理业务流程

2. **阶段三撰写时**：
   - 按此清单逐一撰写每个提示词的详细文档
   - 为每个提示词补充设计思路、示例和使用说明
