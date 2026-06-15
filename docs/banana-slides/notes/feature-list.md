# Banana Slides 功能清单

## 核心创作流程

### 三种创作模式

| 模式 | 输入 | 输出 | 使用场景 |
|-----|------|------|---------|
| **想法模式**（idea） | 一句话描述 | 大纲 → 描述 → 图片 | 快速从零开始创作 |
| **大纲模式**（outline） | 结构化大纲文本 | 描述 → 图片 | 已有清晰大纲，只需生成内容 |
| **描述模式**（descriptions） | 逐页描述文本 | 大纲 + 描述 → 图片 | 已有详细内容设计 |

### 典型用户流程

```
1. 用户在首页选择创作方式
   ↓
2. 输入相应内容（想法/大纲/描述）
   ↓
3. 选择模板风格（可选）
   ↓
4. 选择图片比例（16:9 / 4:3）
   ↓
5. 点击"创建项目"
   ↓
6. 系统生成大纲（想法模式）
   ↓
7. 用户确认/修改大纲
   ↓
8. 系统生成页面描述
   ↓
9. 用户确认/修改描述
   ↓
10. 系统生成图片
   ↓
11. 用户可 Vibe 式修改图片
   ↓
12. 导出 PPTX/PDF/视频
```

---

## 功能模块清单

### 1. 项目管理

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 创建项目 | 选择创作方式，输入内容 | `POST /api/projects` |
| 查看项目 | 获取项目详情和页面列表 | `GET /api/projects/{id}` |
| 编辑项目 | 修改项目设置 | `PUT /api/projects/{id}` |
| 删除项目 | 删除项目和所有页面 | `DELETE /api/projects/{id}` |
| 项目列表 | 查看历史项目 | `GET /api/projects` |
| 上传模板 | 上传模板风格图片 | `POST /api/projects/{id}/template` |

---

### 2. 大纲功能

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 生成大纲 | AI 生成 PPT 大纲 | `POST /api/projects/{id}/generate/outline` |
| 流式生成大纲 | 实时流式生成（SSE） | `POST /api/projects/{id}/generate/outline/stream` |
| 细化大纲 | 自然语言修改大纲 | `POST /api/projects/{id}/refine/outline` |
| 更新页面大纲 | 手动编辑单页大纲 | `PUT /api/projects/{id}/pages/{page_id}/outline` |

---

### 3. 描述功能

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 批量生成描述 | 为所有页面生成详细描述 | `POST /api/projects/{id}/generate/descriptions` |
| 流式生成描述 | 实时流式生成（SSE） | `POST /api/projects/{id}/generate/descriptions/stream` |
| 生成单页描述 | 为单个页面生成描述 | `POST /api/projects/{id}/pages/{page_id}/generate/description` |
| 细化描述 | 自然语言修改描述 | `POST /api/projects/{id}/refine/descriptions` |
| 更新页面描述 | 手动编辑单页描述 | `PUT /api/projects/{id}/pages/{page_id}/description` |

---

### 4. 图片功能

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 批量生成图片 | 为所有页面生成图片 | `POST /api/projects/{id}/generate/images` |
| 生成单页图片 | 为单个页面生成图片 | `POST /api/projects/{id}/pages/{page_id}/generate/image` |
| 编辑图片（Vibe 式） | 自然语言修改图片 | `POST /api/projects/{id}/pages/{page_id}/edit/image` |
| 查看图片版本 | 查看历史版本 | `GET /api/projects/{id}/pages/{page_id}/image-versions` |
| 切换图片版本 | 回退到历史版本 | `POST /api/projects/{id}/pages/{page_id}/image-versions/{version_id}/set-current` |

**Vibe 式图片编辑特性**：
- 自然语言修改（"把这个图换成饼图"）
- 框选区域编辑
- 整页优化
- 素材替换

---

### 5. 文件解析

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 上传参考文件 | 上传 PDF/Docx/MD/Txt | `POST /api/reference-files/upload` |
| 解析文件 | 提取文件内容和图片 | `POST /api/reference-files/{id}/parse` |
| 关联到项目 | 将文件关联到项目 | `POST /api/reference-files/{id}/associate` |
| 查看项目文件 | 列出项目的参考文件 | `GET /api/reference-files/project/{project_id}` |
| 从项目移除 | 取消文件关联 | `POST /api/reference-files/{id}/dissociate` |

**支持的文件格式**：
- PDF：使用 MinerU 或 PyPDF2 解析
- Docx：使用 python-docx 解析
- Markdown：直接读取
- Txt：直接读取

---

### 6. 素材管理

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 上传素材 | 上传图片素材 | `POST /api/materials/upload` |
| 生成素材 | AI 生成素材图片 | `POST /api/projects/{id}/materials/generate` |
| 处理素材 | 编辑/擦除素材 | `POST /api/projects/{id}/materials/process` |
| 查看素材列表 | 获取所有素材 | `GET /api/materials` |
| 查看项目素材 | 获取项目的素材 | `GET /api/projects/{id}/materials` |
| 删除素材 | 删除素材 | `DELETE /api/materials/{id}` |
| 关联素材 | 将素材关联到项目 | `POST /api/materials/associate` |
| 获取素材描述 | AI 生成素材描述 | `GET /api/materials/{id}/caption` |

**素材处理操作**：
- `generate`：生成新素材
- `edit_full`：整页编辑
- `region_edit`：区域编辑
- `erase_region`：区域擦除

---

### 7. 导出功能

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 导出 PPTX | 导出标准 PPTX 文件 | `GET /api/projects/{id}/export/pptx` |
| 导出 PDF | 导出 PDF 文件 | `GET /api/projects/{id}/export/pdf` |
| 导出图片 | 导出页面图片（ZIP） | `GET /api/projects/{id}/export/images` |
| 导出可编辑 PPTX | 导出可编辑 PPTX（异步） | `POST /api/projects/{id}/export/editable-pptx` |
| 导出讲解视频 | 导出带旁白的视频（异步） | `POST /api/projects/{id}/export/video` |
| 查看导出文件 | 列出已导出的文件 | `GET /api/projects/{id}/exports` |

**PPTX 导出特性**：
- 页面切换动画（淡入淡出、翻页、平移等）
- 多种动画效果可随机应用
- 标准 16:9 比例

**可编辑 PPTX 特性**：
- 提取文字属性（颜色、字体）
- 清理背景
- 生成可编辑的文本框和背景

**视频导出特性**：
- AI 生成旁白
- 多语言、多音色
- 多种表达风格
- 字幕和旁白同步

---

### 8. PPT 翻新

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 创建翻新项目 | 上传 PDF/PPTX 创建项目 | `POST /api/projects/renovation` |
| 重新生成翻新页面 | 重新解析单页 | `POST /api/projects/{id}/pages/{page_id}/regenerate-renovation` |

**翻新流程**：
1. 上传现有 PPT（PDF/PPTX）
2. 系统解析内容
3. 生成大纲和描述
4. 基于原设计重新生成图片
5. 导出新 PPT

---

### 9. 旁白功能

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 生成单页旁白 | AI 生成页面讲解 | `POST /api/projects/{id}/pages/{page_id}/generate/narration` |
| 批量生成旁白 | 为所有页面生成旁白 | `POST /api/projects/{id}/generate/narrations` |
| 更新旁白 | 手动编辑旁白 | `PUT /api/projects/{id}/pages/{page_id}/narration` |

**旁白配置**：
- 演讲者人设
- 目标听众
- 语调风格
- 演讲主题
- 字数范围

---

### 10. 系统设置

| 功能 | 说明 | API 端点 |
|-----|------|---------|
| 获取设置 | 查看系统配置 | `GET /api/settings` |
| 更新设置 | 修改 API Key、模型等 | `PUT /api/settings` |
| 重置设置 | 恢复默认设置 | `POST /api/settings/reset` |
| 验证 API Key | 测试 API Key 是否可用 | `POST /api/settings/verify` |
| 检查更新 | 检查项目更新 | `GET /api/settings/check-update` |
| OpenAI OAuth | OpenAI 官方 OAuth 绑定 | `/api/settings/openai-oauth/*` |

**配置项**：
- AI Provider 格式（gemini/openai/vertex/lazyllm）
- API Key
- 模型选择
- 并发限制
- 输出语言
- 额外字段配置

---

## 功能优先级

### 高优先级（核心功能）

- ✅ 项目创建和管理
- ✅ 大纲生成（普通 + 流式）
- ✅ 描述生成（普通 + 流式）
- ✅ 图片生成（批量 + 单页）
- ✅ 图片编辑（Vibe 式）
- ✅ 导出 PPTX/PDF

### 中优先级（重要功能）

- 文件解析
- 素材管理
- 可编辑 PPTX 导出
- 旁白生成
- 视频导出

### 低优先级（高级功能）

- PPT 翻新
- OpenAI OAuth
- 用户模板管理

---

## 用户场景

### 场景 1：快速创作

```
用户需求：明天要汇报，还没准备 PPT
操作流程：
1. 选择"想法模式"
2. 输入："生成一个关于人工智能的 PPT"
3. 点击"创建项目"
4. 自动生成大纲
5. 自动生成描述
6. 自动生成图片
7. 导出 PPTX
时间：5-10 分钟
```

### 场景 2：精准控制

```
用户需求：已有详细设计，只需执行
操作流程：
1. 选择"描述模式"
2. 粘贴逐页描述文本
3. 点击"创建项目"
4. 自动解析大纲和描述
5. 生成图片
6. 微调（如有需要）
7. 导出
时间：3-5 分钟
```

### 场景 3：迭代优化

```
用户需求：对生成结果不满意，需要修改
操作流程：
1. 已有项目和图片
2. 自然语言输入："把第三页的图表换成饼图"
3. 系统自动编辑图片
4. 查看历史版本，如不满意可回退
5. 继续修改直到满意
时间：每次修改 1-2 分钟
```

---

## 技术亮点

### 1. 流式生成（SSE）
- 实时显示生成进度
- 提升用户体验
- 降低等待焦虑

### 2. Vibe 式交互
- 自然语言修改
- 无需学习复杂操作
- 智能理解用户意图

### 3. 图片版本管理
- 保存每次修改
- 支持版本回退
- 对比不同版本

### 4. 多 Provider 支持
- Gemini（Google）
- OpenAI
- Vertex AI
- LazyLLM（国内厂商）

### 5. 异步任务
- 长时间任务异步执行
- 任务状态实时查询
- 避免请求超时

---

## 下一步

基于此清单，我们将：

1. **已完成**：
   - [x] 提示词清单
   - [x] AI 服务架构
   - [x] 任务管理系统
   - [x] 控制器概览
   - [x] 前端 API 和数据模型
   - [x] 功能清单和业务流程

2. **最后一步**：
   - [ ] 绘制架构图

完成后即可进入阶段二：撰写功能流程文档！
