# Cyber Income Innovators — Content Library

This repository is a **content-only** library of six long-form MDX articles plus a taxonomy file for Cyber Income Innovators. It is framework-agnostic: bring your own static site generator or headless CMS and ingest these files as needed.

## Repository Structure

```
/content/_data/taxonomy.json      # Category metadata
/content/categories/.../*.mdx     # MDX posts with complete frontmatter
/LICENSE                          # MIT license covering textual content
/README.md                        # This guide
```

Each article corresponds to one category in the taxonomy, and filenames match the `slug` declared in their frontmatter.

## Using the Content

### Option A: Import Into an Existing MDX Pipeline

1. Copy `content/` into your project (Next.js, Astro, Remix, Eleventy, etc.).
2. Parse frontmatter with your preferred MDX loader (e.g., `@next/mdx`, `contentlayer`, `gray-matter`).
3. Map categories using `/content/_data/taxonomy.json` so navigation and feeds stay consistent.
4. Surface metadata in your templates—titles, descriptions, Open Graph fields, and disclosure banners.

### Option B: Keep Content in This Repo and Consume Remotely

1. Add this repository as a Git submodule or fetch it during CI.
2. Point your build step to the `content/` directory and compile MDX during deployment.
3. Watch for updates in `main` and rebuild when new commits land.

### Frontmatter Contract

Every MDX file includes the following required fields:

| Field | Description |
| --- | --- |
| `title` | Human-readable title for templates and SEO. |
| `slug` | URL slug; matches the filename. |
| `date` | Original publication date (ISO 8601). |
| `lastReviewed` | Last editorial review date (ISO 8601). |
| `authorName` / `authorRole` | People-first E-E-A-T signals. |
| `description` | 1–2 sentence summary for meta descriptions. |
| `category` | Category slug defined in `taxonomy.json`. |
| `tags` | Array of topical tags. |
| `ogTitle` / `ogDescription` | Social sharing metadata. |
| `canonical` | Preferred canonical URL. |
| `disclosure` | Short disclosure statement; leave empty unless affiliate links are present. |
| `aiAssistance` | Boolean indicator that AI contributed to drafting. |
| `sources` | Array of canonical references cited in the post. |
| `draft` | Boolean flag for publishing workflows. |

Downstream consumers should respect `disclosure` and `sources` so readers can evaluate trustworthiness. If you add affiliate links, set `disclosure` and ensure link markup includes `rel="sponsored"`.

## SEO & Content Quality Notes

- **People-first content:** Articles focus on real problems, step-by-step workflows, and expert commentary.
- **Intent alignment:** Length and structure follow search intent (foundational guides vs. operational playbooks).
- **E-E-A-T cues:** Author fields, last-reviewed date, sources, and internal QA sections are ready for surfacing.
- **Internal links:** Posts reference each other using relative paths. Update URLs if your site structure differs.
- **External links:** Point to authoritative resources. Keep them up to date to avoid link rot.
- **Affiliate compliance:** Add disclosures above monetized sections, apply `rel="sponsored"`, and maintain an audit log.
- **Core Web Vitals awareness:** Downstream sites should target LCP ≤ 2.5s, INP ≤ 200ms, and CLS ≤ 0.1 at p75. Avoid embedding heavy assets directly in MDX; prefer lightweight diagrams or linked media.

## Integration Tips

- **Image handling:** Add images via your site’s asset pipeline; MDX here uses text descriptions for portability.
- **Remark/rehype plugins:** Use tooling to auto-link headings, generate tables of contents, or wrap callouts.
- **Testing:** Consider linting MDX with `remark-lint` and validating links during CI.
- **Versioning:** Track changes through Git history. Consumers can watch this repo and trigger rebuilds on new commits.

## License

Content is released under the MIT License (see `LICENSE`). You may adapt and republish with attribution.

## Changelog

- **2024-06-04:** Initial release of six MDX articles, taxonomy file, and documentation.
