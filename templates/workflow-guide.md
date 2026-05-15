# Workflow Guide

## 目标

把设计图转换为前端可落地的生产资料，而不是把截图切成不可维护的图片。

## 产物解释

### 01-input/input-map.md

记录用户上传的设计图顺序、页面名称、用途和推断信息。

### 02-analysis/page-analysis.md

逐页拆分设计图，判断哪些内容属于前端组件，哪些内容属于图片素材。

### 03-manifest/asset-manifest.json

最终图片资产清单。前端和图片生成都以它为准。

### 03-manifest/prompt-groups.json

提示词复用组映射。解决“一个提示词生成多张图”时数量和命名不清楚的问题。

### 04-prompts/gpt-image-prompts.md

给图片模型使用的提示词文件。生成图片时按这个文件逐项执行。

### 05-generated-assets/

所有生成后的干净图片资产。

### 06-frontend-handoff/frontend-handoff.md

前端开发交接文档。用户可以在新会话中把它交给 Agent，让 Agent 继续写页面代码。
