---
name: design-to-code
description: Use this skill when converting UI design screenshots, mobile app screens, ecommerce pages, Figma exports, or visual design images into frontend implementation assets, image-generation prompts, clean image assets, optimized exported images, asset gallery links, and a frontend handoff document for uniapp, Vue3, TypeScript, and design-to-code workflows.
---

# Design to Code

## Description

Use this skill when the user provides UI design screenshots, mobile app screens, ecommerce app designs, Figma exports, product page screenshots, or visual design images and wants to convert them into frontend implementation assets, image-generation prompts, clean image assets, optimized exported images, previewable asset directories, and a frontend handoff document.

This skill is especially useful for uniapp, Vue3, TypeScript, ecommerce UI, mobile app UI, design-to-code workflows, asset manifests, GPT image prompts, clean asset rebuilding, image compression, asset gallery management, slicing strategy discussion, and frontend page implementation handoff.

## Instructions

This skill turns UI design screenshots or exported design images into a production-ready frontend workflow.

It must not directly slice screenshots as production assets. It must use the screenshots only as visual references, then separate what should be implemented by frontend code from what should be rebuilt as clean image assets.

The agent must not assume the slicing strategy automatically. The agent must discuss slicing strategy with the user before generating assets.

## Supported Outputs

The agent should produce these files during the workflow:

```txt
outputs/
  00-project-strategy/
    project-slicing-strategy.md
    project-slicing-strategy.json
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
3. Text, buttons, prices, labels, search bars, tab bars, navigation bars, forms, cards, shadows, rounded corners, dividers, status bars, quantity steppers, and tables must be implemented with frontend code unless the user explicitly requests otherwise.
4. Image assets are allowed only for Banner backgrounds, product photos, category product images, operation/promotion backgrounds, outfit photos, material/detail photos, and avatars.
5. Every image-generation prompt must require: no text, no buttons, no prices, no labels, no UI controls, no white card containers, no screenshot edges, no watermark, and no logo unless the approved slicing strategy explicitly allows embedded UI.
6. Generate each master image only once. WebP/JPG/PNG export variants are post-processing outputs, not new image generations.
7. Never regenerate an image only because the file size is too large. Compress or convert from the master image instead.
8. Do not inline-display every generated image in the conversation. Many inline images make Codex App sessions slow to reopen.
9. Show generated image results as directory paths, manifest rows, and clickable file links/gallery documents instead of rendering all images in chat.
10. Before generating images, the agent must show the user the analysis, slicing strategy, manifest summary, prompt groups, asset count, file-size budget, export strategy, and preview strategy.
11. The agent must ask the user whether anything needs adjustment before generating images.
12. If the user requests adjustments, update the project slicing strategy, analysis, manifest, prompts, export policy, and preview policy first.
13. After all assets are generated and optimized, the agent must generate `frontend-handoff.md`.
14. `frontend-handoff.md` must be self-contained so the user can paste it into a new conversation and continue frontend development.
15. High-information-density regions must enter a discussion phase before slicing decisions are finalized.
16. Users may append new slicing strategies instead of choosing only from AI-provided options.

## Slicing Strategy Rules

### Density Types

```txt
low-density
- product images
- avatars
- material/detail photos

high-density
- homepage banners
- campaign visuals
- operation cards
- marketing sections

interactive-density
- product cards
- forms
- tab bars
- search areas
- business widgets
```

### Render Modes

```txt
full-composite
- export full visual area
- may include embedded text/button

image-only
- export clean image only
- frontend renders all business UI

frontend-only
- implemented entirely by frontend code

hybrid
- image + frontend mixed rendering

split-layer
- export background/character/decorations separately
```

### Discussion Rule

If a region contains more than three of these elements:

```txt
background
character
title text
description
button
tag
promotion
interactive UI
```

then:

```txt
requiresDiscussion = true
```

The agent must generate strategy suggestions instead of directly slicing.

### Strategy Discussion Rule

The agent must:

```txt
1. identify high-information-density regions
2. provide default slicing suggestions
3. explain advantages/disadvantages
4. allow the user to modify or append strategies
5. generate the final project slicing strategy
```

Users may:

```txt
- accept
- modify
- append new strategies
- combine strategies
```

### Example Strategy

```json
{
  "area": "home_banner_01",
  "renderMode": "hybrid",
  "background": "inside-image",
  "character": "inside-image",
  "titleText": "inside-image",
  "button": "frontend",
  "supportsI18n": true
}
```

### Project Strategy File Rule

The final slicing strategy must be saved to:

```txt
outputs/00-project-strategy/project-slicing-strategy.json
outputs/00-project-strategy/project-slicing-strategy.md
```

All later files must be generated based on this strategy:

```txt
project-slicing-strategy.json
↓
asset-manifest.json
↓
prompt-groups.json
↓
gpt-image-prompts.md
↓
frontend-handoff.md
```

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

### Phase 0.5: Project Slicing Strategy Discussion

Analyze the project style and determine:

```txt
- visual restoration priority
- frontend flexibility priority
- internationalization requirements
- dynamic operation requirements
- bundle size priority
- desktop/mobile export requirements
- embedded text/button policy
```

Then identify high-information-density regions and generate slicing strategy suggestions.

The agent must not directly decide all slicing behavior.

The user may modify or append strategy rules.

Generate:

```txt
outputs/00-project-strategy/project-slicing-strategy.md
outputs/00-project-strategy/project-slicing-strategy.json
```

### Phase 1: Page Analysis

Analyze every design image and split content into frontend components, image assets, SVG/icon assets, reusable styles, and areas that must not be sliced.

The analysis must follow `project-slicing-strategy.json`.

Create `outputs/02-analysis/page-analysis.md`.

### Phase 2: Asset Manifest

Create `outputs/03-manifest/asset-manifest.json`.

Every manifest item must follow the approved slicing strategy.

Every manifest item must include:

```json
{
  "assetName": "home_banner_aw_collection",
  "page": "home",
  "assetType": "banner",
  "renderMode": "hybrid",
  "densityType": "high-density",
  "requiresDiscussion": true,
  "sourceStrategy": "slice | clean-slice | regenerate | frontend",
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
  "frontEndUsage": "Use as banner background; overlay buttons in frontend code.",
  "allowTextInsideImage": true,
  "allowButtonInsideImage": false,
  "targetFormat": "jpg",
  "fallbackFormat": "webp",
  "preferredFileSizeKB": 500,
  "maxFileSizeKB": 650,
  "previewMode": "link-only",
  "showInlinePreview": false
}
```

## Final Response Requirement

At the end, tell the user exactly what was generated and where the files are.

Do not display every generated image inline. Provide the asset directory and gallery files instead.
