---
name: web-qa
description: Generate expert-level, site-specific QA testing instructions by crawling a live website with Playwright. Use this skill whenever a user wants to create QA tests, quality assurance plans, website testing checklists, site audits, or automated testing instructions for a website. Also trigger when users mention wanting to monitor a site's health, test workflows on their site, get UX feedback, or set up recurring site checks — even if they don't use the term "QA" explicitly.
---

# Web QA Skill

Generate professional, site-specific QA instructions by crawling a real website, capturing screenshots, and producing a comprehensive markdown document that an agent can execute on a recurring schedule.

The output is designed for site owners and developers who aren't QA experts — it should be clear, visual, and actionable without requiring specialized testing knowledge.

## Workflow Overview

1. Collect the target URL and preferences from the user
2. Crawl the site with Playwright, taking screenshots along the way
3. Analyze what was found and generate tailored QA instructions
4. Deliver a markdown file with embedded screenshot references

---

## Step 1: Collect Input

Ask the user for:

**Required:**
- Target URL

**Focus area menu** — present these options and let the user pick one or more:

> Which areas should the QA plan focus on? Pick all that apply:
>
> 1. **Site Health & Uptime** — Pages load correctly, no broken links, images load, acceptable performance
> 2. **Key Workflows** — Critical user journeys work end-to-end (signup, checkout, search, etc.)
> 3. **UX/UI Review** — Layout consistency, mobile responsiveness, accessibility, visual polish
> 4. **Bug Hunting** — Form validation, error handling, edge cases, console errors, unexpected behavior
> 5. **Content & SEO** — Missing meta tags, broken structured data, content rendering issues
> 6. **Security Basics** — Mixed content, exposed sensitive info, insecure forms, open redirects
>
> Or just say "all" for comprehensive coverage.

**Optional — ask if relevant:**
- Does the site have login/authenticated areas to test? If so, ask for test credentials or whether to skip authenticated flows.
- Are there specific pages or flows they care most about?
- Any known issues or areas of concern?
- What environment is this? (production, staging, dev)

Once you have the answers, confirm the plan with the user before crawling.

---

## Step 2: Crawl the Site with Playwright

Use Playwright to browse the site like a real user. The goal is to understand the site's structure, discover pages and interactive elements, and capture visual evidence of what you find.

### Setup

Create a workspace directory for the crawl outputs:

```
<project-dir>/web-qa-output/
├── screenshots/       — All captured screenshots
└── qa-instructions.md — The final output
```

### Crawling Strategy

Start from the provided URL and explore intelligently:

1. **Load the homepage** — screenshot it. Note the navigation structure, header, footer, and main content areas.

2. **Map the navigation** — Follow primary navigation links (top nav, sidebar, footer links). Screenshot each major section landing page. Build a mental sitemap of what exists.

3. **Identify interactive elements** — Look for:
   - Forms (login, signup, contact, search, checkout)
   - Buttons and CTAs
   - Dropdowns, modals, accordions
   - Carousels, tabs, interactive widgets
   - Shopping carts, filters, pagination

4. **Test key interactions** — For important elements:
   - Click buttons and see what happens
   - Open dropdowns and modals — screenshot them in their open state
   - Submit empty forms to see validation behavior — screenshot error states
   - Try the search if one exists

5. **Check responsive behavior** — Resize the viewport to mobile width (375px) for a few key pages and screenshot the mobile layout.

6. **Watch the console** — Note any JavaScript errors, failed network requests, or warnings that appear during navigation.

7. **Respect boundaries** — Stay within the same domain. Don't follow external links. Don't submit forms with real data. If the user provided auth credentials, log in and explore authenticated areas too, but be careful not to modify any data.

### Screenshot Naming Convention

Name screenshots descriptively so they make sense when referenced in the markdown:

```
screenshots/01-homepage-desktop.png
screenshots/02-homepage-mobile.png
screenshots/03-navigation-menu-open.png
screenshots/04-signup-form.png
screenshots/05-signup-form-validation-errors.png
screenshots/06-search-results-page.png
screenshots/07-product-detail-page.png
screenshots/08-checkout-flow-step1.png
screenshots/09-footer-links.png
screenshots/10-console-errors.png
```

Number them in the order they were captured. Aim for at least 8-10 screenshots to thoroughly document the site. Focus on what's important for the QA plan — key pages, interactive elements, error states, and mobile views.

### What to Record During Crawling

As you crawl, keep notes on:
- All pages discovered (URL, title, purpose)
- Navigation structure and hierarchy
- Forms and their fields
- Interactive elements and their behavior
- Visual issues spotted (layout broken, elements overlapping, etc.)
- Console errors or failed requests
- Performance observations (slow loads, large images)
- Accessibility observations (missing alt text, poor contrast, no focus indicators)
- Anything that looks broken, inconsistent, or could be improved

These observations directly feed into the QA instructions you'll write.

---

## Step 3: Generate the QA Instructions

Based on what you discovered during the crawl, write a comprehensive QA markdown document. This is the core deliverable — it needs to be specific enough that an agent (or a human) could follow it step by step to test the site.

Read `references/qa-methodology.md` for detailed patterns and checklists for each focus area. Use the relevant sections based on what the user selected.

**Important:** When Content & SEO is a selected focus area, always include checks for `sitemap.xml`, `robots.txt`, structured data (JSON-LD/microdata), and meta tags — not just meta tags alone. These are commonly missed but critical for a complete SEO audit.

### Document Structure

Write the output to `web-qa-output/qa-instructions.md` using this structure:

```markdown
# QA Instructions: [Site Name]

**Target URL:** [url]
**Generated:** [date]
**Focus Areas:** [list of selected areas]
**Environment:** [production/staging/dev]

## Site Overview

[Brief description of what the site is, based on what you observed during the crawl. Include a screenshot of the homepage.]

![Homepage](screenshots/01-homepage-desktop.png)

[Describe the site's purpose, main navigation structure, and key content areas. Reference screenshots where helpful.]

## Site Map

[List the pages/sections discovered during the crawl, organized hierarchically. Include URLs.]

---

## Test Scenarios

[Organized by focus area. Each scenario should be specific to THIS site.]

### [Focus Area Name]

#### Scenario: [Descriptive name]

**What to test:** [Clear description of what to check]
**Steps:**
1. Navigate to [specific URL]
2. [Specific action — click this button, fill this form, etc.]
3. [Expected result]

**Why this matters:** [Brief explanation for non-QA readers]

**Reference:** [Screenshot showing the element/page being tested]
![Description](screenshots/XX-relevant-screenshot.png)

**Pass criteria:**
- [ ] [Specific, verifiable condition]
- [ ] [Another condition]

**Fail indicators:**
- [What would indicate a problem]

---

[Repeat for each scenario...]

## Known Issues Found During Crawl

[If you spotted any actual issues during the crawl, document them here with screenshots. This gives the user immediate value.]

## Recommended Test Schedule

[Based on the focus areas and site complexity, provide a frequency table like the one below. Always include this section — it's essential for actionable QA plans.]

| Test Category | Frequency | Rationale |
|---------------|-----------|-----------|
| Site health / uptime | Daily | Catch outages and broken deploys early |
| Key workflows | Weekly | Ensure critical paths stay functional |
| UX/UI review | Monthly | Catch visual regressions after updates |
| Bug hunting | Post-deployment | Verify new code doesn't introduce regressions |
| Content & SEO | Monthly | Keep SEO hygiene and catch stale content |
| Security basics | Weekly | Catch misconfigurations before they're exploited |

[Adjust the table to match the focus areas selected and the site's complexity/update frequency.]

## Appendix: Page Inventory

[Full list of all pages discovered with their URLs and a one-line description]
```

### Writing the Test Scenarios

This is where the quality of the crawl pays off. Every test scenario should reference real elements found on the site:

- Use actual URLs, not placeholders
- Reference real form fields by their labels or names
- Describe real buttons, links, and UI elements as they appear on the site
- Include screenshots using proper markdown image embed syntax: `![Description](screenshots/XX-name.png)` — never use plain text references to screenshot filenames; always use the `![]()` embed syntax so images render inline
- Write pass/fail criteria that are specific and unambiguous, using checkbox format (`- [ ] condition`)

The scenarios should be written so that an automated agent can follow them step by step using Playwright. Include CSS selectors or descriptive locators where you noticed them during crawling, as these help the executing agent find the right elements.

Think about what could go wrong with each element or flow you discovered. A professional QA engineer looks at a form and thinks: "What if I submit it empty? What if I put a script tag in the name field? What if I use a 200-character email address?" Bring that mindset to every element.

### Tone and Audience

The site owners reading this aren't QA experts. Write clearly:
- Explain *why* each test matters, not just what to do
- Use plain language — "check that the login form shows an error when you enter a wrong password" instead of "validate negative authentication flow error handling"
- The screenshots make things concrete — reference them often
- Group related tests logically so the document flows naturally

---

## Step 4: Deliver the Output

Once the QA instructions are written:

1. Let the user know the document is ready at `web-qa-output/qa-instructions.md`
2. Give a summary of what was found:
   - How many pages were crawled
   - How many test scenarios were generated
   - Any issues spotted during the crawl
   - Recommended test frequency
3. Ask if they want to adjust anything — add scenarios, change focus, go deeper on a particular area
4. Mention that the document is designed to be handed to an agent for recurring execution, and the screenshots directory needs to stay alongside it for the references to work

---

## Handling Edge Cases

**JavaScript-heavy SPAs:** Playwright handles these well since it renders the full page. Wait for content to load before screenshotting. If the site uses client-side routing, follow in-page navigation links rather than just looking at `<a>` tags in the HTML.

**Sites behind authentication:** If the user provides credentials, log in first, then crawl. Include both authenticated and unauthenticated test scenarios. Clearly mark which scenarios require auth.

**Very large sites:** Don't try to crawl everything. Focus on the main user-facing flows and the areas the user cares about. For large e-commerce sites, sample a few product categories rather than every product page.

**Sites that block bots:** If you get blocked, let the user know. They may need to whitelist the testing agent's IP or provide alternative access.

**Broken sites:** If the site is significantly broken during the crawl, document what you find — that's valuable QA information in itself. Capture screenshots of the broken states and include them as "known issues found during crawl."
