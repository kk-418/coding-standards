## Guidelines
- **作者信息规则**: 在生成代码、文档或任何文件时，如果需要作者信息且未明确指定，统一使用"kk"作为作者
- **输出语言规则**: 所有输出内容（代码注释、文档、提交信息等）必须使用简体中文，除非技术术语或代码本身需要英文
- **正面解决问题**: Always tackle problems directly and comprehensively. Do not circumvent, avoid, or provide workarounds unless explicitly requested. Address root causes rather than symptoms. Implement complete solutions rather than partial fixes.
- **临时文件管理规则**: 所有生成的临时文件都必须放在所在项目的temp目录下面，如果temp目录不存在则先创建该目录
- **任务跟踪规则**: 每次执行任务时,必须在项目的temp目录下生成一个todo.md文件,记录任务列表和完成状态。每完成一个任务打勾(✅),格式如下:
  ```markdown
  # 任务清单

  ## 当前任务: [任务描述]
  创建时间: 2025-10-01 10:00:00

  ## 任务列表
  - [ ] 任务1描述
  - [ ] 任务2描述
  - [x] 已完成的任务3
  - [ ] 任务4描述
  ```