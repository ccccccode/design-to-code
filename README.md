# design-to-code

`design-to-code` 是一个面向 AI Agent 的工作流 Skill，用于把移动端/网页端设计图转成可落地的前端开发资产方案。

它不做“截图反切”，而是执行：

设计图输入 -> 页面结构分析 -> 前端组件拆分 -> 图片素材清单 -> 图片生成提示词 -> 用户确认 -> 生成干净图片资产 -> 输出前端交接文档。

## 适用场景

- 移动端商城 UI 图转 uniapp / Vue3 / TypeScript 页面
- 设计截图拆分为前端组件和图片资产
- 用 gpt-image / 图像模型重建干净商品图、Banner、运营图
- 在新会话中把交接文档交给另一个 Agent 继续写代码

## 核心原则

- 截图只作为视觉参考，不直接反切生产素材
- 文字、按钮、价格、标签、搜索框、TabBar、表单、卡片、阴影、圆角全部由前端实现
- 图片只用于商品图、人物图、Banner、运营背景图、材质图、头像
- 生成图片前必须输出 manifest 和 prompts，并让用户确认
- 流程结束必须生成 `frontend-handoff.md`

## 目录结构

```txt
design-to-code/
  SKILL.md
  README.md
  templates/
    user-intake.md
    workflow-guide.md
    page-analysis.template.md
    asset-manifest.template.json
    prompt-groups.template.json
    gpt-image-prompts.template.md
    frontend-handoff.template.md
    output-readme.md
  examples/
    mall-ui/
      asset-manifest.example.json
      prompt-groups.example.json
      frontend-handoff.example.md
  docs/
    file-structure.md
```

## 快速使用

把 `SKILL.md` 和 `templates/` 放到你的 Agent skills 目录中，然后在会话里说：

```txt
使用 design-to-code skill，根据我上传的设计图生成前端可落地的素材方案。
```

如果没有上传设计图，Agent 必须先要求用户上传设计图，并说明每张图用途。
