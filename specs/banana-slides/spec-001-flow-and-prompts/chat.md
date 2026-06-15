## add submodules

提示词

```text
帮我把  git@github.com:Anionex/banana-slides.git 给 submodule 到 @vendors 目录下，并且列出来你都用了什么命令
```

命令讲解


```bash
# 1. 进入你的项目根目录
cd /path/to/your-project

# 2. 添加 submodule（自动 clone 并创建 .gitmodules）
# 克隆仓库到 vendors/banana-slides，创建 .gitmodules，在父仓库记录 submodule 的 commit hash
git submodule add git@github.com:Anionex/banana-slides.git vendors/banana-slides

# 3. 初始化并拉取代码（真正把代码下载下来）
# 真正 checkout 代码；--init 自动注册配置；--recursive 处理嵌套 submodule
git submodule update --init --recursive

# 4. 提交到父仓库
git add .gitmodules vendors/banana-slides
git commit -m "Add banana-slides as submodule in vendors/"

# 5. 后续更新submodules
cd vendors/banana-slides && git pull origin main && cd ../..
git add vendors/banana-slides
git commit -m "Update banana-slides submodule"
```

## 梳理处理流程和提示词

分析需求并生成提示词

```text
对于 @vendors/banana-slides/ 这样的项目，我想梳理出它的功能执行流程，以及各环节的提示词，整理成一份文档。
问题1：这文档属于什么类型的文档？SPEC还是其它？ 
问题2：什么身份的人最适合写写这样的文档？
问题3：对于通过这篇文档，学习这个开源项目的人来说，文档应该具备什么样的特点？
将你的分析结论输出在 @specs/banana-slides/requirement.md 中
```

```text
现在我想让 Claude Code 阅读 @vendors/banana-slides/  下面的代码，生成这样的功能执行流程及提示词文档在 @docs/banana-slides/目录下，根据你刚才的分析内容，帮我生成这个文档的需求规格（SPEC）放在 @specs/banana-slides/spec.md 文档中。注意：1. 我希望 Prompt 单独放在一起，但是在 功能执行流程中，也添加相关的提示词引用；2. 生成 sped.md 即可，先不要去写最终的目标文档。
```



