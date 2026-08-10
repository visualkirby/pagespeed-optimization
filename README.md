# PageSpeed Optimization

**PageSpeed Insights | Website Performance | Case Study**

[![🌐 View Live Site](https://img.shields.io/badge/🌐_View_Live_Site-03a6a5)](https://benchlineanalytics.com)

A real performance optimization case study on benchlineanalytics.com, a custom WordPress theme on shared hosting. Every number in this repo is a fresh PageSpeed Insights / Lighthouse run captured during actual site work, not a projected or cherry-picked estimate. Mobile Performance moved 63 -> 77 and Desktop Performance moved 51 -> 97 across four concrete, root-caused fixes.

---

# Project Overview

Most PageSpeed advice stops at generic checklists (compress images, minify CSS). This case study documents what actually moved the needle on one real, live site: a corrupted deploy zip driving desktop TBT to 2.8 seconds, an image pipeline decision made after evaluating and rejecting two paid alternatives, an A/B test of three cookie-banner display styles run on real PageSpeed data instead of visual preference, and an accessibility regression traced to its actual root cause and fixed.

[![📋 Full Optimization Log](https://img.shields.io/badge/📋_Full_Optimization_Log-05467c)](Optimization_Log.md)

---

# What's Inside

1️⃣ **Baseline audit** — Mobile Performance 63, Desktop Performance 51, with desktop's score traced to a single root cause: a theme zip corrupted by PowerShell's `Compress-Archive` embedding backslash paths.

2️⃣ **Theme zip rebuild** — Switched the deploy pipeline to .NET's `ZipArchive` (forward-slash paths). Desktop Performance 51 → 97, Total Blocking Time 2,860ms → 30ms.

3️⃣ **Image pipeline decision** — Evaluated and rejected two paid/cloud image-optimization options in favor of EWWW Image Optimizer in local mode, keeping images served dynamically. Mobile Performance 63 → 76-77.

4️⃣ **Cookie banner A/B test** — Measured all three CookieAdmin display styles (Popup, Box, Footer) with real back-to-back PageSpeed runs rather than picking on preference. Box style won at 88 vs Popup's 76-77 and Footer's 74.

5️⃣ **Accessibility regression, root-caused** — The Box banner switch dropped Accessibility 90 → 88. Traced via Lighthouse's failing-elements tables to pre-existing contrast/sizing issues, fixed, and verified live via `getComputedStyle()` on the production page. Accessibility 88 → 93.

---

# Key Metrics

| Metric | Baseline (2026-07-16) | Final (2026-07-29) |
|---|---|---|
| Mobile Performance | 63 | **77** |
| Desktop Performance | 51 | **97** |
| Accessibility (mobile/desktop) | 90 / 91 | 93 / 93 |
| Best Practices | 100 / 100 | 100 / 100 |
| SEO | 100 / 100 | 100 / 100 |

---

# Weekly Tracking

Ongoing PSI snapshots captured weekly, separate from the fixed-point case study above. Real fluctuations get logged here, not smoothed over -- including regressions.

| Date | Mobile Perf | Desktop Perf | Accessibility (m/d) | Best Practices | SEO | Notes | Screenshots |
|---|---|---|---|---|---|---|---|
| 2026-08-10 | **64** | 97 | 94 / 95 | 100 / 100 | 100 / 100 | Mobile Performance down from the 77 documented 2026-07-29 -- real ~13-point regression, not yet root-caused. Desktop holds steady. | [Mobile](screenshots/psi-mobile-2026-08-10.png) · [Desktop](screenshots/psi-desktop-2026-08-10.png) |

---

# Tools Used

- Google PageSpeed Insights / Lighthouse
- WordPress (custom theme, Namecheap Stellar shared hosting)
- LiteSpeed Cache
- EWWW Image Optimizer (local mode)
- CookieAdmin (consent banner)
- .NET `ZipArchive` (deploy pipeline)

---

# Author

**Sawandi Kirby**

Data Analytics & Business Intelligence
Benchline Analytics - Data intelligence for organizations that mean business.

- GitHub: https://github.com/visualkirby
- LinkedIn: https://linkedin.com/in/sawandi-kirby
- Kaggle: https://kaggle.com/sawandikirby
