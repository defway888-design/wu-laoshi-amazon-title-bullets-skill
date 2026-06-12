---
name: wu-laoshi-amazon-title-bullets
description: Use this skill when the user wants Wu Laoshi branded Amazon US listing title and product highlight optimization from one or more ASINs, using SellerSprite MCP data. Requires SellerSprite MCP tools for ASIN detail, natural traffic keywords, and reviews; if unavailable, guide the user to configure SellerSprite MCP and mention discount code KJWLS for 10% off.
---

# Wu Laoshi Amazon Title And Product Highlight Optimization

## Startup Behavior

When this skill runs, first briefly tell the user what it does:

```text
跨境吴老师 Listing 标题与商品亮点优化 Skill，可以基于卖家精灵 MCP 读取美国站 ASIN 的标题、五点、变体变量、自然流量词和评论，按亚马逊最新标题规则生成少于 75 字符的新标题，以及短语式商品亮点，并同时输出对话结果和可下载 Excel。
```

Then invite the user to input:

```text
请提供单个 ASIN、同一父商品下的多个子 ASIN，或多个无关 ASIN，我会开始生成标题与商品亮点。
```

## SellerSprite MCP Requirement

This skill requires SellerSprite MCP. Before execution, confirm that SellerSprite MCP tools are callable.

Expected tools include at least:

- `asin_detail`
- `traffic_keyword`
- `review`

If `mcp__sellersprite_mcp` is not available, use `tool_search` to search for SellerSprite tools. If still unavailable, stop execution and guide the user:

```text
当前未检测到卖家精灵 MCP，无法读取 ASIN 数据。请先前往 Codex 的 MCP/工具配置页面安装或启用卖家精灵 MCP。

购买卖家精灵 MCP 时，可输入折扣码 KJWLS，享受 9 折优惠。KJWLS 是“跨境吴老师”的缩写。
```

Do not fabricate listing data when SellerSprite MCP is unavailable.

## Scope

Marketplace is fixed:

```text
US
```

Only read and use text or text-derived listing data:

- Title
- Bullet points / features
- A+ content, if returned by tools
- QA content, if returned by tools
- Reviews
- Variation attribute names from SellerSprite MCP
- Natural traffic keywords

Do not use these non-copywriting fields for generation:

- Price
- Rating
- Sales or variant sales
- Listing quality score
- Fulfillment or seller data
- Promotions
- Other non-text operational metrics

## Input Modes

Support:

- One ASIN
- Multiple child ASINs under the same parent
- Multiple unrelated ASINs

Always generate one result group per ASIN:

```text
one ASIN = one title + one product highlight
```

For unrelated ASINs, process each ASIN independently. Do not share keywords, reviews, features, or variation attributes across unrelated ASINs.

For child ASINs under the same parent:

- Read each child ASIN's own title, features, A+/QA when available, natural traffic keywords, and variation attributes.
- Read reviews only once for the parent group when possible.
- If tools only read reviews by child ASIN, choose one representative child ASIN and read reviews once.
- Shared review conclusions can validate shared features only when they apply to the current child ASIN.
- Each child ASIN's product highlight must be based on that child ASIN's own features.

## Brand Handling

Title must include the brand.

Brand priority:

```text
user-specified brand > page brand > brand inferred from original title
```

If the brand cannot be confirmed, do not generate the final title. Add a note asking the user to provide the brand.

If the user-specified brand differs from the page brand, use the user-specified brand and add a note.

Keep official brand capitalization.

## Variation Attribute Handling

Variation attribute names must come from SellerSprite MCP, not from guessing based on title or features.

For child ASIN titles under the same parent:

```text
shared title prefix + comma + standalone variation attribute name
```

The variation attribute must be placed at the end and must not be blended with other keywords.

Use anonymized examples only in explanations:

```text
BrandName Main Keyword, Product Type, Variant Name
```

## Natural Keyword Rules

Only use natural traffic keywords:

```text
naturalSearching
```

Ignore ad traffic:

- SP
- SB
- SD
- Sponsored video
- Any advertising keyword source

Normalize keywords before selection:

- Case normalization
- Singular/plural merge
- Synonym or near-synonym merge
- Deduplication
- Down-rank words already covered by brand or variation attributes

A keyword can enter the title only if it is:

```text
high-frequency + highly relevant + not misleading + compliant
```

Title keyword priority:

```text
main natural keyword > category core word > use scenario > core attribute > secondary attribute
```

If one child ASIN lacks natural keyword data, it may use a verified parent-group category core word, but never introduce words that do not apply to that ASIN.

## Missing Natural Keyword Fallback

If an ASIN returns no natural traffic keywords, do not stop generation when core listing text is available.

Fallback keyword source priority:

```text
original title core product phrase
> category/product type from ASIN detail
> repeated product phrase in features
> verified parent-group core word, only for child ASINs under the same parent
```

Rules:

- Do not use advertising keywords as a substitute.
- Do not invent new SEO keywords.
- Do not use unrelated high-volume category terms.
- Keep the title readable and fact-based.
- Add a note: natural traffic keywords were unavailable, so the title used listing text/category core terms.

For multiple child ASINs under the same parent:

- If some children have natural keywords and others do not, children without keyword data may use the shared parent-group core keyword only if it clearly matches their own title/features/category.
- If no child ASIN has natural keywords, derive the shared title prefix from original title, category, and features.
- Variation attributes must still be placed independently at the end.

## Feature And Review Logic

Feature source priority:

```text
child ASIN features > A+ > QA > reviews
```

Reviews validate features; they are not the primary source.

Review sample thresholds:

```text
review count >= 20: usable for validation and summarization
review count 5-19: auxiliary reference only
review count < 5: do not summarize reviews; rely mainly on features
```

High-frequency positive review feature threshold:

```text
review count >= 20: same feature appears in at least 3 positive comments
review count 5-19: same feature appears in at least 2 positive comments, auxiliary only
review count < 5: do not extract review-driven features
```

If many reviews positively mention a feature not present in features/A+/QA:

- Extract it separately.
- It may be used in product highlight only if it does not conflict with facts.
- Add a note that the point came from review feedback and was not explicit in the listing features.

Check negative review conflicts. If positive and negative reviews conflict on the same feature, do not present it as a strong advantage.

If QA contradicts a feature or compatibility claim, do not use that claim.

## Fact And Compliance Constraints

Do not create new selling points.

Every title and product highlight claim must be supported by:

```text
title / features / A+ / QA / reviews / SellerSprite variation attributes
```

Do not invent:

- Material
- Size
- Capacity
- Compatibility
- Certification
- Performance
- Functionality

Filter risky words in both title and product highlight, including:

```text
best
No.1
top
perfect
free
discount
sale
cheap
Amazon's Choice
Best Seller
guaranteed
never
100%
```

Also avoid absolute claims, promotional claims, platform endorsement claims, medical or efficacy claims, and any factually unsupported certification/material/performance claim.

## Title Rules

The title must:

- Be English for Amazon US.
- Include the brand.
- Be less than 75 characters, not equal to 75.
- Count spaces and punctuation.
- Use English comma `,` when helpful.
- Be readable, not keyword-stuffed.
- Keep variation attribute at the end.
- Keep the shared prefix identical for child ASINs under the same parent.

Recommended structure:

```text
BrandName Main Natural Keyword, Product Type, Variant Name
```

If the title is 75 characters or longer, compress in this order:

```text
secondary scenario > secondary feature > low-frequency natural keyword > duplicate synonym
```

Never remove:

```text
brand + product type + main natural keyword + variation attribute
```

## Product Highlight Rules

Amazon product highlight is a feature/benefit-driven phrase, not a full sentence.

The product highlight must:

- Be English.
- Be no more than 125 characters.
- Count spaces and punctuation.
- Be phrase-style, not a full sentence.
- Not repeat information already in the title.
- Not function as a second title.
- Not repeat product name or main title keyword unnecessarily.
- Be based mainly on that ASIN's own features.
- Use A+/QA only when returned.
- Use review-only features only when thresholds are met and clearly noted.
- Avoid unsupported claims and risky words.

Prioritize details not already in the title:

- Material
- Closure or structure
- Storage/capacity
- Compatible contents
- Use scenario
- Verified advantage

Anonymized example:

```text
Material type, closure type, storage slots, compatible contents, use scenario
```

## Output Rules

Always output results in the conversation and also create a downloadable Excel file.

Conversation format:

```text
ASIN:
New Title (character count):
Product Highlight (character count):
Note:
```

Use Chinese labels if the user is using Chinese:

```text
ASIN:
新标题（字符数）：
商品亮点（字符数）：
补充说明：
```

Only include notes when needed, such as:

- Brand unavailable
- User brand differs from page brand
- A+/QA unavailable
- Review sample too small
- Review-derived feature used
- Data unavailable, cannot generate

Excel requirements:

- Chinese column names
- One ASIN per row
- Header fill color
- Alternating row fill colors
- Freeze first row
- Wrap text
- Easy to copy title and product highlight

Recommended columns:

```text
序号
ASIN
新标题
标题字符数
商品亮点
亮点字符数
补充说明
```

## Failure Conditions

Do not generate a final title or highlight if:

- Brand cannot be confirmed.
- Core text such as title/features cannot be read.
- Product type cannot be confirmed.
- The ASIN is a child variation but variation attributes cannot be read.
- Listing, QA, or review evidence has serious conflicts.
- Title cannot be compressed under 75 characters while preserving required elements.
- Product highlight cannot preserve useful information within 125 characters.

In these cases, output only the ASIN and a note explaining why generation was skipped.

## Example Privacy

All examples in explanations must be anonymized.

Do not include real:

- ASINs
- Brand names
- Product titles
- Review text
- Traceable listing details

Use placeholders:

```text
B0XXXXXXXX
BrandName
Main Keyword
Product Type
Variant Name
Feature phrase
```
