# 前端 API 与数据模型

## 前端 API

**代码位置**：[frontend/src/api/endpoints.ts](../../vendors/banana-slides/frontend/src/api/endpoints.ts)

### API 分类

#### 1. 项目相关 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `createProject()` | 创建项目 | [endpoints.ts:24-42](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L24-L42) |
| `getProject()` | 获取项目详情 | [endpoints.ts:78-81](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L78-L81) |
| `listProjects()` | 获取项目列表 | [endpoints.ts:64-73](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L64-L73) |
| `updateProject()` | 更新项目 | [endpoints.ts:94-100](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L94-L100) |
| `deleteProject()` | 删除项目 | [endpoints.ts:86-89](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L86-L89) |
| `updatePagesOrder()` | 更新页面顺序 | [endpoints.ts:105-114](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L105-L114) |

#### 2. 大纲生成 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generateOutline()` | 生成大纲 | [endpoints.ts:123-130](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L123-L130) |
| `generateOutlineStream()` | 流式生成大纲（SSE） | [endpoints.ts:151-212](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L151-L212) |
| `refineOutline()` | 细化大纲 | [endpoints.ts:368-384](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L368-L384) |

#### 3. 描述生成 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generateFromDescription()` | 从描述生成 | [endpoints.ts:222-232](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L222-L232) |
| `generateDescriptions()` | 批量生成描述 | [endpoints.ts:239-246](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L239-L246) |
| `generateDescriptionsStream()` | 流式生成描述（SSE） | [endpoints.ts:264-324](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L264-L324) |
| `generatePageDescription()` | 生成单页描述 | [endpoints.ts:329-342](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L329-L342) |
| `refineDescriptions()` | 细化描述 | [endpoints.ts:393-409](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L393-L409) |

#### 4. 图片生成 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `generateImages()` | 批量生成图片 | [endpoints.ts:419-426](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L419-L426) |
| `generatePageImage()` | 生成单页图片 | [endpoints.ts:431-443](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L431-L443) |
| `editPageImage()` | 编辑页面图片 | [endpoints.ts:448-490](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L448-L490) |
| `getPageImageVersions()` | 获取图片版本 | [endpoints.ts:495-503](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L495-L503) |
| `setCurrentImageVersion()` | 设置当前版本 | [endpoints.ts:508-517](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L508-L517) |

#### 5. 导出 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `exportPPTX()` | 导出 PPTX | [endpoints.ts:681-698](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L681-L698) |
| `exportPDF()` | 导出 PDF | [endpoints.ts:705-714](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L705-L714) |
| `exportImages()` | 导出图片 | [endpoints.ts:719-728](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L719-L728) |
| `exportEditablePPTX()` | 导出可编辑 PPTX（异步） | [endpoints.ts:736-748](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L736-L748) |
| `exportVideo()` | 导出讲解视频（异步） | [endpoints.ts:771-810](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L771-L810) |
| `listExports()` | 列出导出文件 | [endpoints.ts:753-764](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L753-L764) |

#### 6. 素材 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `listMaterials()` | 获取素材列表 | [endpoints.ts:918-936](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L918-L936) |
| `uploadMaterial()` | 上传素材 | [endpoints.ts:945-968](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L945-L968) |
| `deleteMaterial()` | 删除素材 | [endpoints.ts:973-976](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L973-L976) |
| `getMaterialCaption()` | 获取素材描述 | [endpoints.ts:981-984](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L981-L984) |
| `generateMaterialImage()` | 生成素材图片 | [endpoints.ts:818-845](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L818-L845) |
| `processMaterialImage()` | 处理素材图片 | [endpoints.ts:873-908](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L873-L908) |
| `associateMaterialsToProject()` | 关联素材到项目 | [endpoints.ts:1024-1033](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1024-L1033) |

#### 7. 参考文件 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `uploadReferenceFile()` | 上传参考文件 | [endpoints.ts:1137-1152](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1137-L1152) |
| `getReferenceFile()` | 获取文件信息 | [endpoints.ts:1158-1163](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1158-L1163) |
| `listProjectReferenceFiles()` | 列出项目文件 | [endpoints.ts:1169-1176](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1169-L1176) |
| `deleteReferenceFile()` | 删除文件 | [endpoints.ts:1182-1187](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1182-L1187) |
| `triggerFileParse()` | 触发文件解析 | [endpoints.ts:1193-1198](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1193-L1198) |
| `associateFileToProject()` | 关联文件到项目 | [endpoints.ts:1205-1214](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1205-L1214) |
| `dissociateFileFromProject()` | 从项目移除文件 | [endpoints.ts:1220-1227](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1220-L1227) |

#### 8. 旁白 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `updatePageNarration()` | 更新页面旁白 | [endpoints.ts:606-616](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L606-L616) |
| `generatePageNarration()` | 生成单页旁白 | [endpoints.ts:621-632](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L621-L632) |
| `generateAllNarrations()` | 批量生成旁白 | [endpoints.ts:637-648](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L637-L648) |

#### 9. 设置 API

| 函数名 | 说明 | 代码位置 |
|-------|------|---------|
| `getSettings()` | 获取设置 | [endpoints.ts:1276-1279](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1276-L1279) |
| `updateSettings()` | 更新设置 | [endpoints.ts:1289-1303](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1289-L1303) |
| `resetSettings()` | 重置设置 | [endpoints.ts:1308-1311](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1308-L1311) |
| `verifyApiKey()` | 验证 API Key | [endpoints.ts:1356-1359](../../vendors/banana-slides/frontend/src/api/endpoints.ts#L1356-L1359) |

---

## 数据模型

### 1. Project（项目）

**代码位置**：[backend/models/project.py](../../vendors/banana-slides/backend/models/project.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 项目 ID |
| `creation_type` | str | 创作类型：idea/outline/descriptions |
| `idea_prompt` | str | 用户输入的想法 |
| `outline_text` | str | 用户提供的大纲文本 |
| `description_text` | str | 用户提供的描述文本 |
| `template_image_url` | str | 模板图片 URL |
| `image_aspect_ratio` | str | 图片比例（16:9/4:3） |
| `pages` | List[Page] | 页面列表 |
| `reference_files` | List[ReferenceFile] | 参考文件列表 |
| `created_at` | datetime | 创建时间 |
| `updated_at` | datetime | 更新时间 |

---

### 2. Page（页面）

**代码位置**：[backend/models/page.py](../../vendors/banana-slides/backend/models/page.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 页面 ID |
| `project_id` | str | 项目 ID |
| `index` | int | 页面序号 |
| `title` | str | 页面标题 |
| `points` | List[str] | 大纲要点 |
| `part` | str | 所属章节（可选） |
| `outline_content` | dict | 大纲内容 |
| `description_content` | dict | 描述内容 |
| `image_url` | str | 生成的图片 URL |
| `narration_text` | str | 旁白文本 |
| `created_at` | datetime | 创建时间 |
| `updated_at` | datetime | 更新时间 |

**description_content 结构**：
```json
{
    "text": "页面文字内容",
    "materials": ["图片素材1", "图片素材2"],
    "extra_fields": {
        "备注": "额外的字段内容"
    }
}
```

---

### 3. Material（素材）

**代码位置**：[backend/models/material.py](../../vendors/banana-slides/backend/models/material.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 素材 ID |
| `project_id` | str | 项目 ID（可为 null，表示全局素材） |
| `url` | str | 素材 URL |
| `thumb_url` | str | 缩略图 URL |
| `caption` | str | 素材描述 |
| `created_at` | datetime | 创建时间 |

---

### 4. ReferenceFile（参考文件）

**代码位置**：[backend/models/reference_file.py](../../vendors/banana-slides/backend/models/reference_file.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 文件 ID |
| `project_id` | str | 项目 ID（可为 null） |
| `filename` | str | 文件名 |
| `file_size` | int | 文件大小 |
| `file_type` | str | 文件类型（pdf/docx/md/txt） |
| `parse_status` | str | 解析状态：pending/parsing/completed/failed |
| `markdown_content` | str | 解析后的 Markdown 内容 |
| `error_message` | str | 错误信息 |
| `created_at` | datetime | 创建时间 |

---

### 5. Task（任务）

**代码位置**：[backend/models/task.py](../../vendors/banana-slides/backend/models/task.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 任务 ID |
| `project_id` | str | 项目 ID |
| `status` | str | 状态：pending/processing/completed/failed |
| `task_type` | str | 任务类型 |
| `result` | json | 任务结果 |
| `error` | str | 错误信息 |
| `created_at` | datetime | 创建时间 |
| `updated_at` | datetime | 更新时间 |

**任务类型**：
- `generate_descriptions`：生成描述
- `generate_images`：生成图片
- `export_editable_pptx`：导出可编辑 PPTX
- `export_video`：导出视频
- `process_ppt_renovation`：PPT 翻新
- `generate_material_image`：生成素材
- `process_material_image`：处理素材

---

### 6. PageImageVersion（图片版本）

**代码位置**：[backend/models/page.py](../../vendors/banana-slides/backend/models/page.py)

**字段**：
| 字段 | 类型 | 说明 |
|-----|------|------|
| `id` | str | 版本 ID |
| `page_id` | str | 页面 ID |
| `image_url` | str | 图片 URL |
| `version_type` | str | 版本类型：generated/edited/uploaded |
| `is_current` | bool | 是否为当前版本 |
| `created_at` | datetime | 创建时间 |

---

## 数据流向

### 创建项目流程

```
用户输入（想法/大纲/描述）
  ↓
前端调用 createProject()
  ↓
POST /api/projects
  ↓
后端 ProjectController.create_project()
  ↓
创建 Project 记录
  ↓
返回项目信息
  ↓
前端保存项目 ID
```

### 生成大纲流程

```
用户点击"生成大纲"
  ↓
前端调用 generateOutline() 或 generateOutlineStream()
  ↓
POST /api/projects/{id}/generate/outline
  ↓
后端 ProjectController.generate_outline()
  ↓
创建 AIService
  ↓
构建 ProjectContext
  ↓
调用 get_outline_generation_prompt()
  ↓
调用 AI Provider
  ↓
解析返回结果
  ↓
更新 Project.pages
  ↓
返回大纲
```

### 生成图片流程

```
用户点击"生成图片"
  ↓
前端调用 generateImages()
  ↓
POST /api/projects/{id}/generate/images
  ↓
后端 ProjectController.generate_images()
  ↓
创建 Task（pending）
  ↓
提交到 TaskManager
  ↓
TaskManager.generate_images_task()
  ↓
并发生成每页图片
  ↓
调用 AIService.generate_image()
  ↓
保存图片版本
  ↓
更新 Task（completed）
  ↓
前端轮询任务状态
```

---

## 下一步

阶段一接近完成！最后两个任务：

1. **绘制架构图**
2. **梳理业务流程**

这将完成所有资料收集工作。
