# AI 服务架构理解

## 核心组件

### 1. ProjectContext（项目上下文）

**代码位置**：[backend/services/ai_service.py:39-78](../../vendors/banana-slides/backend/services/ai_service.py#L39-L78)

**作用**：统一管理 AI 调用需要的所有项目信息

**属性**：
| 属性 | 类型 | 说明 |
|-----|------|------|
| `idea_prompt` | str | 用户输入的想法（idea 模式） |
| `outline_text` | str | 用户提供的大纲文本（outline 模式） |
| `description_text` | str | 用户提供的描述文本（descriptions 模式） |
| `creation_type` | str | 创作类型：'idea' / 'outline' / 'descriptions' |
| `outline_requirements` | str | 大纲生成要求 |
| `description_requirements` | str | 描述生成要求 |
| `reference_files_content` | List[Dict] | 参考文件内容列表 |

**设计特点**：
- 支持直接传入 Project 对象或字典
- 自动处理 reference_files_content
- 提供 `to_dict()` 方法方便传递

---

### 2. AIService（AI 服务）

**代码位置**：[backend/services/ai_service.py:81-1119](../../vendors/banana-slides/backend/services/ai_service.py#L81-L1119)

**作用**：处理所有 AI 模型交互

#### 2.1 初始化

**Provider 架构**：
```python
self.text_provider = text_provider or get_text_provider(model=self.text_model)
self.image_provider = image_provider or get_image_provider(model=self.image_model)
self.caption_provider = caption_provider or get_caption_provider(model=self.caption_model)
```

**支持的 AI Provider**：
- Gemini（Google）
- OpenAI（包括 Codex）
- Vertex AI
- LazyLLM（国内厂商聚合）

**配置来源**：
1. Flask app.config（Settings 覆盖）
2. Config 默认值
3. 环境变量

**推理配置**：
- `enable_text_reasoning`：启用文本推理
- `text_thinking_budget`：文本思考预算
- `enable_image_reasoning`：启用图像推理
- `image_thinking_budget`：图像思考预算

#### 2.2 核心方法分类

**大纲相关**：

| 方法 | 说明 | 代码位置 |
|-----|------|---------|
| `generate_outline()` | 生成 PPT 大纲（JSON） | [ai_service.py:328-342](../../vendors/banana-slides/backend/services/ai_service.py#L328-L342) |
| `generate_outline_stream()` | 流式生成大纲（Markdown） | [ai_service.py:391-553](../../vendors/banana-slides/backend/services/ai_service.py#L391-L553) |
| `parse_outline_text()` | 解析用户提供的大纲 | [ai_service.py:555-568](../../vendors/banana-slides/backend/services/ai_service.py#L555-L568) |
| `parse_description_to_outline()` | 从描述提取大纲 | [ai_service.py:1023-1035](../../vendors/banana-slides/backend/services/ai_service.py#L1023-L1035) |
| `refine_outline()` | 细化大纲 | [ai_service.py:1059-1083](../../vendors/banana-slides/backend/services/ai_service.py#L1059-L1083) |

**描述相关**：

| 方法 | 说明 | 代码位置 |
|-----|------|---------|
| `generate_page_description()` | 生成单个页面描述 | [ai_service.py:640-681](../../vendors/banana-slides/backend/services/ai_service.py#L640-L681) |
| `generate_descriptions_stream()` | 流式生成所有页面描述 | [ai_service.py:683-804](../../vendors/banana-slides/backend/services/ai_service.py#L683-L804) |
| `parse_description_to_page_descriptions()` | 切分描述到每页 | [ai_service.py:1037-1057](../../vendors/banana-slides/backend/services/ai_service.py#L1037-L1057) |
| `refine_descriptions()` | 细化描述 | [ai_service.py:1085-1117](../../vendors/banana-slides/backend/services/ai_service.py#L1085-L1117) |

**图片相关**：

| 方法 | 说明 | 代码位置 |
|-----|------|---------|
| `generate_image_prompt()` | 构建图片生成提示词 | [ai_service.py:827-875](../../vendors/banana-slides/backend/services/ai_service.py#L827-L875) |
| `generate_image()` | 生成图片 | [ai_service.py:877-995](../../vendors/banana-slides/backend/services/ai_service.py#L877-L995) |
| `edit_image()` | 编辑图片 | [ai_service.py:997-1021](../../vendors/banana-slides/backend/services/ai_service.py#L997-L1021) |

**内容提取相关**：

| 方法 | 说明 | 代码位置 |
|-----|------|---------|
| `extract_page_content()` | 从 markdown 提取页面内容 | [ai_service.py:1119-1137](../../vendors/banana-slides/backend/services/ai_service.py#L1119-L1137) |

#### 2.3 辅助方法

**图片处理**：
- `extract_image_urls_from_markdown()`：从 markdown 提取图片 URL
- `remove_markdown_images()`：移除 markdown 图片链接
- `download_image_from_url()`：下载图片

**格式转换**：
- `parse_markdown_outline()`：解析 markdown 大纲
- `flatten_outline()`：扁平化大纲（展开 part 结构）
- `generate_outline_text()`：生成大纲文本

**额外字段**：
- `_parse_extra_fields()`：解析额外字段
- `_get_extra_field_names()`：获取额外字段名
- `_build_extra_field_pattern()`：构建额外字段正则

---

## AI 调用流程

### 典型流程：大纲生成

```
用户输入想法
  ↓
创建 ProjectContext
  ↓
调用 AIService.generate_outline()
  ↓
调用 get_outline_generation_prompt() 构建提示词
  ↓
调用 text_provider.generate_json()
  ↓
解析 JSON 返回结果
  ↓
返回结构化大纲
```

### 典型流程：图片生成

```
页面描述
  ↓
调用 AIService.generate_image_prompt()
  ↓
调用 get_image_generation_prompt() 构建提示词
  ↓
处理参考图片（模板、素材）
  ↓
调用 image_provider.generate_image()
  ↓
保存图片并返回路径
```

---

## 数据流向

### 前端 → 后端 → AI Provider

```
前端 (React)
  ↓ API 调用
后端控制器
  ↓ 调用 AIService
AIService
  ↓ 构建 ProjectContext
提示词构建
  ↓ 调用 Provider
AI Provider（Gemini/OpenAI/LazyLLM）
  ↓ 返回结果
AIService
  ↓ 解析结果
后端控制器
  ↓ 返回响应
前端
```

---

## 关键设计模式

### 1. Provider 模式

**目的**：抽象不同的 AI Provider

**实现**：
```python
class TextProvider(ABC):
    @abstractmethod
    def generate_text(self, prompt: str) -> str:
        pass
    
    @abstractmethod
    def generate_json(self, prompt: str) -> Union[Dict, List]:
        pass
```

**支持的 Provider**：
- GeminiProvider
- OpenAIProvider
- VertexAIProvider
- LazyLLMProvider

### 2. Context 模式

**目的**：统一管理项目上下文

**实现**：
```python
class ProjectContext:
    def __init__(self, project_or_dict, reference_files_content=None):
        # 统一处理 Project 对象和字典
        pass
```

### 3. Stream 模式

**目的**：支持流式生成（SSE）

**实现**：
- `generate_outline_stream()`：生成器模式
- 使用 `<!-- PAGE_END -->` 标记分隔
- 前端使用 EventSource 接收

---

## 配置管理

### 模型配置

| 配置项 | 说明 | 默认值 |
|-------|------|-------|
| TEXT_MODEL | 文本生成模型 | gemini-2.5-pro |
| IMAGE_MODEL | 图像生成模型 | gemini-2.5-pro |
| IMAGE_CAPTION_MODEL | 图像识别模型 | gemini-2.5-flash |

### 推理配置

| 配置项 | 说明 | 默认值 |
|-------|------|-------|
| ENABLE_TEXT_REASONING | 启用文本推理 | False |
| TEXT_THINKING_BUDGET | 文本思考预算 | 1024 |
| ENABLE_IMAGE_REASONING | 启用图像推理 | False |
| IMAGE_THINKING_BUDGET | 图像思考预算 | 1024 |

### Provider 配置

| 配置项 | 说明 | 可选值 |
|-------|------|-------|
| AI_PROVIDER_FORMAT | AI Provider 格式 | gemini / openai / vertex / lazyllm |

---

## 错误处理

### 重试机制

使用 `tenacity` 库：

```python
@retry(
    stop=stop_after_attempt(3),
    retry=retry_if_exception_type(...)
)
def generate_image(...):
    pass
```

### 异常类型

- API 调用超时
- API 返回错误
- JSON 解析失败
- 图片下载失败

---

## 下一步

基于此理解，我们：

1. **已完成**：
   - [x] 提示词清单
   - [x] AI 服务架构理解

2. **待完成**：
   - [ ] TaskManager 理解
   - [ ] 控制器代码阅读
   - [ ] 前端 API 阅读
   - [ ] 数据模型阅读
   - [ ] 架构图绘制
   - [ ] 业务流程梳理
