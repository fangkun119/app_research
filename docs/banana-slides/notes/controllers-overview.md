# 后端控制器概览

## 控制器列表

| 控制器 | 说明 | 代码位置 | 核心路由 |
|-------|------|---------|---------|
| `project_controller.py` | 项目管理 | [project_controller.py](../../vendors/banana-slides/backend/controllers/project_controller.py) | `/api/projects` |
| `page_controller.py` | 页面管理 | [page_controller.py](../../vendors/banana-slides/backend/controllers/page_controller.py) | `/api/projects/{project_id}/pages` |
| `export_controller.py` | 导出功能 | [export_controller.py](../../vendors/banana-slides/backend/controllers/export_controller.py) | `/api/projects/{project_id}/export` |
| `material_controller.py` | 素材管理 | [material_controller.py](../../vendors/banana-slides/backend/controllers/material_controller.py) | `/api/materials` |
| `reference_file_controller.py` | 参考文件 | [reference_file_controller.py](../../vendors/banana-slides/backend/controllers/reference_file_controller.py) | `/api/reference-files` |
| `file_controller.py` | 文件管理 | [file_controller.py](../../vendors/banana-slides/backend/controllers/file_controller.py) | 文件上传下载 |
| `template_controller.py` | 模板管理 | [template_controller.py](../../vendors/banana-slides/backend/controllers/template_controller.py) | `/api/templates` |
| `user_template_controller.py` | 用户模板 | [user_template_controller.py](../../vendors/banana-slides/backend/controllers/user_template_controller.py) | `/api/user-templates` |
| `user_style_template_controller.py` | 用户风格模板 | [user_style_template_controller.py](../../vendors/banana-slides/backend/controllers/user_style_template_controller.py) | `/api/user-style-templates` |
| `settings_controller.py` | 系统设置 | [settings_controller.py](../../vendors/banana-slides/backend/controllers/settings_controller.py) | `/api/settings` |
| `openai_oauth_controller.py` | OpenAI OAuth | [openai_oauth_controller.py](../../vendors/banana-slides/backend/controllers/openai_oauth_controller.py) | `/api/settings/openai-oauth` |

---

## 核心控制器详解

### 1. project_controller.py（项目管理）

**路由前缀**：`/api/projects`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/api/projects` | POST | 创建项目 | [project_controller.py:45-80](../../vendors/banana-slides/backend/controllers/project_controller.py#L45-L80) |
| `/api/projects/{id}` | GET | 获取项目详情 | [project_controller.py:83-95](../../vendors/banana-slides/backend/controllers/project_controller.py#L83-L95) |
| `/api/projects` | GET | 获取项目列表 | [project_controller.py:98-115](../../vendors/banana-slides/backend/controllers/project_controller.py#L98-L115) |
| `/api/projects/{id}` | PUT | 更新项目 | [project_controller.py:118-140](../../vendors/banana-slides/backend/controllers/project_controller.py#L118-L140) |
| `/api/projects/{id}` | DELETE | 删除项目 | [project_controller.py:143-160](../../vendors/banana-slides/backend/controllers/project_controller.py#L143-L160) |
| `/api/projects/{id}/generate/outline` | POST | 生成大纲 | [project_controller.py:163-227](../../vendors/banana-slides/backend/controllers/project_controller.py#L163-L227) |
| `/api/projects/{id}/generate/outline/stream` | POST | 流式生成大纲（SSE） | [project_controller.py:230-330](../../vendors/banana-slides/backend/controllers/project_controller.py#L230-L330) |
| `/api/projects/{id}/generate/descriptions` | POST | 批量生成描述 | [project_controller.py:333-372](../../vendors/banana-slides/backend/controllers/project_controller.py#L333-L372) |
| `/api/projects/{id}/generate/descriptions/stream` | POST | 流式生成描述（SSE） | [project_controller.py:375-440](../../vendors/banana-slides/backend/controllers/project_controller.py#L375-L440) |
| `/api/projects/{id}/generate/images` | POST | 批量生成图片 | [project_controller.py:443-483](../../vendors/banana-slides/backend/controllers/project_controller.py#L443-L483) |
| `/api/projects/{id}/refine/outline` | POST | 细化大纲 | [project_controller.py:486-535](../../vendors/banana-slides/backend/controllers/project_controller.py#L486-L535) |
| `/api/projects/{id}/refine/descriptions` | POST | 细化描述 | [project_controller.py:538-587](../../vendors/banana-slides/backend/controllers/project_controller.py#L538-L587) |
| `/api/projects/{id}/template` | POST | 上传模板图片 | [project_controller.py:590-610](../../vendors/banana-slides/backend/controllers/project_controller.py#L590-L610) |
| `/api/projects/renovation` | POST | 创建 PPT 翻新项目 | [project_controller.py:613-690](../../vendors/banana-slides/backend/controllers/project_controller.py#L613-L690) |

**核心功能**：
- 项目 CRUD 操作
- 大纲生成（普通 + 流式）
- 描述生成（普通 + 流式）
- 图片生成
- 大纲/描述细化
- 模板管理
- PPT 翻新

---

### 2. page_controller.py（页面管理）

**路由前缀**：`/api/projects/{project_id}/pages`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/pages` | POST | 添加页面 | [page_controller.py:45-70](../../vendors/banana-slides/backend/controllers/page_controller.py#L45-L70) |
| `/pages/{page_id}` | GET | 获取页面详情 | [page_controller.py:73-85](../../vendors/banana-slides/backend/controllers/page_controller.py#L73-L85) |
| `/pages/{page_id}` | PUT | 更新页面 | [page_controller.py:88-115](../../vendors/banana-slides/backend/controllers/page_controller.py#L88-L115) |
| `/pages/{page_id}` | DELETE | 删除页面 | [page_controller.py:118-135](../../vendors/banana-slides/backend/controllers/page_controller.py#L118-L135) |
| `/pages/{page_id}/generate/description` | POST | 生成单页描述 | [page_controller.py:138-175](../../vendors/banana-slides/backend/controllers/page_controller.py#L138-L175) |
| `/pages/{page_id}/generate/image` | POST | 生成单页图片 | [page_controller.py:178-215](../../vendors/banana-slides/backend/controllers/page_controller.py#L178-L215) |
| `/pages/{page_id}/edit/image` | POST | 编辑页面图片 | [page_controller.py:218-340](../../vendors/banana-slides/backend/controllers/page_controller.py#L218-L340) |
| `/pages/{page_id}/image-versions` | GET | 获取图片版本列表 | [page_controller.py:343-360](../../vendors/banana-slides/backend/controllers/page_controller.py#L343-L360) |
| `/pages/{page_id}/image-versions/{version_id}/set-current` | POST | 设置当前图片版本 | [page_controller.py:363-382](../../vendors/banana-slides/backend/controllers/page_controller.py#L363-L382) |
| `/pages/{page_id}/outline` | PUT | 更新页面大纲 | [page_controller.py:385-405](../../vendors/banana-slides/backend/controllers/page_controller.py#L385-L405) |
| `/pages/{page_id}/description` | PUT | 更新页面描述 | [page_controller.py:408-428](../../vendors/banana-slides/backend/controllers/page_controller.py#L408-L428) |
| `/pages/{page_id}/regenerate-renovation` | POST | 重新生成翻新页面 | [page_controller.py:431-465](../../vendors/banana-slides/backend/controllers/page_controller.py#L431-L465) |
| `/pages/{page_id}/generate/narration` | POST | 生成页面旁白 | [page_controller.py:468-489](../../vendors/banana-slides/backend/controllers/page_controller.py#L468-L489) |
| `/pages/{page_id}/narration` | PUT | 更新页面旁白 | [page_controller.py:492-504](../../vendors/banana-slides/backend/controllers/page_controller.py#L492-L504) |

**核心功能**：
- 页面 CRUD 操作
- 单页描述生成
- 单页图片生成
- 图片编辑（Vibe 式修改）
- 图片版本管理
- 页面大纲/描述/旁白更新
- 翻新页面重新生成

---

### 3. export_controller.py（导出功能）

**路由前缀**：`/api/projects/{project_id}/export`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/export/pptx` | GET | 导出 PPTX | [export_controller.py:45-80](../../vendors/banana-slides/backend/controllers/export_controller.py#L45-L80) |
| `/export/pdf` | GET | 导出 PDF | [export_controller.py:83-115](../../vendors/banana-slides/backend/controllers/export_controller.py#L83-L115) |
| `/export/images` | GET | 导出图片 | [export_controller.py:118-145](../../vendors/banana-slides/backend/controllers/export_controller.py#L118-L145) |
| `/export/editable-pptx` | POST | 导出可编辑 PPTX（异步） | [export_controller.py:148-220](../../vendors/banana-slides/backend/controllers/export_controller.py#L148-L220) |
| `/export/video` | POST | 导出讲解视频（异步） | [export_controller.py:223-310](../../vendors/banana-slides/backend/controllers/export_controller.py#L223-L310) |
| `/exports` | GET | 列出导出文件 | [export_controller.py:313-340](../../vendors/banana-slides/backend/controllers/export_controller.py#L313-L340) |

**核心功能**：
- PPTX 导出（支持动画效果）
- PDF 导出
- 图片导出
- 可编辑 PPTX 导出（异步）
- 讲解视频导出（异步）

---

### 4. material_controller.py（素材管理）

**路由前缀**：`/api/materials`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/materials` | GET | 获取素材列表 | [material_controller.py:45-80](../../vendors/banana-slides/backend/controllers/material_controller.py#L45-L80) |
| `/materials/upload` | POST | 上传素材 | [material_controller.py:83-115](../../vendors/banana-slides/backend/controllers/material_controller.py#L83-L115) |
| `/materials/{id}` | DELETE | 删除素材 | [material_controller.py:118-135](../../vendors/banana-slides/backend/controllers/material_controller.py#L118-L135) |
| `/materials/{id}/caption` | GET | 获取素材描述 | [material_controller.py:138-155](../../vendors/banana-slides/backend/controllers/material_controller.py#L138-L155) |
| `/materials/associate` | POST | 关联素材到项目 | [material_controller.py:158-185](../../vendors/banana-slides/backend/controllers/material_controller.py#L158-L185) |
| `/materials/download` | POST | 批量下载素材 | [material_controller.py:188-210](../../vendors/banana-slides/backend/controllers/material_controller.py#L188-L210) |
| `/materials/by-url` | GET | 通过 URL 获取素材 | [material_controller.py:213-230](../../vendors/banana-slides/backend/controllers/material_controller.py#L213-L230) |

**项目专属端点**：
- `/api/projects/{project_id}/materials`：获取项目素材
- `/api/projects/{project_id}/materials/upload`：上传到项目
- `/api/projects/{project_id}/materials/generate`：生成素材
- `/api/projects/{project_id}/materials/process`：处理素材

**核心功能**：
- 素材 CRUD
- 素材描述生成
- 素材关联
- 素材生成和处理

---

### 5. reference_file_controller.py（参考文件）

**路由前缀**：`/api/reference-files`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/reference-files/upload` | POST | 上传参考文件 | [reference_file_controller.py:45-90](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L45-L90) |
| `/reference-files/{id}` | GET | 获取文件信息 | [reference_file_controller.py:93-110](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L93-L110) |
| `/reference-files/{id}` | DELETE | 删除文件 | [reference_file_controller.py:113-130](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L113-L130) |
| `/reference-files/{id}/parse` | POST | 解析文件 | [reference_file_controller.py:133-165](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L133-L165) |
| `/reference-files/{id}/associate` | POST | 关联到项目 | [reference_file_controller.py:168-190](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L168-L190) |
| `/reference-files/{id}/dissociate` | POST | 从项目移除 | [reference_file_controller.py:193-210](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L193-L210) |
| `/reference-files/project/{project_id}` | GET | 列出项目文件 | [reference_file_controller.py:213-230](../../vendors/banana-slides/backend/controllers/reference_file_controller.py#L213-L230) |

**核心功能**：
- 参考文件上传
- 文件解析（PDF/Docx/MD/Txt）
- 文件关联管理

---

### 6. settings_controller.py（系统设置）

**路由前缀**：`/api/settings`

**核心端点**：

| 端点 | 方法 | 说明 | 代码位置 |
|-----|------|------|---------|
| `/settings` | GET | 获取设置 | [settings_controller.py:45-70](../../vendors/banana-slides/backend/controllers/settings_controller.py#L45-L70) |
| `/settings` | PUT | 更新设置 | [settings_controller.py:73-115](../../vendors/banana-slides/backend/controllers/settings_controller.py#L73-L115) |
| `/settings/verify` | POST | 验证 API Key | [settings_controller.py:118-145](../../vendors/banana-slides/backend/controllers/settings_controller.py#L118-L145) |
| `/settings/reset` | POST | 重置设置 | [settings_controller.py:148-165](../../vendors/banana-slides/backend/controllers/settings_controller.py#L148-L165) |
| `/settings/check-update` | GET | 检查更新 | [settings_controller.py:168-195](../../vendors/banana-slides/backend/controllers/settings_controller.py#L168-L195) |
| `/settings/tests/{test_id}/status` | GET | 获取测试状态 | [settings_controller.py:198-220](../../vendors/banana-slides/backend/controllers/settings_controller.py#L198-L220) |
| `/settings/openai-oauth/*` | * | OpenAI OAuth 相关 | [settings_controller.py:223-330](../../vendors/banana-slides/backend/controllers/settings_controller.py#L223-L330) |

**核心功能**：
- 系统配置管理
- API Key 验证
- 更新检查
- OpenAI OAuth 集成

---

## API 设计模式

### 1. RESTful 设计

**资源命名**：
- `/api/projects`：项目资源
- `/api/projects/{id}/pages`：项目的子资源
- `/api/materials`：全局素材资源

**HTTP 方法**：
- GET：查询
- POST：创建
- PUT：更新
- DELETE：删除

### 2. 异步任务模式

**任务端点**：
- 创建任务 → 返回 task_id
- 轮询 `/api/projects/{id}/tasks/{task_id}` 查询状态

**示例**：
```python
# 1. 创建任务
POST /api/projects/{id}/export/editable-pptx
Response: { "task_id": "xxx", "status": "pending" }

# 2. 查询状态
GET /api/projects/{id}/tasks/{task_id}
Response: { "status": "processing", "result": null }

# 3. 完成后查询
GET /api/projects/{id}/tasks/{task_id}
Response: { "status": "completed", "result": {...} }
```

### 3. 流式生成模式（SSE）

**端点**：
- `/api/projects/{id}/generate/outline/stream`
- `/api/projects/{id}/generate/descriptions/stream`

**前端使用**：
```javascript
const eventSource = new EventSource('/api/projects/xxx/generate/outline/stream');
eventSource.addEventListener('page', (e) => {
    const page = JSON.parse(e.data);
    // 实时显示生成的页面
});
```

---

## 响应格式

### 成功响应

```json
{
    "success": true,
    "data": {
        // 响应数据
    }
}
```

### 错误响应

```json
{
    "success": false,
    "error": "错误信息"
}
```

---

## 下一步

基于此理解，我们：

1. **已完成**：
   - [x] 提示词清单
   - [x] AI 服务架构理解
   - [x] 任务管理系统理解
   - [x] 控制器概览

2. **待完成**：
   - [ ] 前端 API 阅读
   - [ ] 数据模型阅读
   - [ ] 架构图绘制
   - [ ] 业务流程梳理
