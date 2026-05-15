# Design to Code

## Description

Use this skill when the user provides UI design screenshots, mobile app screens, ecommerce app designs, Figma exports, product page screenshots, or visual design images and wants to convert them into frontend implementation assets, image-generation prompts, clean image assets, and a frontend handoff document.

This skill is especially useful for uniapp, Vue3, TypeScript, ecommerce UI, mobile app UI, design-to-code workflows, asset manifests, GPT image prompts, clean asset rebuilding, and frontend page implementation handoff.

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
    banner/
    product/
    category/
    detail/
    material/
    avatar/
    icons/
  06-frontend-handoff/
    frontend-handoff.md
```

## Hard Rules

1. Do not directly crop whole UI screenshots into production assets.
2. Do not turn UI controls into images.
3. Text, buttons, prices, labels, search bars, tab bars, navigation bars, forms, cards, shadows, rounded corners, dividers, status bars, quantity steppers, and tables must be implemented with frontend code.
4. Image assets are allowed only for Banner backgrounds, product photos, category product images, operation/promotion backgrounds, outfit photos, material/detail photos, and avatars.
5. Every image-generation prompt must require: no text, no buttons, no prices, no labels, no UI controls, no white card containers, no screenshot edges, no watermark, and no logo.
6. Before generating images, the agent must show the user the analysis, manifest summary, prompt groups, and asset count.
7. The agent must ask the user whether anything needs adjustment before generating images.
8. If the user requests adjustments, update the analysis, manifest, and prompts first.
9. After all assets are generated, the agent must generate `frontend-handoff.md`.
10. `frontend-handoff.md` must be self-contained so the user can paste it into a new conversation and continue frontend development.

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
  "outputPath": "/static/images/banner/home_banner_aw_collection.png",
  "recommendedSize": "1380x600",
  "transparent": false,
  "generationMode": "single",
  "promptGroup": "home_banner_aw_collection",
  "promptGroupOutputCount": 1,
  "variantIndex": 1,
  "variantName": "two_models_autumn_winter_commuter_banner",
  "reuseSource": null,
  "styleDifference": "Homepage hero banner with two models on the right and large empty space on the left.",
  "frontEndUsage": "Use as banner background; overlay all text and buttons in frontend code."
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

Prompt groups must explicitly state group name, output count, asset names, variant names, variant differences, and whether assets are generated, reused, or cropped.

A reused prompt group is valid only when it declares the exact output count and each output asset mapping.

### Phase 4: Image Prompt File

Create `outputs/04-prompts/gpt-image-prompts.md`.

For each `single` or `single_master` asset, provide a full independent prompt.

For each `group_variant` prompt group, provide:

```md
## prompt_group_name

Output count: 6

Common style:
...

### Variant 1: asset_name
Purpose:
Recommended size:
Variant difference:
Prompt:
Negative requirements:
```

For `reuse` and `reuse_crop`, do not create duplicate image-generation prompts; document their source in the manifest.

### Phase 5: User Confirmation

Before generating images, summarize total final assets, total master assets, total prompt groups, assets to generate, assets to reuse/crop, UI elements implemented by frontend, and assumptions.

Then ask:

```txt
请确认以上素材清单、复用关系和提示词是否需要调整。确认后我再开始生成图片。
```

If the user asks for changes, update `page-analysis.md`, `asset-manifest.json`, `prompt-groups.json`, and `gpt-image-prompts.md`.

Only continue after the user confirms.

### Phase 6: Generate Images

Generate images according to `gpt-image-prompts.md`.

Save generated assets according to `asset-manifest.json`.

Required naming rule:

```txt
assetName + ".png"
```

Required directories:

```txt
outputs/05-generated-assets/banner/
outputs/05-generated-assets/product/
outputs/05-generated-assets/category/
outputs/05-generated-assets/detail/
outputs/05-generated-assets/material/
outputs/05-generated-assets/avatar/
outputs/05-generated-assets/icons/
```

For transparent assets, use PNG with transparency if the image tool supports it. If transparency is not available, generate on clean neutral background and mark it for background removal.

### Phase 7: Frontend Handoff

Create `outputs/06-frontend-handoff/frontend-handoff.md`.

This document must be self-contained and must support a new conversation.

It must include project goal, input design summary, final asset paths, frontend pages to implement, component breakdown, design tokens, image usage map, CSS/SVG-only UI elements, implementation order, and instructions for a new agent.

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

If GitHub publishing is requested and repository write access is available, commit the skill files to the requested repository.

If repository creation is not available, generate a complete local repository ZIP and tell the user that the repo must be created or connected before publishing.
