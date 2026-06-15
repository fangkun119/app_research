# 任务管理系统架构

## 核心组件

### 1. ResourceLimiter（资源限制器）

**代码位置**：[backend/services/task_manager.py:52-95](../../vendors/banana-slides/backend/services/task_manager.py#L52-L95)

**作用**：线程安全的并发限制器，控制外部资源的并发访问

**设计特点**：
- 使用 `threading.Condition` 实现线程同步
- 支持 `@contextmanager` 的 `slot()` 方法
- 等待机制：超过容量时等待，直到有可用 slot
- 线程安全：使用 `with self._condition` 保护共享状态

**使用场景**：
- 限制 AI API 并发调用
- 限制图片生成并发数
- 限制描述生成并发数

**示例**：
```python
# 创建限制器（容量为 5）
limiter = ResourceLimiter("AI-Text", 5)

# 使用限制器
with limiter.slot("generate_outline"):
    # 执行需要限制的代码
    ai_service.generate_outline(...)
```

---

### 2. TaskManager（任务管理器）

**代码位置**：[backend/services/task_manager.py:98-1800+](../../vendors/banana-slides/backend/services/task_manager.py#L98-L1800)

**作用**：使用 ThreadPoolExecutor 管理后台任务

**设计特点**：
- **无需 Celery/Redis**：使用内存任务跟踪
- **线程池执行**：使用 `concurrent.futures.ThreadPoolExecutor`
- **状态管理**：通过 Task 模型跟踪任务状态

**全局限制器实例**：
```python
# 文本生成限制器
description_limiter = ResourceLimiter("Description", description_workers)

# 图片生成限制器
image_limiter = ResourceLimiter("Image", image_workers)
```

---

## 异步任务分类

### 1. 描述生成任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generate_descriptions_task()` | 批量生成页面描述 | [task_manager.py:362-519](../../vendors/banana-slides/backend/services/task_manager.py#L362-L519) |

**工作流程**：
1. 获取项目所有页面
2. 并发调用 `generate_page_description()`
3. 使用 `description_limiter` 限制并发
4. 更新页面描述内容
5. 更新任务状态

**并发控制**：
- 使用 `ThreadPoolExecutor` 并发生成
- 使用 `description_limiter` 限制 AI 调用并发数

---

### 2. 图片生成任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generate_images_task()` | 批量生成页面图片 | [task_manager.py:521-752](../../vendors/banana-slides/backend/services/task_manager.py#L521-L752) |
| `generate_single_page_image_task()` | 生成单个页面图片 | [task_manager.py:754-896](../../vendors/banana-slides/backend/services/task_manager.py#L754-L896) |

**工作流程**：
1. 获取项目所有页面
2. 并发调用 `generate_image()`
3. 使用 `image_limiter` 限制并发
4. 保存图片到文件系统
5. 保存图片版本记录
6. 更新任务状态

**图片版本管理**：
```python
save_image_with_version(
    image, project_id, page_id, file_service,
    version_type="generated"
)
```

**并发控制**：
- 使用 `ThreadPoolExecutor` 并发生成
- 使用 `image_limiter` 限制 AI 调用并发数

---

### 3. 图片编辑任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `edit_page_image_task()` | 编辑页面图片（Vibe 式修改） | [task_manager.py:898-1010](../../vendors/banana-slides/backend/services/task_manager.py#L898-L1010) |

**工作流程**：
1. 获取当前页面图片
2. 处理用户框选区域
3. 构建编辑提示词
4. 调用 AI 图片编辑
5. 保存新图片版本
6. 更新任务状态

**支持的编辑操作**：
- 整页修改
- 区域编辑（框选）
- 素材替换

---

### 4. 素材生成任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generate_material_image_task()` | 生成素材图片 | [task_manager.py:1012-1116](../../vendors/banana-slides/backend/services/task_manager.py#L1012-L1116) |
| `process_material_image_task()` | 处理素材图片（编辑/擦除） | [task_manager.py:1118-1284](../../vendors/banana-slides/backend/services/task_manager.py#L1118-L1284) |

**支持的操作**：
- `generate`：生成新素材
- `edit_full`：整页编辑
- `region_edit`：区域编辑
- `erase_region`：区域擦除

---

### 5. PPT 翻新任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `process_ppt_renovation_task()` | 处理 PPT 翻新项目 | [task_manager.py:1286-1537](../../vendors/banana-slides/backend/services/task_manager.py#L1286-L1537) |

**工作流程**：
1. 上传 PDF/PPTX 文件
2. 使用 MinerU 解析文档
3. 逐页提取内容
4. 生成大纲和描述
5. 生成封面图片
6. 更新任务状态

---

### 6. 导出任务

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `export_editable_pptx_with_recursive_analysis_task()` | 导出可编辑 PPTX | [task_manager.py:1539-1794](../../vendors/banana-slides/backend/services/task_manager.py#L1539-L1794) |
| `export_video_task()` | 导出讲解视频 | [task_manager.py:1796-1950+](../../vendors/banana-slides/backend/services/task_manager.py#L1796-L1950) |

**可编辑 PPTX 导出流程**：
1. 逐页分析图片
2. 提取文字属性（颜色、字体）
3. 清理背景（去除文字和图表）
4. 生成可编辑的 PPTX
5. 嵌入提取的文字和背景

**视频导出流程**：
1. 生成旁白（如需要）
2. 渲染每页为图片
3. 使用 FFmpeg 合成视频
4. 添加字幕和旁白

---

## 辅助功能

### 1. 图片版本管理

**函数**：`save_image_with_version()`

**代码位置**：[task_manager.py:186-252](../../vendors/banana-slides/backend/services/task_manager.py#L186-L252)

**作用**：保存图片并创建版本记录

**版本类型**：
- `generated`：AI 生成
- `edited`：用户编辑
- `uploaded`：用户上传

---

### 2. 资源限制同步

**函数**：`sync_resource_limits()`

**代码位置**：[task_manager.py:177-184](../../vendors/banana-slides/backend/services/task_manager.py#L177-L184)

**作用**：根据配置更新资源限制器容量

**配置项**：
- `description_workers`：描述生成并发数
- `image_workers`：图片生成并发数

---

### 3. 数据库重试

**函数**：`_commit_with_retry()`

**代码位置**：[task_manager.py:254-285](../../vendors/banana-slides/backend/services/task_manager.py#L254-L285)

**作用**：数据库提交失败时自动重试

**重试策略**：
- 最多 5 次重试
- 指数退避延迟
- 捕获 `OperationalError`

---

## 任务状态管理

### Task 模型

| 字段 | 说明 |
|-----|------|
| `id` | 任务 ID |
| `project_id` | 项目 ID |
| `status` | 状态：pending / processing / completed / failed |
| `task_type` | 任务类型 |
| `result` | 任务结果（JSON） |
| `error` | 错误信息 |
| `created_at` | 创建时间 |
| `updated_at` | 更新时间 |

### 状态转换

```
pending → processing → completed
                     ↘ failed
```

---

## 并发控制策略

### 1. ThreadPoolExecutor

**用途**：并发执行多个任务

**配置**：
```python
executor = ThreadPoolExecutor(max_workers=max_workers)
```

**使用场景**：
- 批量生成描述
- 批量生成图片
- 并发处理多个页面

### 2. ResourceLimiter

**用途**：限制外部资源并发访问

**配置**：
```python
description_limiter = ResourceLimiter("Description", capacity)
image_limiter = ResourceLimiter("Image", capacity)
```

**使用场景**：
- 限制 AI API 调用并发数
- 避免触发 API 速率限制
- 控制资源消耗

---

## 错误处理

### 1. 任务失败处理

```python
try:
    # 执行任务
    result = do_task()
    task.status = 'completed'
    task.result = result
except Exception as e:
    task.status = 'failed'
    task.error = str(e)
finally:
    db.session.commit()
```

### 2. 超时处理

- 使用 `concurrent.futures.as_completed()` 超时控制
- 任务执行时间过长则标记为失败

### 3. 数据库错误

- 使用 `_commit_with_retry()` 自动重试
- 捕获 `OperationalError` 并重试

---

## 设计亮点

### 1. 轻量级实现

- **无需 Celery**：不需要 Redis 或消息队列
- **内存管理**：使用内存跟踪任务状态
- **简单部署**：无需额外组件

### 2. 灵活并发控制

- **分层限制**：TaskManager 级别 + ResourceLimiter 级别
- **动态调整**：支持运行时更新容量
- **线程安全**：使用 Condition 保护共享状态

### 3. 版本管理

- **图片版本**：保存每次生成的图片
- **版本切换**：支持回退到历史版本
- **版本追踪**：记录生成方式（generated/edited/uploaded）

---

## 典型任务流程

### 批量生成描述

```
1. 控制器调用
   ↓
2. 创建 Task（pending）
   ↓
3. 提交到 ThreadPoolExecutor
   ↓
4. 更新 Task（processing）
   ↓
5. 并发生成描述（使用 description_limiter）
   ↓
6. 更新页面内容
   ↓
7. 更新 Task（completed）
   ↓
8. 返回结果
```

### 批量生成图片

```
1. 控制器调用
   ↓
2. 创建 Task（pending）
   ↓
3. 提交到 ThreadPoolExecutor
   ↓
4. 更新 Task（processing）
   ↓
5. 并发生成图片（使用 image_limiter）
   ↓
6. 保存图片版本
   ↓
7. 更新 Task（completed）
   ↓
8. 返回结果
```

---

## 下一步

基于此理解，我们：

1. **已完成**：
   - [x] 提示词清单
   - [x] AI 服务架构理解
   - [x] 任务管理系统理解

2. **待完成**：
   - [ ] 控制器代码阅读
   - [ ] 前端 API 阅读
   - [ ] 数据模型阅读
   - [ ] 架构图绘制
   - [ ] 业务流程梳理
