---
name: design-to-code
description: Use this skill when converting UI design screenshots, mobile app screens, ecommerce pages, Figma exports, or visual design images into frontend implementation assets, image-generation prompts, clean image assets, optimized exported images, asset gallery links, and a frontend handoff document for uniapp, Vue3, TypeScript, and design-to-code workflows.
---

# Design to Code

## Description

Use this skill when the user provides UI design screenshots, mobile app screens, ecommerce app designs, Figma exports, product page screenshots, or visual design images and wants to convert them into frontend implementation assets, image-generation prompts, clean image assets, optimized exported images, previewable asset directories, and a frontend handoff document.

This skill is especially useful for uniapp, Vue3, TypeScript, ecommerce UI, mobile app UI, design-to-code workflows, asset manifests, GPT image prompts, clean asset rebuilding, image compression, asset gallery management, and frontend page implementation handoff.

## Instructions

This skill turns UI design screenshots or exported design images into a production-ready frontend workflow.

It must not directly slice screenshots as production assets. It must use the screenshots only as visual references, then separate what should be implemented by frontend code from what should be rebuilt as clean image assets.

## Supported Outputs

The agent should produce these files during the workflow:

```txt
outputs/
  01-input/
    input-map.md
  02-analysis/
    page-analysis.md
  03-manifest/
    asset-manifest.json
    prompt-groups.json
  04-prompts/
    gpt-image-prompts.md
  05-generated-assets/
    master/
    banner/
    product/
    category/
    detail/
    material/
    avatar/
    icons/
  06-frontend-handoff/
    frontend-handoff.md
  07-quality-report/
    image-export-report.md
  08-preview-gallery/
    asset-gallery.md
    asset-gallery.html
```

## Hard Rules

1. Do not directly crop whole UI screenshots into production assets.
2. Do not turn UI controls into images.
3. Text, buttons, prices, labels, search bars, tab bars, navigation bars, forms, cards, shadows, rounded corners, dividers, status bars, quantity steppers, and tables must be implemented with frontend code.
4. Image assets are allowed only for Banner backgrounds, product photos, category product images, operation/promotion backgrounds, outfit photos, material/detail photos, and avatars.
5. Every image-generation prompt must require: no text, no buttons, no prices, no labels, no UI controls, no white card containers, no screenshot edges, no watermark, and no logo.
6. Generate each master image only once. WebP/JPG/PNG export variants are post-processing outputs, not new image generations.
7. Never regenerate an image only because the file size is too large. Compress or convert from the master image instead.
8. Do not inline-display every generated image in the conversation. Many inline images make Codex App sessions slow to reopen.
9. Show generated image results as directory paths, manifest rows, and clickable file links/gallery documents instead of rendering all images in chat.
10. Before generating images, the agent must show the user the analysis, manifest summary, prompt groups, asset count, file-size budget, export strategy, and preview strategy.
11. The agent must ask the user whether anything needs adjustment before generating images.
12. If the user requests adjustments, update the analysis, manifest, prompts, export policy, and preview policy first.
13. After all assets are generated and optimized, the agent must generate `frontend-handoff.md`.
14. `frontend-handoff.md` must be self-contained so the user can paste it into a new conversation and continue frontend development.

## Image Generation, Export, and Preview Rules

### Generation Rule

Generate a clean master image once per `single`, `single_master`, or `group_variant` output.

Save master images into:

```txt
outputs/05-generated-assets/master/
```

The master image is the source of truth for later export, compression, crop, and format conversion.

### Export Rule

Frontend-ready images must be exported from the master image according to `asset-manifest.json`.

Do not use master images directly in frontend code unless the user explicitly wants uncompressed development assets.

### File-size Budget Rule

Every image asset must declare:

```json
{
  "targetFormat": "jpg",
  "fallbackFormat": "webp",
  "preferredFileSizeKB": 500,
  "maxFileSizeKB": 650,
  "minQuality": 72,
  "qualityPreset": "balanced",
  "compressionPolicy": "generate-once-export-many"
}
```

Default file-size budgets:

| Asset Type | Preferred Size | Max Size | Default Format | Fallback |
|---|---:|---:|---|---|
| banner | 300-600KB | 750KB | jpg | webp |
| product | 250-500KB | 650KB | jpg | webp |
| category | 120-300KB | 400KB | png if transparent, jpg if not | webp |
| operation | 150-350KB | 450KB | jpg | webp |
| detail | 300-600KB | 800KB | jpg | webp |
| material | 120-300KB | 400KB | jpg | webp |
| avatar | 80-200KB | 250KB | jpg | webp |
| icon | 5-50KB | 80KB | svg/png | none |

### Compression Rule

Use this export chain:

```txt
master image
↓
export preferred format, usually jpg
↓
check file size
↓
if file size <= maxFileSizeKB: keep it
↓
if file size > maxFileSizeKB: reduce jpg quality step by step until minQuality
↓
if still too large: export webp from the same master image
↓
write result to image-export-report.md
```

This process is post-processing only. It must not call the image generation model again.

### Transparent Asset Rule

If `transparent=true`, use PNG first.

If PNG is too large and transparency is still required, keep PNG and report it.

If transparency is not required after review, export JPG/WebP from the same master image.

### Preview Rule

Do not embed all generated images directly in the chat response.

After generation, create:

```txt
outputs/08-preview-gallery/asset-gallery.md
outputs/08-preview-gallery/asset-gallery.html
```

The gallery must list every generated asset with:

```txt
assetName
assetType
page
masterPath
exportedPath
fileSizeKB
format
preview link
usage
```

In the final response, show only:

```txt
1. Generated asset root directory
2. Asset gallery path
3. Quality report path
4. Frontend handoff path
5. Total generated/exported asset count
```

If a visual preview is necessary, show at most 1-3 representative images, never the full batch.

### Parallel Rule

Independent image generations may be split into parallel batches or sub-agents.

Dependency order:

```txt
1. Generate single_master assets first
2. Generate independent single/group_variant assets in parallel batches
3. Run reuse/reuse_crop exports after their reuseSource exists
4. Run compression/export checks in parallel
5. Generate image-export-report.md
6. Generate asset-gallery.md and asset-gallery.html
```

Do not run `reuse` or `reuse_crop` tasks before the source master image exists.

## Workflow

### Phase 0: Input Check

If the user did not provide design images, ask the user to upload them and provide the purpose of each image.

Ask for this format:

```txt
Image 1: Home page
Image 2: Category page
Image 3: Product detail page
Image 4: Cart page
Image 5: Profile page
Image 6: Login page
Image 7: Register page
```

If the user uploaded images but did not explain their purposes, infer page purposes from the visual content and ask the user to confirm.

Create `outputs/01-input/input-map.md` recording original image order, page name, page purpose, notes, and assumptions.

### Phase 1: Page Analysis

Analyze every design image and split content into frontend components, image assets, SVG/icon assets, reusable styles, and areas that must not be sliced.

Create `outputs/02-analysis/page-analysis.md` with these sections:

```md
# Page Analysis

## Page List

## Per-page Structure

## Frontend Components

## Image Assets

## SVG/Icon Assets

## Reusable Styles

## Do Not Slice

## Risks and Assumptions
```

### Phase 2: Asset Manifest

Create `outputs/03-manifest/asset-manifest.json`.

Every manifest item must include:

```json
{
  "assetName": "home_banner_aw_collection",
  "page": "home",
  "assetType": "banner",
  "outputPath": "/static/images/banner/home_banner_aw_collection.jpg",
  "masterPath": "outputs/05-generated-assets/master/home_banner_aw_collection.png",
  "recommendedSize": "1380x600",
  "transparent": false,
  "generationMode": "single",
  "promptGroup": "home_banner_aw_collection",
  "promptGroupOutputCount": 1,
  "variantIndex": 1,
  "variantName": "two_models_autumn_winter_commuter_banner",
  "reuseSource": null,
  "styleDifference": "Homepage hero banner with two models on the right and large empty space on the left.",
  "frontEndUsage": "Use as banner background; overlay all text and buttons in frontend code.",
  "targetFormat": "jpg",
  "fallbackFormat": "webp",
  "preferredFileSizeKB": 500,
  "maxFileSizeKB": 650,
  "minQuality": 72,
  "qualityPreset": "balanced",
  "compressionPolicy": "generate-once-export-many",
  "previewMode": "link-only",
  "showInlinePreview": false
}
```

Allowed `generationMode` values:

- `single`: one prompt generates one final image asset
- `group_variant`: one prompt group generates multiple named variants
- `single_master`: one master image is generated and reused
- `reuse`: no generation; directly reuse `reuseSource`
- `reuse_crop`: no generation; crop/derive from `reuseSource`

### Phase 3: Prompt Groups

Create `outputs/03-manifest/prompt-groups.json`.

Prompt groups must explicitly state group name, output count, asset names, variant names, variant differences, whether assets are generated/reused/cropped, whether the group can run in parallel, and whether inline preview is disabled.

A reused prompt group is valid only when it declares the exact output count and each output asset mapping.

### Phase 4: Image Prompt File

Create `outputs/04-prompts/gpt-image-prompts.md`.

For each `single` or `single_master` asset, provide a full independent prompt.

For each `group_variant` prompt group, provide:

```md
## prompt_group_name

Output count: 6
Parallelizable: true
Preview mode: link-only

Common style:
...

### Variant 1: asset_name
Purpose:
Recommended size:
Preferred file size:
Max file size:
Variant difference:
Prompt:
Negative requirements:
```

For `reuse` and `reuse_crop`, do not create duplicate image-generation prompts; document their source in the manifest.

### Phase 5: User Confirmation

Before generating images, summarize total final assets, total master assets, total prompt groups, assets to generate, assets to reuse/crop, parallel batches, UI elements implemented by frontend, target formats, file-size budgets, preview mode, and assumptions.

Then ask:

```txt
请确认以上素材清单、复用关系、提示词、导出格式、体积预算和预览方式是否需要调整。确认后我再开始生成图片。
```

If the user asks for changes, update `page-analysis.md`, `asset-manifest.json`, `prompt-groups.json`, and `gpt-image-prompts.md`.

Only continue after the user confirms.

### Phase 6: Generate Images and Export Optimized Assets

Generate images according to `gpt-image-prompts.md`.

Then export optimized frontend assets according to `asset-manifest.json`.

Required master naming rule:

```txt
outputs/05-generated-assets/master/assetName.png
```

Required frontend export directories:

```txt
outputs/05-generated-assets/banner/
outputs/05-generated-assets/product/
outputs/05-generated-assets/category/
outputs/05-generated-assets/detail/
outputs/05-generated-assets/material/
outputs/05-generated-assets/avatar/
outputs/05-generated-assets/icons/
```

Required quality report:

```txt
outputs/07-quality-report/image-export-report.md
```

Required preview gallery:

```txt
outputs/08-preview-gallery/asset-gallery.md
outputs/08-preview-gallery/asset-gallery.html
```

The quality report must include assetName, master path, exported path, target format, final format, preferred file size, max file size, actual file size, quality value, and status.

The preview gallery must use file links and small thumbnails when possible. It must not force the conversation UI to render every image inline.

### Phase 7: Frontend Handoff

Create `outputs/06-frontend-handoff/frontend-handoff.md`.

This document must be self-contained and must support a new conversation.

It must include project goal, input design summary, final asset paths, frontend pages to implement, component breakdown, design tokens, image usage map, CSS/SVG-only UI elements, implementation order, image format policy, file-size budget, asset gallery path, and instructions for a new agent.

The handoff must start with:

```md
# Frontend Handoff

You are continuing a design-to-code workflow. Do not re-slice screenshots. Use this document as the source of truth.
```

## Local Codex Installation Check

The expected local path is:

```txt
C:\Users\PC\.codex\skills\design-to-code\SKILL.md
```

Do not nest the repository twice. This path is wrong:

```txt
C:\Users\PC\.codex\skills\design-to-code\design-to-code\SKILL.md
```

After updating the skill, fully restart Codex App so it rescans the skills directory.

## Final Response Requirement

At the end, tell the user exactly what was generated and where the files are.

Do not display every generated image inline. Provide the asset directory and gallery files instead.

If GitHub publishing is requested and repository write access is available, commit the skill files to the requested repository.

If repository creation is not available, generate a complete local repository ZIP and tell the user that the repo must be created or connected before publishing.
