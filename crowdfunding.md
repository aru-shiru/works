# Crowdfunding (Wakafyuk, Bantu Sesama, Wakaf Bangun Negeri) — Resume Project Brief

**Role:** Flutter + API (Wakafyuk, with one mid) → **sole engineer** (Bantu Sesama) → **Tech Lead** (Wakaf Bangun Negeri)  
**Product line:** Lamacca’s donation / wakaf **crowdfunding** platform, shipped three times with more complete stack each generation. Public names: **Wakafyuk**, **Bantu Sesama**, **Wakaf Bangun Negeri**.  
**Period:** Nov 2020 – Jun 2024 (three repos; last WBN landing 11 Jun 2024).  
**Lamacca public cards:** product **Crowdfunding** (Wakafyuk.id + Bantusesama.id) and work **Wakafyuk & Bantusesama (2023)**. Those years are **marketing**. Git is **2020–21** (Wakafyuk), **2022–24** (Bantu), **2023–24** (WBN). **WBN is not named** on `/products`. Not on GitHub Selected Projects. **No MAU** — do not invent.

This document is a **source brief**, not a finished resume entry. Keep the **three generations**; do not collapse them into one SaaS tenant.

---

## 1. Project overview

### 1.1 One-sentence pitch

A donor-facing Flutter app + ops dashboard + payment APIs for Islamic **wakaf** / social donations, cloned per foundation: **Wakafyuk** was the Bootstrap+React dashboard + Flutter + Cloud Functions **pilot**; **Bantu Sesama** was you **alone** (same feature set, first **Midtrans** Snap, Nest on Functions); **Wakaf Bangun Negeri** is the **full Nx** edition (Nest, Next public web, Next dashboard, Flutter) that you led with **four** other engineers.

### 1.2 Why three codebases

Same product idea (campaigns, donors, receipts, nazhir, news, events, forum), **not** one multi-tenant Firebase. Each client got its own repo and payment wiring. You reused the playbook — hard copy from Wakafyuk into Bantu, then WBN as the “we know this now” rewrite with a team.

### 1.3 The three

| Generation | Repos | Role | Team (git) | Window |
|---|---|---|---|---|
| **1. Wakafyuk (pilot)** | `~/code/wakafyuk` (dashboard, Next **web**, Functions); `~/code/wakafyuk_mobile` | You: Flutter + dashboard + Functions. Mid: **web (MUI)** + **LinkAja** | **Mohammad Luthfi Fathur Rahman** (`mollfpr`) + you. Flutter **100% you** (216 commits) | Nov 2020 – Sep 2021 |
| **2. Bantu Sesama** | `~/code/berbagi-sesama` (`lamaccatech/berbagi-sesama`) | **Sole** — Wakafyuk as reference; **first Midtrans** | You only (105 commits) | Feb 2022 – Jan 2024 |
| **3. Wakaf Bangun Negeri** | `~/code/wakaf-bangun-negeri` | Tech lead. You: Flutter + API (and still touched web/dashboard). Others: dashboard + public web | You + **Bimo**, **Nadella**, **Nana**, **zakikii** — **four** others (you remembered 3/4; git has four) | Oct 2023 – Jun 2024 |

Feature lineup (all three, richer on WBN): program/campaign, donate + payment status, nazhir, news/wakaf-pedia, events, banners, donor forum/threads, receipts/reports, Google login, share.

### 1.4 Who uses it

| Actor | Surfaces |
|---|---|
| **Donors** | Flutter; Wakafyuk/WBN also a **public Next/MUI web** (what you call the phone-shaped web / PWA) |
| **Foundation / admin / nazhir** | Dashboard (Bootstrap CRA → Tailwind React admin → Next/shadcn) |
| **System** | Firebase Auth + Firestore; payments (Permata VA + **LinkAja** on Wakafyuk web; **Midtrans Snap** on Bantu and WBN) |

Bantu Sesama is UCare / “Bantu Sesama” in the Flutter `pubspec` (repo name **berbagi-sesama**). Lamacca lists **Bantusesama.id**. Don’t fight TLDs in an interview.

---

## 2. Stack (by generation)

### 2.1 Wakafyuk — split dashboard vs mobile

| Surface | Stack |
|---|---|
| **Dashboard** | React 17 CRA, **Bootstrap 4 / Reactstrap**, Redux, Formik, Firebase JS 8 |
| **Public web** (mobile-of-the-web) | **Next 10 + Material UI v4**, SWR — **Luthfi-heavy** |
| **API** | Firebase **Cloud Functions** (TS, Node 14), Firestore, **Algolia**, Permata **VA** helper, **LinkAja** (Luthfi, Sep 2021) |
| **Mobile** | Flutter, **Provider**, Firebase (Auth, Firestore, Functions, Messaging, Storage), Gmaps, Google Sign-In. Last tag **v1.2.0+14**. **Only you** in git |

Luthfi **file-touches:** `web` 688, `dashboard` 429, `functions` 42. Yours: `dashboard` 469, `functions` 285, almost no `web`. Matches “he owned the donor website + LinkAja; you owned Flutter, dashboard, API” — he still committed a lot of dashboard too; don’t say you were the only dashboard author.

### 2.2 Bantu Sesama — sole, Midtrans, Nx

Nx 13 monorepo: `apps/admin` (React 17, React Router 6, **Tailwind 3**, React Query), `apps/functions` (**Nest 8** on Cloud Functions), `apps/berbagisesama` (Flutter, **Riverpod 1**, Dio, in-app WebView for Snap), `apps/search`. Firestore + Firebase Auth. **Midtrans Snap** + payment-notification handler — **your first Midtrans**. No second author.

### 2.3 Wakaf Bangun Negeri — “most complete”

Nx 16: **Nest 10** API, **Next 13** `apps/web` + **Next 13** `apps/dashboard` (Radix/shadcn, Tailwind, TanStack Query), Flutter (**Riverpod 2**, go_router, Dio, Midtrans webview). CASL, Firebase, Puppeteer/html-to-image, **Midtrans Snap** (pattern copied from Bantu). 302 Dart files; 989 commits.

GitHub product card live sites: Wakafyuk.id, Bantusesama.id. WBN Firebase projects `wakaf-bangun-negeri` / `-dev`.

---

## 3. Contribution

### 3.1 Wakafyuk — two people

You started both repos (28–29 Nov 2020). Flutter is **sole**. Dashboard + Functions is a split with Luthfi. **LinkAja is his** (`[web] linkaja integration`, `[functions] implementing linkaja payment`, Sep 2021). You merged/fixed Firestore donation accounting, news, waqf-report, Permata VA follow-ups. Interview: **pilot full-stack with a mid on the public web and LinkAja**; not “I integrated LinkAja.”

### 3.2 Bantu Sesama — you alone

105 / 105 commits. You cloned the *product*, not the CRA stack: Nx + Nest + Tailwind admin + Riverpod, and **Midtrans** instead of LinkAja/Permata. That Midtrans module is what WBN later reuses.

### 3.3 WBN — you led; git vs memory

You: initial commit 17 Oct 2023; PRs through Jun 2024 (285 commits).

**File-touches (approx., not a pie chart):**

| Person | Where git puts them |
|---|---|
| **You** | Spread: public web, Flutter, **API**, some dashboard — lead + Flutter/API as you said, but **not** the only Flutter author |
| **Bimo** | Public **web** then dashboard (PWA/web volume) |
| **Nadella** | **Dashboard** then web |
| **Nana** | **API** a lot (more API file-touches than you) + some dashboard |
| **zakikii** | Almost only **Flutter** (highest mobile file-touch count) |

Use resume language: **tech lead; I took WBN after two live clones, steered architecture, owned Flutter + API with the team** — not “I wrote the Flutter app by myself.” Same honesty as Pantau Relawan / zakikii. You remembered **3 or 4** others; **four names** are in git.

### 3.4 Outcomes you can defend

- Same donation product, **three** foundations, each stack a generation newer.  
- **First Midtrans** on Bantu, then production-patterned on WBN.  
- LinkAja credited to **Luthfi**.  
- Flutter from Provider (2020) → Riverpod (2022) → Riverpod 2 + go_router (2023).  
- API from “Functions listeners” → Nest-on-Functions → Nest in Nx.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead / Full-Stack, Lamacca crowdfunding (Wakafyuk, Bantu Sesama, Wakaf Bangun Negeri)** — 2020 – 2024  
Donation/wakaf platforms (Flutter, Firebase, payments). Pilot with one mid; sole rebuild with Midtrans; then lead a 5-person Nx rewrite.

### 4.2 Short blurb

Shipped Lamacca’s crowdfunding stack three times: Wakafyuk (Flutter + Bootstrap dashboard + Functions, with Luthfi on Next/MUI and LinkAja); Bantu Sesama alone (Nest, Midtrans Snap); Wakaf Bangun Negeri as tech lead (Next web + dashboard, Nest, Flutter) with Bimo, Nadella, Nana, and zakikii.

### 4.3 Medium blurb

Wakafyuk (2020–21) proved the product: Flutter donors (I owned), React Bootstrap ops dashboard and Cloud Functions (shared with Mohammad Luthfi Fathur Rahman, who built the Material-UI Next.js web and LinkAja). I then rebuilt it alone as Bantu Sesama (Nx, Nest on Functions, Tailwind admin, Riverpod, **Midtrans Snap** — my first Midtrans). Wakaf Bangun Negeri (2023–24) is the complete edition: I led four engineers, kept Flutter + API, they took the Next dashboard and donor web. GitHub/Lamacca list Wakafyuk and Bantusesama; WBN is the third, unpublished on those cards.

### 4.4 Bullets

- Three-generation donation/wakaf product (Wakafyuk → Bantu Sesama → Wakaf Bangun Negeri), not one tenant.  
- **Wakafyuk:** sole Flutter app; dashboard + Functions with a mid; **LinkAja on the public web was Luthfi**.  
- **Bantu Sesama:** sole engineer; Nx + Nest + Midtrans Snap, using Wakafyuk as the spec.  
- **Wakaf Bangun Negeri:** tech lead of five (you + Bimo, Nadella, Nana, zakikii); full Next + Nest + Flutter; Midtrans carried forward.  
- Payments: Permata VA + LinkAja (pilot) → **Midtrans Snap** (Bantu, then WBN).  
- Flutter progression: Provider → Riverpod → Riverpod 2 / go_router.

### 4.5 Skills

Flutter, Riverpod, Provider, go_router, React, Next.js, Material UI, Bootstrap, Tailwind, NestJS, Firebase (Auth, Firestore, Functions, Storage), Midtrans Snap, LinkAja *(Luthfi)*, Algolia, Nx, CASL

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I integrated LinkAja” | **Luthfi** (web + functions, Sep 2021). You owned Flutter/dashboard/API around it. |
| “First time I used a payment gateway” | **First Midtrans** (Bantu). Wakafyuk already had Permata VA + LinkAja. |
| “I was sole on Wakafyuk” | **Two people** on `wakafyuk`; **sole** on `wakafyuk_mobile` and on Bantu. |
| “3 developers on WBN” | **You + four** in git. |
| “I wrote all WBN Flutter” | You led Flutter/API; **zakikii** has the most Flutter file-touches; **Nana** heavy on API. |
| PWA service worker | Donor **Next.js web**; don’t invent Workbox unless you find it. |
| Donation GMV / user counts | **Unknown.** Cite three live brands, not money. |
| Lamacca “2023” | Cards flatten all three. Use **2020–2024**. |
| WBN Midtrans Basic auth in source | **Do not paste.** Treat as a sandbox leak to rotate, not a resume line. |

---

## 5. Suggested 3-bullet block

**Crowdfunding (Wakafyuk, Bantu Sesama, WBN)** — Tech Lead / Full-Stack *(2020 – 2024)*  
Donation and wakaf platforms for three Indonesian foundations.

- Pilot Wakafyuk: sole Flutter app; React Bootstrap dashboard and Cloud Functions with a mid who built the MUI web and LinkAja.  
- Rebuilt Bantu Sesama **alone** (Nx, Nest, Riverpod) and added **Midtrans Snap**.  
- Led four engineers on Wakaf Bangun Negeri (Next dashboard + donor web, Nest, Flutter) — the complete edition of the same product.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Wakaf** | Islamic endowment; campaigns on Wakafyuk / WBN |
| **Nazhir / nazir** | Wakaf manager |
| **Bantu Sesama** | UCare donation app; repo `berbagi-sesama` |
| **Snap** | Midtrans hosted checkout (webview) |
| **Public web / PWA** | Next/MUI donor UI (not the Flutter APK) |

---

*Generated from `wakafyuk`, `wakafyuk_mobile`, `berbagi-sesama`, `wakaf-bangun-negeri` plus lamacca.com products/work as of 16 August 2026. LinkAja attributed from Luthfi’s commits. Midtrans first appears in Bantu Sesama under your authorship.*
