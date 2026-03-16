# web-qa-skill

A [Claude Code](https://claude.ai/code) skill that generates site-specific QA testing instructions by crawling a live website with Playwright.

## What it does

Give it a URL and it will:

1. Crawl the site with Playwright, capturing screenshots along the way
2. Analyze the site's structure, pages, forms, and interactive elements
3. Generate a comprehensive QA markdown document with embedded screenshots
4. Produce step-by-step test scenarios that an agent (or human) can follow

The output is designed for site owners and developers who aren't QA experts — clear, visual, and actionable.

## Focus areas

You can pick one or more areas for the QA plan to cover:

- **Site Health & Uptime** — Page loads, broken links, images, performance
- **Key Workflows** — Critical user journeys (signup, checkout, search, etc.)
- **UX/UI Review** — Layout consistency, mobile responsiveness, accessibility
- **Bug Hunting** — Form edge cases, console errors, unexpected behavior
- **Content & SEO** — Meta tags, structured data, sitemap, robots.txt
- **Security Basics** — Mixed content, exposed info, insecure forms, headers

## Usage

Install the skill in Claude Code, then ask it to generate QA instructions for any website:

```
Generate a QA plan for https://example.com focusing on site health and key workflows
```

The skill produces a `web-qa-output/` directory with:
- `qa-instructions.md` — The full QA document with test scenarios
- `screenshots/` — Numbered screenshots referenced throughout the document

## Project structure

```
web-qa/
├── SKILL.md              # Skill definition and instructions
├── evals/
│   └── evals.json        # Eval definitions (HN, GitHub, e-commerce)
└── references/
    └── qa-methodology.md # Detailed QA checklists per focus area
```

## Eval results (iteration 1)

| Metric | With Skill | Without Skill | Delta |
|--------|------------|---------------|-------|
| Pass Rate | 100% | 93% | +7% |
| Avg Document Length | 755 lines | 578 lines | +177 |
| Screenshot Embeds | Proper `![](path)` syntax | Often plain text refs | — |

The skill's main advantage is comprehensive coverage — without the skill, the e-commerce eval missed SEO checks (sitemap/robots.txt) and test schedule recommendations.
