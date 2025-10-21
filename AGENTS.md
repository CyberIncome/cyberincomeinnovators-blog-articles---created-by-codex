# AGENTS.md

**Purpose**  
This repository is “content-as-code.” Agents generate long-form articles as Markdown files, organized by category, with a strict output contract so downstream automations can publish without manual edits.

---

## 1) Ground Rules for Agents

* Operate **content-only**. Do **not** modify build configs, CI, or unrelated files.
* Create missing folders as needed. Overwrite existing article files **atomically** (full rewrite).
* No placeholders (“TBD”), lorem ipsum, or fabricated links.
* Prefer **primary sources** (official vendor/standards docs). Tag any unsourced number with **[Estimate]**.
* Keep paragraphs ≤ 4 sentences; use scannable headings; write in a pragmatic, expert voice.
* The publishable article **must not** reference or depend on the ops metadata block.

---

## 2) Directory & Naming

* Base path for category content:

  ```
  /content/categories/<category-slug>/
  ```
* `<category-slug>`: **kebab-case** (e.g., `no-code-low-code-automation`).
* Article filenames: **kebab-case**, allowed chars `[a-z0-9-]`, collapse multiple dashes, max length 80, no trailing dash, `.md` extension  
  Example: `bubble-plus-n8n-integration-guide.md`
* If a directory does not exist, **create it** before writing.
* The **slug** in metadata must equal the filename without `.md`.

---

## 3) Single-File Output Contract

Every article file must contain **two blocks in this exact order**:

1) **Publishable article** block:

```
===ARTICLE===
...markdown-only publishable content...
===END ARTICLE===
```

2) **Operations metadata** block (**strict JSON**) for downstream automation:

```
===OPS_METADATA(JSON)===
{ ...valid JSON object... }
===END OPS_METADATA===
```

**Hard requirements**

* The ARTICLE block is **pure Markdown** (no MDX, no frontmatter, no HTML).
* The OPS block is **valid JSON** (no comments, no trailing commas).
* ARTICLE **must not** reference items from OPS.
* All links are absolute `https://` URLs with descriptive anchor text and **no tracking parameters** (strip `utm_*`).

---

## 4) ARTICLE Block — Required Structure & Order

> **Note:** The headings below are literal. Word/step limits are enforced by QA, **not** printed in headings.

```
# Title
_Subtitle (one-line promise)_

## TL;DR
- 4–6 outcome-focused bullets.

## Introduction
- Audience, problem context, promised result (2–4 short paragraphs).

## Definition
- A concise, neutral definition of the main topic (≤60 words).

## Quick Start
1. ...
2. ...
3. ...
4. ...
5. ...
(5–8 steps total)

## Core Sections
- 4–8 **core** H2 sections (use H3/H4 for additional detail).
- **Include one reusable framework/model** with explicit steps or criteria.
- **Include one worked example with explicit math**:
  - Inputs (numbers + units)
  - Formula
  - Computed result
  - Short interpretation and caveats

## Comparison Table
| Option | Best For | Not For | Limits/Quotas | Notes |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## Diagram (Mermaid)
```mermaid
...valid mermaid diagram (flowchart/sequence/state)...
```

## Checklist / SOP
1. ...
2. ...
3. ...

## Benchmarks
> Time to implement: [Estimate]  
> Expected outcome: [Estimate]  
> Common pitfalls: item; item  
> Rollback plan: one sentence

## Sources
* Title — https://example.com
* Title — https://example.com
* (≥ 7 credible sources; prefer primary docs; use recent sources when discussing limits/quotas.)

**Call to action:** One concise sentence inviting the next action.
```

**Style & constraints**
- No images embedded; use **Mermaid** for diagrams.
- No internal cross-links (added later).
- Any numeric claim **without a citation** must be tagged **[Estimate]**.
- Heading order must be hierarchical (no skipping levels).
- No empty headings or stub sections.

**Mermaid validity notes**
- Use triple backticks with `mermaid`.
- Avoid reserved words that can break parsing in certain diagram types unless quoted.
- Stick to syntax supported by current Mermaid releases.

---

## 5) OPS_METADATA(JSON) — Required Keys

The OPS JSON object must include the following keys and semantics. Generators **must** populate all keys; keep JSON minimal and valid.

```json
{
  "version": "1.0",
  "category": "<category-slug>",
  "slug": "<kebab-filename-without-md>",
  "serpPack": {
    "seoTitle": "<=60 chars, human-readable>",
    "metaDescription": "140–160 chars, human-readable",
    "slug": "<kebab-filename-without-md>"
  },
  "entities": ["Entity1", "Entity2", "..."],
  "coverageChecklist": [
    "Auth & secrets",
    "Webhooks",
    "Rate limits/quotas",
    "Error handling & retries",
    "Testing & monitoring",
    "Governance"
  ],
  "imagePlan": [
    {
      "filename": "descriptive-diagram-or-image-name.svg",
      "alt": "Human-centric alt text describing what the diagram shows",
      "caption": "Short caption clarifying the diagram’s purpose"
    }
  ],
  "downloads": [],
  "schemaPlan": {
    "@context": "https://schema.org",
    "@type": "TechArticle",
    "headline": "<article title>",
    "description": "<1–2 sentence abstract>",
    "datePublished": "YYYY-MM-DD",
    "dateModified": "YYYY-MM-DD",
    "author": { "@type": "Organization", "name": "Cyber Income Innovators" },
    "wordCount": 0,
    "about": ["Entity1","Entity2"],
    "citation": ["https://primary-doc-1","https://primary-doc-2"]
  },
  "review": {
    "lastReviewed": "YYYY-MM-DD",
    "updateWhen": [
      "Vendor API/limits change",
      "Pricing changes",
      "Security guidance updates"
    ]
  },
  "qaGate": {
    "wordCountMin": 2800,
    "hasFramework": true,
    "hasWorkedExampleWithMath": true,
    "hasComparisonTableWithLimits": true,
    "hasValidMermaid": true,
    "minSources": 7,
    "unsourcedNumbersTaggedEstimate": true
  },
  "$schema": "https://json-schema.org/draft/2020-12/schema"
}
```

**Notes**

* `category` must match the target folder’s slug.
* `slug` must match the filename without `.md`.
* `schemaPlan.wordCount` must reflect the **actual** ARTICLE word count.
* If no downloads are needed, set `"downloads": []`.
* `serpPack.metaDescription` target length is a **house style**; search engines may truncate as needed.
* Keep JSON minimal and valid; downstream automation will parse it strictly.

---

## 6) Quality Gates (must pass **before** write/commit)

* Word count ≥ `qaGate.wordCountMin` (set to the **lower bound** of the target range for the article, or use the default 2800).
* One **framework/model** section present.
* One **worked example with explicit math** (inputs → formula → result).
* Comparison table includes **Best For / Not For / Limits/Quotas** rows.
* Mermaid diagram present and syntactically valid.
* ≥ `qaGate.minSources` external sources; **prefer primary documentation**.
* All unsourced numbers tagged **[Estimate]**.
* **Definition** section present and ≤60 words.
* **Quick Start** present with **5–8** numbered steps.
* **Core Sections** limited to **4–8 H2s** (additional details under H3/H4).
* No empty headings or stub sections.
* Paragraphs ≤ 4 sentences.
* `review.lastReviewed` set to the **current date** (generation run date).

---

## 7) Safety & Scope

* Do **not** alter repository settings, CI, or unrelated files.
* Do **not** introduce frontmatter or MDX.
* Do **not** include binary assets (images, PDFs) in ARTICLE or OPS.
* Outputs must be **deterministic and idempotent**: same inputs → same file content (excluding `schemaPlan.dateModified`).

---

## 8) Example Skeleton (for generators)

```
===ARTICLE===
# Example Title
_Subtitle (one-line promise)_

## TL;DR
- ...

## Introduction
...

## Definition
...

## Quick Start
1. ...
2. ...
3. ...
4. ...
5. ...

## Core Sections
...

## Comparison Table
| Option | Best For | Not For | Limits/Quotas | Notes |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## Diagram (Mermaid)
```mermaid
flowchart LR
  A[Start] --> B[Step]
  B --> C[Result]
```

## Checklist / SOP
1. ...
2. ...

## Benchmarks
> Time to implement: [Estimate]  
> Expected outcome: [Estimate]  
> Common pitfalls: ...  
> Rollback plan: ...

## Sources
* Title — https://example.com
* Title — https://example.com

**Call to action:** ...
===END ARTICLE===

===OPS_METADATA(JSON)===
{
  "version": "1.0",
  "category": "no-code-low-code-automation",
  "slug": "example-title",
  "serpPack": {
    "seoTitle": "Example SEO Title",
    "metaDescription": "Short, compelling description within 140–160 characters.",
    "slug": "example-title"
  },
  "entities": ["Entity1","Entity2"],
  "coverageChecklist": [
    "Auth & secrets",
    "Webhooks",
    "Rate limits/quotas",
    "Error handling & retries",
    "Testing & monitoring",
    "Governance"
  ],
  "imagePlan": [
    {
      "filename": "example-diagram.svg",
      "alt": "Simple flow from start to result",
      "caption": "High-level flow."
    }
  ],
  "downloads": [],
  "schemaPlan": {
    "@context": "https://schema.org",
    "@type": "TechArticle",
    "headline": "Example Title",
    "description": "Short abstract.",
    "datePublished": "YYYY-MM-DD",
    "dateModified": "YYYY-MM-DD",
    "author": { "@type": "Organization", "name": "Cyber Income Innovators" },
    "wordCount": 0,
    "about": ["Entity1","Entity2"],
    "citation": ["https://example.com/doc1","https://example.com/doc2"]
  },
  "review": {
    "lastReviewed": "YYYY-MM-DD",
    "updateWhen": [
      "Vendor API/limits change",
      "Pricing changes",
      "Security guidance updates"
    ]
  },
  "qaGate": {
    "wordCountMin": 2800,
    "hasFramework": true,
    "hasWorkedExampleWithMath": true,
    "hasComparisonTableWithLimits": true,
    "hasValidMermaid": true,
    "minSources": 7,
    "unsourcedNumbersTaggedEstimate": true
  },
  "$schema": "https://json-schema.org/draft/2020-12/schema"
}
===END OPS_METADATA===
```
