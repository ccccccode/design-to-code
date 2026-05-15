# Frontend Handoff

You are continuing a design-to-code workflow. Do not re-slice screenshots. Use this document as the source of truth.

## Project Goal

Implement the frontend pages based on confirmed design analysis, generated clean image assets, and the asset manifest.

## Tech Stack

- uniapp
- Vue3
- TypeScript
- SCSS

## Source of Truth

- `asset-manifest.json`
- `prompt-groups.json`
- generated assets in `/static/images/`
- this handoff document

## Do Not Do

- Do not crop UI screenshots.
- Do not turn buttons, prices, labels, search bars, cards, or tab bars into images.
- Do not invent image paths.
- Do not change asset names without updating the manifest.

## Design Tokens

```scss
$primary: #E72B5C;
$primary-dark: #D91F51;
$text-main: #151515;
$text-sub: #666666;
$text-light: #999999;
$bg-page: #F8F7F4;
$bg-card: #FFFFFF;
$border: #E8E3DE;

$page-padding: 32rpx;
$radius-card: 28rpx;
$radius-image: 20rpx;
$shadow-card: 0 12rpx 36rpx rgba(30, 20, 10, 0.06);
```

## Asset Usage Map

| Page | Asset | Path | Usage |
|---|---|---|---|
| home | home_banner_aw_collection | /static/images/banner/home_banner_aw_collection.png | Hero banner background |

## Component Plan

- ProductCard.vue
- AppTabBar.vue
- SearchBar.vue
- SectionHeader.vue
- PromotionTile.vue
- QuantityStepper.vue
- PriceText.vue

## Implementation Order

1. Create global styles and design tokens.
2. Create reusable components.
3. Implement home page.
4. Implement category page.
5. Implement product detail page.
6. Implement cart page.
7. Implement profile page.
8. Implement login/register pages.

## New Agent Instruction

Use this handoff as the source of truth. Start by generating the global styles and reusable components. Then implement pages one by one. If an image asset is missing, list the missing asset instead of changing the path.
