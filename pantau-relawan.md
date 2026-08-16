# Pantau Relawan — Resume Project Brief

**Role:** Tech Lead (2018–2019); Tech Lead + **sole Flutter engineer** (2023 Firebase rewrite)  
**Product:** Pantau Relawan — multi-candidate electoral **volunteer, dukungan, and real-count** SaaS (Lamacca)  
**Period:** 2018–2019 (Pemilu 2019 product) · **2023–present** rewrite (this repo last landing Jan 2026)  
**Team (do not mix)**  
- **2018–2019:** You as lead + **two other people** — **a completely different roster** from 2023. Do not name Bimo/Nadella/Nana/Irfan/zakikii on that product. Do not apply 2023 contribution %. This brief **stops** there for 2019.  
- **2023 rewrite:** You as lead + **five** junior–mid: **Bimo Eka Saputra** (PWA), **Nadella**, **Nana Sukarna** (Vue dashboard), **Irfan** (unstable rewrite + **homepage in a separate repo**), **zakikii** (last, after the platform was already stable).  
**Repo (rewrite):** `~/code/pantau-relawan-monorepo` (`lamaccatech/pantau-relawan-monorepo`)  
**2018 code:** not on this machine (Laravel + React Native, VPS). Facts below are yours.

This document is a **source brief**, not a finished resume entry. Sections 1–3 are detailed so they can be trimmed later. Section 4 has ready-to-paste bullets.

Public GitHub listing: **Pantau Relawan — Electoral Volunteer Management & Real-Time Vote Monitoring** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). Public card says **~300K** volunteers · **40** concurrent organizations. **Do not cite 40 as this SaaS’s tenant count.** That number mixes Lamacca’s white-label/basic clients with **e-Partai / PAN**, where the *same volunteer system* is embedded and used by **thousands of PAN candidates**. For **Pantau Relawan the product**, scale is **~20+ candidates** (dedicated white-labels + basic-plan tenants combined). Volunteer totals on GitHub may likewise include PAN usage — cite the profile only if you explain that split.

**Do not use** the GitHub line “8,800+ composite indexes.” In this tree `firestore.indexes.json` is **~8.8 KB** with **24** composites. That card likely mistook **file size** for index count.

e-Partai later **imports Pantau Relawan Firestore** (`pantau-relawan-by-lamacca`) into PAN’s Postgres rewrite. That is a downstream consumer, not this product’s end.

---

## 1. Project overview

### 1.1 One-sentence pitch

Pantau Relawan is a multi-tenant campaign platform: volunteer trees, supporter (dukungan) capture against DPT, surveys, field activity, and TPS real-count — originally a 2018 Laravel/RN VPS product for **8 candidates** in Pemilu 2019, rewritten in 2023 as a Firebase SaaS with **white-label Flutter apps** per paying candidate and one **basic** app for the cheaper plan (**~20+ candidates** on this product), plus Vue admin and a React/MUI PWA. The same volunteer model is also **embedded in e-Partai for PAN** (thousands of party candidates) — that is a different tenant story from Lamacca’s 20+ paid/white-label list.

### 1.2 Why it exists (two eras)

**2018–2019 — eight campaigns, one VPS (frozen facts).** Laravel dashboard, React Native, VPS, **8** Pemilu 2019 candidates. You were tech lead with **two other engineers who are not the 2023 five**. Code not in this tree. No names, no %-split, no further invention.

**2023 rewrite — many candidates, one Firebase project.** Goal: onboard the next candidate without a new Laravel deploy. **One** Firestore (`pantau-relawan-by-lamacca`), documents scoped by `id_kandidat`, custom Auth claims (`candidate`, `admin`, `superadmin`). Surfaces fork by **plan**: dedicated branded Flutter `applicationId`s for premium candidates; a shared **Pantau Relawan Basic** app that binds the candidate after login.

### 1.3 Who uses it

| Actor | Surface | What they do |
|---|---|---|
| **Relawan** | Flutter dedicated / `basic`, React PWA | Recruit downline, dukungan + KTP, aktivitas, survey, leaderboard, news/events |
| **Koordinator / admin kandidat** | Vue `dashboard` | Relawan, dukungan, real-count rekap, settings (forms, hak akses, DPT lock), CMS (berita, program, kegiatan, pengumuman) |
| **Saksi** | Mobile + dashboard | TPS real-count (`tps_2024`, C1, `real_count`) |
| **Calon / brand** | Flutter home (dedicated apps), public PWA `candidates/:id` | Profil, program, berita |
| **Lamacca ops** | Vue `superadmin` | Candidate CRUD, email/password + `superadmin` claim |

Role ladder includes koordinator pusat → wilayah → relawan / calon pemilih, plus saksi.

### 1.4 Core product ideas (2023)

**One database, many store listings.** Isolation is `id_kandidat` + rules on `request.auth.token.candidate`, **not** a Firebase project per client. Hosting targets: `dashboard`, `superadmin`, `mobile` (the **PWA**). Flutter ships to Play as **many APKs**.

**Dedicated vs basic (the commercial split).**  
- *Dedicated* (Sahabat Putri / `apps/mobile`, SIAP NAS / `nasruddin`, ChoIs, Relawan Renaldi Hawadi / `rfh`, …): hardcoded `MyConfig.candidateId`, logos, swatches, `com.pantaurelawan.<slug>`. Full home/news/kegiatan/profil.  
- *Basic* (`apps/basic`, `com.pantaurelawan.basic`): **no** baked-in candidate; candidate comes from the logged-in account. Thinner shell (no public candidate home clone). Same core: relawan, dukungan, survey, real-count, leaderboard.

Implementation is **shared Dart packages** (`libs/mobile/*`) plus **copied app directories** (not Android `productFlavors`). GitHub “Flutter package to serve dedicated apps” = that package layer + copy/white-label, not a store flavor matrix.

**Username login is custom tokens.** Volunteer accounts live in Firestore `accounts/{username}` + bcrypt; callable `login` → `createCustomToken` → client `signInWithCustomToken`. Superadmin is normal email/password + claim.

**DPT is not Firestore.** Nest **9** + Fastify search service + Elasticsearch index `dpt_raw`. Flutter hits `pantaurelawan.lamaccaservices.com/api/dpt`.

**Offline is a draft queue, not a second source of truth.** `sqflite` table `upload_drafts` + Firestore persistence. Connectivity resumes image uploads.

### 1.5 Features (rewrite)

Relawan CRUD and drafts · dukungan / NIK+DPT+KTP · aktivitas · survey + response triggers · berita / program / kegiatan · pengumuman (`broadcasts`) · saran / aspirasi · leaderboard (`user_stats`) · real-count create/update + onCreate/onUpdate rollup `real_count_stats` by wilayah · saksi sync callables · weekly/monthly recruit counters · logs.

### 1.6 Production footprint (rewrite)

| Piece | Choice |
|---|---|
| Backend | Firebase Auth, Firestore, Storage, Cloud Functions (`asia-southeast2`), Node 22 |
| Functions | **~47** exports in `main.ts`: HTTP callables, Firestore triggers, scheduled counters — **not** Nest (Nest is search-only) |
| Collections | ~24 named in `FirestoreCollection` (users, drafts, real_count, tps_2024, surveys, …) |
| Indexes | **24** composites in repo |
| Search | Elasticsearch 7-era DPT via Nest/Fastify |
| Admin | Vue **3.2**, Vue Router 4, Pinia, Bootstrap 5 |
| Volunteer web | React **18.2**, **MUI 5**, react-router 6, Zustand, TanStack Query |
| Mobile | Flutter **3.7.12** (FVM), Riverpod 2.3, go_router 9, sqflite, Freezed |
| Monorepo | Nx **15.7** — **29** `apps/` dirs, **7** lib roots (`dashboard`, `functions`, `mobile`, `pwa`, `search`, `shared`, `superadmin`), **18** Flutter `pubspec`s |

GitHub “25+ domain-driven Cloud Functions modules” is in the right ballpark (`libs/functions` has **22** domain folders + shared; **50** function library projects). Prefer “callable + trigger surface, ~47 exports.”

### 1.7 Timeline

| Period | What |
|---|---|
| **2018–2019** | Laravel + RN, VPS, **8** candidates. You + **two different people**. Stop. |
| **18 Jan 2023** | Nx monorepo `Initial commit` (you) |
| **Feb 2023** | Peak month (**687** commits) — platform scramble |
| **2023** | White-label Flutter apps, Vue dashboard, MUI PWA, Functions, ES DPT |
| **Jan 2024** | Pemilu 2024 spike (183 commits). Juniors active through **Feb 2024** |
| **2024–2026** | You remain the committer of record; small ops (e.g. PAN DPD admin script, Jan 2026) |
| **Jul 2024+** | Separate **Nuxt** dashboard/superadmin in `lamaccatech/apps/pantau-relawan` — later web, not this Nx Flutter tree. Do not merge the stories unless asked. |

### 1.8 Year-1 vs rewrite (interview table)

| | 2018 | 2023 |
|---|---|---|
| Scale intent | 8 candidates, one VPS | Many candidates, one Firebase project |
| Dashboard | Laravel | Vue 3 |
| Mobile | React Native | Flutter white-labels + `basic` |
| Volunteer web | — | React + MUI PWA |
| Auth / data | typical Laravel/MySQL on VPS | Firebase Auth custom tokens + Firestore |
| Your role | Lead + **two other people** (not the 2023 five) | Lead + **5** named juniors; **sole Flutter** |

---

## 2. Tech stack & highlights

### 2.1 Short list

**2018:** Laravel · React Native · VPS · MySQL (assumed with Laravel; do not specify engine unless you remember)

**2023:** TypeScript · Nx · Vue 3 · Pinia · React 18 · MUI · Flutter · Riverpod · go_router · sqflite · Firebase (Auth, Firestore, Functions, Storage) · NestJS/Fastify · Elasticsearch · Cloud Functions callables/triggers

### 2.2 White-label mechanics

Shared path packages (`pantaurelawan_config`, `_theme`, `_feature_*`, UI kits). Each dedicated app: new folder, `applicationId`, `MyTheme` / `MyConfig.candidateId`, assets. `basic` omits `candidateId`. This is how “one codebase, many Play listings” actually shipped — **copy + packages**, not flavors.

### 2.3 Auth & tenancy

Callable login → custom token. Claims: `{ candidate: <id> }` for relawan; `{ admin: true, candidate }` for koordinator pusat; `{ superadmin: true }` for Lamacca. Rules key off those claims + `id_kandidat` fields.

### 2.4 Real-count path

Callables create/update counts; triggers aggregate `real_count_stats` (provinsi → desa). Dashboard pages: kandidat / saksi / data-masuk / input-suara / rekapitulasi.

### 2.5 Architecture talking points

- **Tenant = claim + document field**, so ~20+ Lamacca candidates (and PAN-via-e-Partai) do not mean a Firestore project each.  
- **Plan-shaped clients** (dedicated APK vs basic) without splitting Firestore.  
- **PWA + Flutter same domain model** (relawan, dukungan, survey, real count).  
- **DPT stays in Elasticsearch** — NIK search is a different shape than volunteer trees.  
- **Offline = draft uploads**, Firestore remains canonical.

---

## 3. Contribution

### 3.1 Role, in one paragraph

**2018–2019:** Tech lead. Laravel + RN, VPS, eight candidates. Roster is **not** Bimo/Nadella/Nana/Irfan/zakikii. Do not expand this paragraph.

**2023:** Tech lead of **five** junior–mid developers, named: **Bimo** (React/MUI **PWA**); **Nadella** and **Nana** (**Vue dashboard**); **Irfan** (on the rewrite while it was still unstable, **and** the **Pantau Relawan public homepage** in **another repo** — not `pantau-relawan-monorepo`); **zakikii** last, once the app was **already stable** (git from Aug 2023). **I built the Cloud Functions / API base**; they only **tweaked** endpoints. **Sole Flutter engineer**; Bimo/zakikii Dart = **minor bugfixes**. Git: **~2,136** HEAD commits; you ~half of human commits. **Do not** rank Irfan vs zakikii by commit count in this monorepo (85 vs 21): zakikii’s volume is late copies; Irfan’s homepage work **does not even appear here**.

### 3.2 Leadership split (2023)

| Person | Layer | Bound |
|---|---|---|
| **You** | Flutter (sole), **Functions/API base**, tenancy/auth, Elasticsearch DPT service, architecture, review | All new backend capabilities start here |
| **Bimo** | **PWA** (React + MUI) | Follows APIs you already shipped; Dart only for **tiny Flutter fixes** |
| **Nadella, Nana** | **Vue dashboard** | Same: consume existing callables; small API nits if a form needs a field |
| **Irfan** (`irfanzafar.dev`) | Rewrite **while unstable** (this repo, Feb–Mar 2023) **and** the **Pantau Relawan marketing homepage** (separate repo; not in this tree) | One of the **five**. Monorepo git undercounts him — homepage commits live elsewhere |
| **zakikii** | **Last** of the five; platform **already stable** (from ~Aug 2023) | Flutter **bugfixes** / white-label copies |

After **Feb 2024** the five drop off; you keep the repo.

### 3.3 Rough contribution % (**2023 rewrite only**)

**Does not apply to 2018–2019.** That product was a different team; leave it without a pie chart.

**What to say in an interview: ~55–60% of the rewrite** (Flutter + Functions base + architecture). That is the fair workload number.

**If someone wants a line-count:** added LOC in this monorepo (locks and generated `*.g.dart` / `*.freezed.dart` excluded) is **~72% you** — round to **~70%**. Do not lead with 70%. Dashboard used a **Vue template**, so Nadella/Nana git/LOC overstates original UI work; zakikii’s **~13% added-lines** is mostly **duplicated white-label Flutter apps**, not unique features. Functions added-lines: **~81% you**. Dart added-lines: **~85% you**.

**Git on `pantau-relawan-monorepo` HEAD** (~2,136 commits; **lamaccatech 191** is merge-bot — drop it for human %).

| Person | Human commits | Added LOC (approx) | Notes |
|---|---:|---:|---|
| **You** | **~54%** | **~72%** | Commit bar is lower than LOC because you wrote dense backend + shared Flutter; juniors open many small PRs |
| **Bimo** | **~25%** | **~7%** | PWA. Commit-heavy, less unique line mass than Flutter copies |
| **Nadella** | **~12%** | **~4%** | Dashboard **on a template** — % ≠ greenfield UI |
| **Nana** | **~6%** | **~2%** | Same template dashboard |
| **zakikii** | **~3%** | **~13%** | Late; LOC inflated by cloning white-label apps |
| **Irfan** | **~1%** here | **~1%** here | Homepage is **another repo** — undercounted |

**Interview / ownership split (2023 product + homepage; cite this, not 70%):**

| Person | ~% | Why |
|---|---:|---|
| **You** | **~55–60%** | Sole Flutter, Functions **base**, tenancy, DPT, architecture. 70% LOC is the *ceiling* metric |
| **Bimo** | **~15–20%** | PWA |
| **Irfan** | **~8–12%** | Early rewrite **plus entire homepage** |
| **Nadella** | **~8–12%** | Dashboard (template-assisted) |
| **Nana** | **~5–8%** | Dashboard (template-assisted) |
| **zakikii** | **~2–4%** | Last joiner, already-stable product |

Quote: **~55–60% of the rewrite, 100% of Flutter features, almost all of the API.** Raw added lines in **this** monorepo are **~70%** (measured ~72%); say that only with the template/clone caveat. Do **not** lead with 70%, and do **not** quote zakikii git as “more than Irfan.”

### 3.4 What “owning Flutter” covered

FVM 3.7.12 · Riverpod codegen · go_router · sqflite upload queue · Firebase plugins · Freezed models · `libs/mobile` (20 Nx libraries: config, theme, features akun/dashboard/kandidat/…) · recipe for `com.pantaurelawan.*` dedicated apps vs login-scoped **basic** · DPT HTTP client to the Nest search API.

### 3.5 Outcomes you can defend

- **8 → many** candidates without a new VPS per campaign.  
- **Dedicated store apps + one basic SKU** on a **single** Firestore.  
- GitHub **~300K volunteers / 40 orgs** mixes this SaaS with **e-Partai/PAN**. For *this* product cite **~20+ candidates** (white-label + basic). PAN used the same volunteer system for **thousands of candidates** inside e-Partai — that claim belongs on the e-Partai slide, or a sentence that names both products.  
- You can walk an interviewer from **custom token login** to **`id_kandidat` rules** to **why basic cannot bake a candidate id**.  
- Honest team story: **five** juniors named (**Bimo** PWA, **Nadella/Nana** dashboard, **Irfan** rewrite + **homepage repo**, **zakikii** late); you owned **Flutter + Functions base**.

---

## 4. Suggested resume copy

### 4.1 Header line

**Tech Lead, Pantau Relawan (Lamacca)** — 2018–2019; rewrite 2023–present  
Multi-candidate volunteer & real-count SaaS. Laravel/RN VPS (8 candidates, Pemilu 2019) → Firebase + white-label Flutter (Vue admin, React/MUI PWA). Lead; **sole Flutter**. ~20+ candidates on this product.

### 4.2 Short blurb (~40 words)

Led Pantau Relawan from an 8-candidate 2019 VPS app (Laravel, React Native; **different two engineers**) to a 2023 Firebase SaaS: Vue admin, React/MUI PWA, white-label Flutter plus a basic-plan client for **~20+ candidates**, lead of **five** junior–mid (named below), **sole Flutter engineer**.

### 4.3 Medium blurb (~80 words)

Led the **2023** Pantau Relawan rewrite (Firebase SaaS). Pemilu 2019 Laravel/RN (8 candidates, **other people**) is a prior product — don’t mix teams. In 2023: one Firestore project, custom-token auth, tenant `id_kandidat`, ~47 Cloud Functions exports (**you wrote the base**), Elasticsearch DPT, Vue 3 dashboard (Nadella/Nana), React/MUI PWA (Bimo), Flutter dedicated + basic, Irfan on the unstable rewrite **and** the public homepage (other repo), zakikii after it was stable — **~20+ candidates**. Lead of **five**; **sole Flutter**. Same volunteer system later **embedded in e-Partai for PAN**.

### 4.4 Bullet options (pick 4–6)

- Led the **2018–19** volunteer product used by **8** Pemilu 2019 candidates (Laravel + React Native on a VPS). **Different teammates than 2023 — do not name the five here.**  
- Led the **2023 rewrite** with **five** junior–mid (**Bimo** PWA, **Nadella/Nana** Vue dashboard, **Irfan** build window + **homepage repo**, **zakikii** last after stable): one Firebase project; custom Auth tokens; `id_kandidat` isolation. **You implemented the Functions/API base**; teammates only made small endpoint adjustments.  
- **Sole Flutter engineer** on the rewrite: shared `libs/mobile` packages, Riverpod + go_router + offline draft queue; **dedicated branded APKs** vs **one basic app**. Teammate Dart commits were **minor bugfixes**.  
- Volunteer **web counterpart** in React + MUI PWA (**Bimo**, against your APIs) and Vue 3 ops dashboard (**Nadella/Nana**).  
- Nest/Elasticsearch **DPT** API beside Firestore volunteer data — also your backend surface, not a junior-owned service.

### 4.5 Skills line

Leadership · Laravel · React Native · Flutter · Riverpod · go_router · SQLite · Vue 3 · Pinia · React · MUI · Firebase Auth/Firestore/Functions/Storage · Elasticsearch · NestJS · Nx · TypeScript · multi-tenant SaaS

### 4.6 Things to say carefully

| Claim | Safer wording |
|---|---|
| “8,800 Firestore indexes” | **Wrong.** 24 composites. GitHub likely used **byte size**. |
| “Juniors built the backend” | **You** shipped Functions/auth/tenancy/real-count. They **adjusted** callables when a UI needed a field. |
| “Irfan barely helped / only count four” | Name **all five**. Irfan also owned the **homepage in another repo**, so this monorepo’s 21 commits undercount him. zakikii is fifth, **after stable**. |
| “Bimo owned Flutter / the whole web” | Bimo owned the **PWA**. Flutter is you. Dashboard is Nadella/Nana. |
| “40 concurrent organizations” (GitHub) | **Mixed metric.** Pantau Relawan SaaS ≈ **20+** (white-label + basic). **40** (and PAN’s **thousands of candidates**) includes **e-Partai embedding** the same system. Name the product when you quote a number. |
| “40 Firebase projects” | **One** project, many `id_kandidat` + many Android IDs. |
| “25 Nest function services” | Functions are **Firebase callables/triggers**. Nest is the **search** app. |
| “29 Nx apps” | 29 **folders**; 16 `project.json` apps; **18** Flutter pubspecs (13 copies not registered in Nx). |
| ~300K / 40 orgs | GitHub card. Split: **20+** = this SaaS; PAN/e-Partai = thousands of candidates on the *system*. |
| “I wrote 70% so put 70% on the resume” | **LOC ≈ 70%.** **Say 55–60%.** Dashboard template + cloned Flutter apps inflate/deflate the rest of the pie. |
| Mixing 2019 and 2023 people | **Different teams.** 2019 = two other engineers. 2023 = Bimo, Nadella, Nana, Irfan, zakikii. No %-split for 2019. |
| e-Partai merge | Downstream import of this Firestore; this brief is the SaaS itself. |

---

## 5. Suggested 3-bullet block

**Pantau Relawan** — Tech Lead *(2018–2019; rewrite 2023–present)*  
Multi-candidate volunteer, dukungan, and real-count platform.

- 2018–19: Laravel + React Native on a VPS for **8** Pemilu 2019 candidates (you as lead; **not** the 2023 five).  
- 2023: led **five** junior–mid (Bimo = PWA, Nadella/Nana = dashboard, **Irfan** = rewrite + **homepage repo**, zakikii last) on a Firebase rewrite; **you built the Functions base** for **~20+** white-label + basic candidates — same system later in **e-Partai for thousands of PAN candidates**.  
- **Sole Flutter engineer** (Riverpod/go_router/SQLite drafts, shared packages, dedicated + basic apps; juniors only bugfixed Dart) — reviewed PWA/dashboard and finished slipping work.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Dukungan** | Supporter / calon pemilih capture |
| **Relawan tree** | Recruiter downline |
| **Basic plan app** | Single SKU; candidate from login |
| **Dedicated app** | Baked `candidateId` + branding |
| **Custom token** | Username/bcrypt account → Firebase session |
| **DPT** | KPU voter roll (Elasticsearch, not Firestore) |

---

*Generated from `pantau-relawan-monorepo` (git + apps/libs), user history of the 2018 VPS product (~20+ rewrite tenants; PAN/e-Partai embedding), and the Pantau Relawan section of github.com/aru-shiru/aru-shiru as of 16 August 2026. GitHub “40 orgs” is split. Index-count claim is corrected. 2018 Laravel/RN tree was not available locally.*
