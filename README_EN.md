# VibeWorld

<p align="center">
  <img src="https://img.shields.io/badge/React-19-61DAFB?logo=react" alt="React 19">
  <img src="https://img.shields.io/badge/Vite-8-646CFF?logo=vite" alt="Vite 8">
  <img src="https://img.shields.io/badge/Supabase-Pro-3ECF8E?logo=supabase" alt="Supabase">
  <img src="https://img.shields.io/badge/Cloudflare-Pages-F38020?logo=cloudflare" alt="Cloudflare Pages">
  <img src="https://img.shields.io/badge/i18n-KO_|_EN-6366f1" alt="KO | EN">
  <img src="https://img.shields.io/badge/tests-500_passing-22c55e" alt="500 tests passing">
  <img src="https://img.shields.io/badge/coverage-85%25+-6366f1" alt="85%+ coverage">
  <a href="https://vibeworld.sharebible.org">
    <img src="https://img.shields.io/badge/Live_Demo-→-22c55e?style=for-the-badge&logo=vercel&logoColor=white" alt="Live Demo">
  </a>
</p>

<p align="center">
  <strong>The biggest wall to launching on Google Play — we recruit 20 testers for you</strong><br>
  <sub>Community-driven tester recruitment for indie & vibe-coded apps. Recruit 20 testers for 14 days, or become an early adopter yourself.</sub>
</p>

<p align="center">
  <a href="https://vibeworld.sharebible.org">
    <img src="https://img.shields.io/badge/🌐_Visit_VibeWorld-Join_Now-6366f1?style=for-the-badge&logo=googlechrome&logoColor=white&labelColor=4f46e5&color=818cf8" alt="Visit VibeWorld">
  </a>
</p>

---

## Why VibeWorld

Did you know? To publish a new app on Google Play, you need **20 testers to use your app for at least 14 days**. For indie developers, this is nearly impossible. Even if you rally every friend and family member, hitting 20 is a struggle. TestFlight requires real users for meaningful feedback too.

**VibeWorld was built to solve this exact problem.**

Register your app and enable "Tester Recruitment" — real users who are genuinely interested in your app's category will voluntarily apply as testers. On the flip side, you can become an **early adopter** who discovers and tests creative apps before anyone else.

> **Give & Take.** Participate as a tester for someone else's app first, then recruit testers for your own. This virtuous cycle keeps the community sustainable.

## Screenshots

<p align="center">
  <img src="screenshots/gallery.png" alt="VibeWorld Gallery" width="32%">
  <img src="screenshots/gallery-tester-filter.png" alt="Tester Recruitment Filter" width="32%">
  <img src="screenshots/board.png" alt="Community Board" width="32%">
</p>

| Gallery Main | Tester Recruitment Filter | Community Board |
|:--:|:--:|:--:|
| 22 categories, curated feed | Filter pre-release apps only | Share vibe coding tips |

<p align="center">
  <img src="screenshots/app-detail.png" alt="App Detail with Tester Banner" width="48%">
</p>

| App Detail — Store links, screenshot gallery, star ratings, community discussion |
|:--:|

## Core Features

### For Developers

| Feature | Description |
|------|------|
| **Tester Recruitment** | Register app → Enable "Tester Recruitment" → Auto-match up to 20 testers. Link your TestFlight / Google Play Internal Test URL |
| **Dual-Path Submission** | Optimized submission flows for released apps (store link) and pre-release apps (tester recruitment) |
| **Tester Dashboard** | Applicant list, emails, status management. Real-time notifications for new applicants |
| **AI Moderation** | DeepSeek V4 automatically reviews content quality. Final approval by human admin |
| **WebP Image Upload** | Client-side WebP conversion → Supabase Storage. 5MB limit, auto-resize |

### For Testers / Users

| Feature | Description |
|------|------|
| **Tester Recruitment Filter** | View only "recruiting" apps in the gallery. Experience apps before they hit the market |
| **One-Click Apply** | Hit "Join Test" on the app detail page — done |
| **Community Board** | App requests, vibe coding tips, open discussion. Markdown supported |
| **Interest-Based Feed** | Curated app recommendations based on your preferred categories |
| **Korean / English** | Full bilingual UI, app descriptions, and board posts |

### Recruit Testers from Board Posts Too

Link an app to your board post, and anyone reading it can jump directly to testing via the "Join Test" button. Share your vibe coding knowledge while naturally recruiting testers — all in one place.

## Tech Stack

| Technology | Why |
|------|----------|
| **React 19 + Vite 8** | Latest React concurrency features. Blazing-fast HMR and build times with Vite |
| **Vanilla CSS** | Full style control without a utility framework. Dark/light theme via CSS custom properties |
| **Supabase** | PostgreSQL + Auth + Storage + Edge Functions in one. Free tier supports up to 50k MAU |
| **Cloudflare Pages** | Global edge deployment. GitHub Actions CI/CD |
| **DeepSeek V4** | Anthropic-compatible API. AI moderation + community seeding + app description rewriting |
| **Vitest + Playwright** | 500 unit/integration tests + E2E critical flows. 85%+ coverage |

## Architecture

```
vibeworld/
├── src/
│   ├── components/      # 35+ components (each with tests)
│   ├── pages/           # 15 pages
│   ├── utils/           # API client, business logic (35+ utilities)
│   ├── i18n/            # ko/en translations (1000+ keys)
│   ├── constants/       # Categories, limits, blocklists
│   └── styles/          # 4 CSS files (components, content, layout, responsive)
├── supabase/
│   ├── functions/       # 13 Edge Functions (TypeScript)
│   │   └── _shared/     # JWT auth, AI rewrite handler
│   ├── migrations/      # 48 DB migrations
│   └── config.toml
├── playwright.config.js # E2E tests
└── vite.config.js       # Vite + Vitest
```

### Data Flow & Security

```
[User] → Cloudflare Pages (React SPA)
                ↓  PostgREST (search metacharacter filtering)
         PostgreSQL + RLS (auth.uid()-based access control)
                ↓
    ┌──────────┼──────────┐
  [Storage]   [Auth]  [Edge Functions]
  (WebP imgs)  (JWT)   (AI moderation, notifications, seeding)
                      (Fail-closed JWT verification)
```

## Technical Challenges & Solutions

### 1. Tester Recruitment Prerequisite — Give & Take Cycle

**Challenge**: Prevent free-riding (recruiting testers without contributing to others) while keeping the barrier to entry low.

**Solution**:
- DB trigger `has_tester_participation()`: you must have participated as a tester for another app before recruiting for your own
- `mapAppSubmitError.js` maps trigger errors to friendly i18n messages
- 20-tester cap per app + progress bar UI for visualizing recruitment status

### 2. WebP Client-Side Conversion + Supabase Storage

**Challenge**: Safely store user-uploaded images of arbitrary size/format within the free tier.

**Solution**:
- Canvas API for client-side WebP conversion (1200px max, quality 0.82, 5MB limit)
- `createObjectURL` / `revokeObjectURL` lifecycle management to prevent memory leaks
- Supabase Storage: RLS — SELECT public, INSERT authenticated, DELETE owner only

### 3. Board RLS Quality Gate

**Challenge**: Allow post editing while maintaining quality in the VIBE_TIP category.

**Solution**:
- RLS UPDATE `WITH CHECK`: INQUIRY freely editable, VIBE_TIP requires title ≥5 chars + content ≥80 chars
- `updatePost` API auto-refreshes `updated_at` + returns author profile via join
- `isAuthor` check controls edit button visibility

### 4. AI Cost Defense — Community Seed Limit

**Challenge**: Control costs when auto-generating community comments via DeepSeek API.

**Solution**:
- Hard limit of 2 AI comments per app lifetime (immutable)
- `communitySeedEligibility.js` pre-flight check
- `check_and_record_rate_limit` advisory lock prevents concurrent execution

## FAQ

<details>
<summary><strong>Can I really recruit 20 testers for free?</strong></summary>
Yes. VibeWorld connects testers with developers at no cost. Simply integrate with your Google Play Console tester management.
</details>

<details>
<summary><strong>Can I register any app?</strong></summary>
Any vibe-coded app on any platform (Android, iOS, Web) is welcome. Submit store links for released apps, or test links for pre-release apps. AI moderation + admin approval before the gallery listing goes live.
</details>

<details>
<summary><strong>What's in it for testers?</strong></summary>
Be the first to experience creative apps before they launch. Your feedback can genuinely shape the direction of an app.
</details>

<details>
<summary><strong>How long does approval take?</strong></summary>
AI auto-moderation → manual admin approval. Usually processed within 24 hours.
</details>

<details>
<summary><strong>Why Vanilla CSS?</strong></summary>
To demonstrate that productive, maintainable styling is possible without a utility framework. 4 CSS files manage all styles; themes toggle via CSS custom properties.
</details>

<details>
<summary><strong>Can I self-host?</strong></summary>
Yes. All you need is a Supabase project + a Cloudflare Pages account to run the entire stack.
</details>

---

<p align="center">
  <a href="https://vibeworld.sharebible.org">
    <img src="https://img.shields.io/badge/🚀_Start_Testing_Now-vibeworld.sharebible.org-818cf8?style=for-the-badge&logo=googlechrome&logoColor=white&labelColor=4f46e5" alt="Start Testing Now">
  </a>
  <br><br>
  <sub>Built with vibe coding • Powered by Supabase & Cloudflare</sub><br>
  <sub><a href="mailto:musicbox26@gmail.com">Contact</a></sub>
</p>

---

<p align="center">
  <sub><a href="README.md">한국어로 보기 (Read in Korean)</a></sub>
</p>
