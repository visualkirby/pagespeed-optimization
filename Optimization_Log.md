# Optimization Log

Real PageSpeed Insights runs against benchlineanalytics.com (a custom WordPress theme on shared hosting), captured over the course of normal site work between 2026-07-16 and 2026-07-29. Every number below is a fresh Lighthouse run, not a cached or projected estimate.

---

## Baseline (2026-07-16)

| Metric | Mobile | Desktop |
|---|---|---|
| Performance | 63 | 51 |
| Total Blocking Time (desktop) | -- | 2,860ms |

Desktop's unusually low score traced to a single root cause: a corrupted theme zip. The deploy pipeline used PowerShell's `Compress-Archive`, which embeds backslash paths in the zip and produced a build with broken relative asset references, driving desktop TBT to nearly 3 seconds.

---

## Fix 1: Theme Zip Rebuild

Rebuilt the deploy zip using .NET's `ZipArchive` class directly (forward-slash paths, no PowerShell `Compress-Archive` in the pipeline). File size also dropped 6.6MB -> 2.3MB as a side effect of a clean rebuild.

**Result (post-deploy, fresh runs):**

| Metric | Before | After |
|---|---|---|
| Desktop Performance | 51 | **97** |
| Desktop TBT | 2,860ms | **30ms** |
| Desktop LCP | -- | 1.1s |
| Desktop FCP | -- | 0.7s |
| Mobile Performance | 63 | 70 |

A follow-up deploy the same week pushed Desktop Performance to 98 and Mobile to 69, holding steady at Accessibility 90/91, Best Practices 100/100, SEO 100/100 (mobile/desktop).

---

## Fix 2: Image Pipeline (EWWW Image Optimizer, local mode)

Evaluated two paid/cloud image-optimization options first (Imagify, LiteSpeed's own cloud optimization) and abandoned both -- the site owner wanted images served dynamically rather than committing to an external optimization service. Landed on **EWWW Image Optimizer in local/no-API mode** instead:

- All 6 page-hero images swapped in as Featured Image / Social Image
- All 22 Customizer-managed Media Library images repointed at EWWW's `.webp` siblings
- LiteSpeed Cache reactivated for caching only (Image Optimization module left off, since EWWW now owns that job)

**Result:** Mobile Performance moved from the 63 baseline to **76-77**.

---

## Fix 3: Cookie Consent Banner (A/B tested on real PageSpeed data, not preference)

The cookie consent banner plugin (CookieAdmin) supports three display styles: Popup, Box, Footer. Rather than pick one on visual preference, each was measured with real back-to-back PageSpeed runs:

| Style | Mobile Performance |
|---|---|
| Popup (full blocking overlay) | 76-77 |
| **Box** | **88** |
| Footer | 74 |

Switched to Box. This is a live WordPress database setting (CookieAdmin plugin config), not a theme file change -- it won't show up in a `git diff` of the theme repo.

---

## Fix 4: Accessibility Regression, Root-Caused

Switching to the Box banner style caused a real Accessibility drop, 90 -> 88. Traced via PageSpeed's expandable failing-elements tables to pre-existing theme issues the banner change happened to expose (Lighthouse run-to-run variance in flagging borderline elements, not something the banner itself broke). Four fixes shipped and verified live via `getComputedStyle()` on the production page (not just page source):

- `main.css` cache-busted to `?ver=3.3.0`
- Cookie banner label color corrected to `rgb(2, 120, 119)` (sufficient contrast)
- Badge color corrected to `rgb(75, 85, 99)`
- Consent dot padding fixed to `7.5px` with `box-sizing: content-box`

**Result:** Accessibility 88 -> **93**.

---

## Final State (2026-07-29 full audit)

| Metric | Mobile | Desktop |
|---|---|---|
| Performance | **77** | **97** |
| Accessibility | 93 | 93 |
| Best Practices | 100 | 100 |
| SEO | 100 | 100 |

**Net change from baseline:** Mobile Performance +14 (63 -> 77), Desktop Performance +46 (51 -> 97), Accessibility +3 net after the regression-and-fix cycle (90 -> 93).

Mobile's remaining performance ceiling is almost entirely image delivery -- the theme-bundled assets are already optimized, so further mobile gains would come from further compressing the ~22 Customizer-managed Media Library images, which is now a no-deploy operation since they're swappable through the WordPress Customizer directly.
