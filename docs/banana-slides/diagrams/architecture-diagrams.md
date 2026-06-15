# Banana Slides 系统架构图

## 1. 总体架构

```mermaid
graph TB
    subgraph "前端 Frontend"
        UI[React 用户界面]
        API[API 客户端]
        SSE[EventSource 流式接收]
    end
    
    subgraph "后端 Backend"
        subgraph "控制器层 Controllers"
            PC[ProjectController]
            PaC[PageController]
            EC[ExportController]
            MC[MaterialController]
            SC[SettingsController]
        end
        
        subgraph "服务层 Services"
            AIS[AIService]
            TM[TaskManager]
            FS[FileService]
            ES[ExportService]
            TTS[TTSVideoService]
        end
        
        subgraph "数据层 Data Layer"
            DB[(SQLite 数据库)]
            FS2[文件系统 uploads/]
        end
    end
    
    subgraph "AI Providers"
        GP[Gemini Provider]
        OP[OpenAI Provider]
        VP[Vertex Provider]
        LP[LazyLLM Provider]
    end
    
    UI --> API
    API --> PC
    API --> PaC
    API --> EC
    API --> MC
    API --> SC
    
    PC --> AIS
    PC --> TM
    PaC --> AIS
    PaC --> TM
    EC --> ES
    EC --> TM
    MC --> TM
    
    AIS --> GP
    AIS --> OP
    AIS --> VP
    AIS --> LP
    
    TM --> AIS
    TM --> FS2
    ES --> DB
    ES --> FS2
    
    PC --> DB
    PaC --> DB
    MC --> DB
    
    UI -.-> SSE
    SSE -.-> PC
    SSE -.-> PaC
```

---

## 2. 数据流向：创建项目

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as ProjectController
    participant D as 数据库
    participant FS as 文件系统
    
    U->>F: 输入想法/大纲/描述
    F->>C: POST /api/projects
    C->>C: 验证输入
    C->>D: 创建 Project 记录
    C->>FS: 创建项目文件夹
    D-->>C: 返回 project_id
    C-->>F: 返回项目信息
    F-->>U: 显示项目详情
```

---

## 3. 数据流向：生成大纲

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as ProjectController
    participant AI as AIService
    participant GP as Gemini Provider
    
    U->>F: 点击"生成大纲"
    F->>C: POST /api/projects/{id}/generate/outline
    C->>C: 创建 ProjectContext
    C->>AI: generate_outline(context)
    AI->>AI: 调用 get_outline_generation_prompt()
    AI->>GP: 调用文本生成模型
    GP-->>AI: 返回大纲 JSON
    AI->>AI: 解析返回结果
    AI-->>C: 返回结构化大纲
    C->>D: 更新 Project.pages
    C-->>F: 返回大纲
    F-->>U: 显示大纲
```

---

## 4. 数据流向：流式生成大纲（SSE）

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as ProjectController
    participant AI as AIService
    participant GP as Gemini Provider
    
    U->>F: 点击"生成大纲"（流式）
    F->>C: POST /api/projects/{id}/generate/outline/stream<br>（SSE 连接）
    C->>AI: generate_outline_stream(context)
    
    loop 每生成一页
        AI->>GP: 流式调用模型
        GP-->>AI: 返回页面内容
        AI->>AI: 解析页面
        AI-->>C: 发送页面事件
        C-->>F: 发送 SSE 事件<br>event: page
        F-->>U: 实时显示页面
    end
    
    AI-->>C: 发送完成事件
    C-->>F: 发送 SSE 事件<br>event: done
    F-->>U: 显示完成状态
```

---

## 5. 数据流向：生成图片

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as ProjectController
    participant TM as TaskManager
    participant AI as AIService
    participant IP as Image Provider
    participant FS as 文件系统
    participant D as 数据库
    
    U->>F: 点击"生成图片"
    F->>C: POST /api/projects/{id}/generate/images
    C->>D: 创建 Task（pending）
    C->>TM: 提交异步任务
    C-->>F: 返回 task_id
    
    TM->>TM: 更新 Task（processing）
    
    par 并发生成所有页面
        TM->>AI: generate_image(页面1描述)
        AI->>AI: 调用 get_image_generation_prompt()
        AI->>IP: 生成图片
        IP-->>AI: 返回图片数据
        AI->>FS: 保存图片
        AI-->>TM: 返回图片路径
    and
        TM->>AI: generate_image(页面2描述)
        AI->>IP: 生成图片
        IP-->>AI: 返回图片数据
        AI->>FS: 保存图片
        AI-->>TM: 返回图片路径
    end
    
    TM->>D: 更新所有页面的 image_url
    TM->>D: 更新 Task（completed）
    
    loop 前端轮询
        F->>C: GET /api/projects/{id}/tasks/{task_id}
        C->>D: 查询任务状态
        D-->>C: 返回任务状态
        C-->>F: 返回状态
    end
    
    F-->>U: 显示生成的图片
```

---

## 6. 数据流向：Vibe 式图片编辑

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as PageController
    participant TM as TaskManager
    participant AI as AIService
    participant IP as Image Provider
    participant FS as 文件系统
    
    U->>U: 框选图片区域
    U->>F: 输入修改指令："把这个区域换成饼图"
    F->>C: POST /api/projects/{id}/pages/{page_id}/edit/image<br>（带框选坐标）
    C->>D: 创建 Task（pending）
    C->>TM: 提交异步任务
    C-->>F: 返回 task_id
    
    TM->>TM: 更新 Task（processing）
    TM->>AI: edit_image(指令, 原图, 框选坐标)
    AI->>AI: 调用 get_image_edit_prompt()
    AI->>AI: 构建编辑指令和参考图
    AI->>IP: 编辑图片
    IP-->>AI: 返回编辑后的图片
    AI->>FS: 保存新图片版本
    AI->>FS: 保存到 PageImageVersion
    AI-->>TM: 返回新图片路径
    
    TM->>D: 更新 Page.image_url
    TM->>D: 更新 Task（completed）
    
    loop 前端轮询
        F->>C: GET /api/projects/{id}/tasks/{task_id}
        C-->>F: 返回任务状态
    end
    
    F-->>U: 显示编辑后的图片
```

---

## 7. 数据流向：导出 PPTX

```mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant C as ExportController
    participant ES as ExportService
    participant D as 数据库
    participant FS as 文件系统
    
    U->>F: 点击"导出 PPTX"
    F->>C: GET /api/projects/{id}/export/pptx
    C->>D: 查询项目所有页面
    D-->>C: 返回页面列表
    C->>ES: build_pptx(页面列表)
    ES->>ES: 读取所有页面图片
    ES->>ES: 创建 PPTX 文件
    ES->>ES: 添加每页图片和文字
    ES->>FS: 保存 PPTX 文件
    ES-->>C: 返回文件路径
    C-->>F: 返回下载 URL
    F->>FS: 下载文件
    FS-->>F: 返回文件
    F-->>U: 保存文件
```

---

## 8. 模块依赖关系

```mermaid
graph LR
    subgraph "前端依赖"
        UI[React UI] --> API[API Client]
        API --> EP[endpoints.ts]
        EP --> T[TypeScript Types]
    end
    
    subgraph "后端依赖"
        PC[ProjectController] --> AIS[AIService]
        PC --> TM[TaskManager]
        PaC[PageController] --> AIS
        PaC --> TM
        EC[ExportController] --> ES[ExportService]
        EC --> TM
        MC[MaterialController] --> TM
        
        AIS --> TP[TextProvider]
        AIS --> IP[ImageProvider]
        TM --> AIS
        TM --> DB[(Database)]
        
        PC --> PM[Project Model]
        PaC --> PaM[Page Model]
        MC --> MM[Material Model]
    end
    
    subgraph "AI Provider 依赖"
        TP --> GP[Gemini]
        TP --> OP[OpenAI]
        TP --> VP[Vertex]
        TP --> LP[LazyLLM]
        
        IP --> GP
        IP --> OP
        IP --> VP
        IP --> LP
    end
```

---

## 9. 任务管理系统架构

```mermaid
graph TB
    subgraph "任务提交"
        C[Controller] --> |创建 Task| DB[(Database)]
        C --> |提交任务| TM[TaskManager]
    end
    
    subgraph "任务执行"
        TM --> |获取任务| DB
        TM --> |创建执行器| TE[ThreadPoolExecutor]
        TE --> |并发执行| T1[Worker 1]
        TE --> |并发执行| T2[Worker 2]
        TE --> |并发执行| T3[Worker N]
    end
    
    subgraph "资源限制"
        RL1[DescriptionLimiter] --> |限制并发| T1
        RL2[ImageLimiter] --> |限制并发| T2
        RL1 --> |限制并发| T3
        RL2 --> |限制并发| T1
    end
    
    subgraph "AI 调用"
        T1 --> |调用| AIS[AIService]
        T2 --> |调用| AIS
        T3 --> |调用| AIS
        AIS --> |调用| GP[AI Provider]
    end
    
    subgraph "结果保存"
        T1 --> |更新| DB
        T1 --> |保存| FS[文件系统]
        T2 --> |更新| DB
        T2 --> |保存| FS
        T3 --> |更新| DB
        T3 --> |保存| FS
    end
    
    TM --> |更新状态| DB
```

---

## 10. 前端状态管理

```mermaid
graph TB
    subgraph "Zustand Store"
        PS[ProjectStore]
        PgS[PageStore]
        SS[SettingsStore]
        TS[TaskStore]
    end
    
    subgraph "组件"
        CP[CreateProject]
        EP[EditProject]
        GP[GeneratePages]
        IP[ImageEditor]
        EPD[ExportDialog]
    end
    
    subgraph "API 调用"
        E[Endpoints]
    end
    
    CP --> PS
    EP --> PS
    EP --> PgS
    GP --> PgS
    GP --> TS
    IP --> PgS
    IP --> TS
    EPD --> PS
    
    PS --> E
    PgS --> E
    SS --> E
    TS --> E
```

---

## 架构说明

### 技术栈

**前端**：
- React 18
- TypeScript
- Vite 5
- Zustand（状态管理）
- TailwindCSS

**后端**：
- Python 3.10+
- Flask 3.0
- SQLAlchemy
- ThreadPoolExecutor

**AI Provider**：
- Gemini（Google）
- OpenAI
- Vertex AI
- LazyLLM

**数据存储**：
- SQLite（数据库）
- 文件系统（uploads/）

### 设计模式

1. **Provider 模式**：抽象不同的 AI Provider
2. **Context 模式**：统一管理项目上下文
3. **Task 模式**：异步任务管理
4. **Stream 模式**：流式生成（SSE）

### 核心流程

1. **创作流程**：想法 → 大纲 → 描述 → 图片
2. **编辑流程**：自然语言修改 → AI 处理 → 版本保存
3. **导出流程**：读取数据 → 生成文件 → 下载
4. **任务流程**：创建 Task → 异步执行 → 状态查询
