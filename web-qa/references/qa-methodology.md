# QA Methodology Reference

Detailed checklists and patterns for each focus area. Use the sections relevant to what the user selected.

## Table of Contents

1. [Site Health & Uptime](#site-health--uptime)
2. [Key Workflows](#key-workflows)
3. [UX/UI Review](#uxui-review)
4. [Bug Hunting](#bug-hunting)
5. [Content & SEO](#content--seo)
6. [Security Basics](#security-basics)

---

## Site Health & Uptime

The foundation — if the site doesn't load, nothing else matters.

### Page Load Checks
- Every page in the sitemap returns HTTP 200 (or appropriate redirect)
- Pages load within acceptable time (under 3 seconds for main content)
- No broken images (img tags that return 404)
- No broken internal links (anchor tags pointing to dead pages)
- CSS and JS assets all load successfully
- Fonts load correctly (no FOUT/FOIT that persists)

### Resource Checks
- Check for mixed content (HTTP resources on HTTPS pages)
- Verify SSL certificate is valid and not expiring soon
- Check that favicon loads
- Verify Open Graph and social preview images load

### Performance Indicators
- Measure time to first meaningful paint on key pages
- Check for excessively large images (over 1MB)
- Look for render-blocking resources
- Note any visible layout shift during page load

### Error Monitoring
- Check browser console for JavaScript errors on each page
- Monitor network tab for failed requests (4xx, 5xx responses)
- Look for CORS errors that might affect functionality

### Writing Scenarios for This Area
Frame each check around a specific page found during the crawl. Instead of "check all pages load," write "Navigate to /pricing — verify it returns 200 and content is visible within 3 seconds." Include the screenshot of each page as reference.

---

## Key Workflows

The critical paths users take through the site. These are the highest-value tests because a broken workflow directly impacts the business.

### Identifying Workflows
During the crawl, look for these common workflow patterns:
- **Account creation/signup** — registration form, email verification, onboarding
- **Login/logout** — authentication, session management, password reset
- **Search and browse** — search bar, filters, pagination, category navigation
- **Purchase/checkout** — add to cart, cart management, checkout steps, payment
- **Content creation** — posting, editing, uploading, commenting
- **Contact/communication** — contact forms, chat widgets, support tickets
- **Settings/profile** — account settings, preferences, notification management

### Testing Each Workflow
For every workflow discovered, create test scenarios that:

1. **Happy path** — Complete the workflow as intended with valid inputs
2. **Empty submission** — Try to proceed without filling required fields
3. **Invalid input** — Enter wrong formats (bad email, short password, letters in phone field)
4. **Interruption** — Navigate away mid-workflow, then come back (does it save state?)
5. **Completion verification** — After finishing, verify the result (account created, order confirmed, etc.)

### Writing Scenarios for This Area
Map each workflow step by step using the actual form fields, buttons, and pages found on the site. Reference screenshots of each step. Include the exact field labels and button text so the testing agent knows precisely what to interact with.

---

## UX/UI Review

Evaluating the visual quality, consistency, and usability of the site. These tests require more judgment but can still be structured.

### Layout and Visual Consistency
- Navigation is consistent across all pages (same position, same items)
- Typography is consistent (headings, body text, font sizes don't randomly change)
- Color scheme is consistent throughout the site
- Spacing and alignment are uniform (elements don't jump around between pages)
- Images are properly sized and not stretched or pixelated
- No content overflow (text or elements spilling outside their containers)

### Responsive Design
Test at these viewport widths:
- **Desktop:** 1920px, 1440px, 1024px
- **Tablet:** 768px
- **Mobile:** 375px, 320px

For each breakpoint check:
- Navigation adapts (hamburger menu on mobile, full nav on desktop)
- Content reflows appropriately (no horizontal scrolling)
- Touch targets are large enough on mobile (at least 44x44px)
- Images scale properly
- Text remains readable (not too small on mobile, not too large on desktop)
- Forms are usable on mobile (inputs aren't tiny, keyboards match input type)

### Accessibility
- All images have alt text
- Headings follow a logical hierarchy (h1 > h2 > h3, no skipping levels)
- Links have descriptive text (not "click here")
- Color contrast meets WCAG AA (4.5:1 for text, 3:1 for large text)
- Interactive elements are reachable via keyboard (tab through the page)
- Focus indicators are visible when tabbing
- Form inputs have associated labels
- ARIA attributes are used correctly where present

### Interactive Elements
- Hover states exist for clickable elements
- Buttons look clickable (visual affordance)
- Links are distinguishable from regular text
- Dropdowns and modals open/close smoothly
- Loading states are shown during async operations
- Error states are clear and helpful

### Writing Scenarios for This Area
Screenshot the same page at multiple breakpoints. Include side-by-side comparisons in the QA document. For accessibility checks, note specific elements found during the crawl (e.g., "The hero image at the top of the homepage is missing alt text — see screenshot 04").

---

## Bug Hunting

Actively trying to break things. This is adversarial testing — looking for edge cases and unexpected behavior.

### Form Testing
For every form discovered:
- Submit completely empty
- Submit with only spaces in text fields
- Enter extremely long strings (500+ characters)
- Use special characters: `< > " ' & / \ ; -- `
- Enter script tags: `<script>alert('test')</script>`
- Use emoji in text fields
- Enter SQL-like strings: `' OR 1=1 --`
- Submit the same form twice rapidly (double-click the submit button)
- Use browser back button after submission and resubmit

### Navigation Edge Cases
- Use the browser back/forward buttons throughout the site
- Open the same page in multiple tabs
- Access a deep page directly via URL (not through navigation)
- Try URLs with trailing slashes and without
- Try adding random query parameters to URLs
- Access pages with a cleared cache

### JavaScript and Console Errors
- Open the browser console on every major page
- Look for unhandled promise rejections
- Check for deprecation warnings
- Look for 404 errors on API calls
- Check for errors that appear on interaction (clicking, scrolling, form submission)

### Visual Bugs
- Scroll to the bottom of every page (look for hidden overflow issues)
- Resize the browser window and watch for layout breaks
- Zoom the page to 150% and 200%
- Check for z-index issues (elements overlapping incorrectly)
- Look for orphaned elements (empty sections, leftover debug text)

### Writing Scenarios for This Area
Be specific about what to try and what to look for. "Submit the contact form at /contact with the name field containing `<script>alert('xss')</script>` — verify the script does not execute and the input is properly sanitized." Screenshot examples of bugs found during the crawl.

---

## Content & SEO

Making sure the site's content is correct, complete, and properly structured for search engines.

### Meta Tags and SEO
For each key page:
- Has a unique, descriptive `<title>` tag
- Has a `<meta name="description">` tag
- Has canonical URL set
- Has Open Graph tags (og:title, og:description, og:image)
- Has Twitter Card tags if applicable
- Heading structure is logical (one h1 per page)

### Content Quality
- No placeholder text ("Lorem ipsum", "TODO", "Coming soon" on launched pages)
- No broken markdown or HTML rendering in content areas
- Dates are current (copyright year, blog dates, event dates)
- Phone numbers and emails are correct and clickable
- Prices and product info are current (if applicable)

### Structured Data
- Check for JSON-LD or microdata
- Validate structured data if present
- Look for appropriate schema types (Organization, Product, Article, etc.)

### Sitemap and Robots
- Check for `/sitemap.xml`
- Check for `/robots.txt`
- Verify important pages aren't blocked by robots.txt
- Verify sitemap URLs are accessible

### Writing Scenarios for This Area
Check specific pages found during the crawl. "Verify that /about has a unique title tag that differs from the homepage title." Include screenshots of the page source or meta tags where relevant.

---

## Security Basics

Surface-level security checks that don't require penetration testing. The goal is to catch obvious issues.

### Transport Security
- All pages redirect HTTP to HTTPS
- SSL certificate is valid (not expired, correct domain)
- No mixed content warnings (HTTP resources loaded on HTTPS pages)
- HSTS header is set

### Information Exposure
- Server version headers are not exposed (X-Powered-By, Server)
- Directory listing is disabled (try adding `/` to a known directory path)
- No sensitive files accessible (`.env`, `.git`, `wp-config.php`, etc.)
- Error pages don't expose stack traces or internal paths
- Form fields for passwords use `type="password"`
- No credentials or API keys visible in page source or JavaScript files

### Form Security
- Login forms have CSRF protection
- Password fields don't autocomplete where inappropriate
- Session cookies have Secure and HttpOnly flags
- Rate limiting exists on login/signup forms (try 10 rapid submissions)

### Headers
Check for security headers on responses:
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options` or `Content-Security-Policy` frame-ancestors
- `X-XSS-Protection` (legacy but still useful)
- `Content-Security-Policy`
- `Referrer-Policy`

### Writing Scenarios for This Area
Frame checks around specific findings. "Verify that navigating to http://[domain] redirects to https://[domain] with a 301." Screenshot any security issues found during the crawl, like exposed server headers or missing HTTPS redirects.
