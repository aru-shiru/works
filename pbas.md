# PBAS Website & Career Information System — Resume Project Brief

**Role:** Full-stack (recruitment / Functions / i18n); homepage & CMS pair with Luthfi  
**Client / product:** **PT Patra Badak Arun Solusi (PBAS)** — Pertamina-group energy services (EPC, commissioning, O&M, plant services, training). Corporate **homepage** plus a **career / applicant** module.  
**Period:** **29 Sep 2020 – 1 Feb 2022** (git). Luthfi first commit 29 Sep 2020; you start **7 Oct 2020** (`applicant pages`). Your last commit **21 Jan 2022**; last repo activity Luthfi merge **1 Feb 2022**. Lamacca `/work` year: **2020** (first commit year).  
**Team:** **Muhammad Dzul Arsyil** + **Mohammad Luthfi Fathur Rahman** (`mollfpr` / `luthfi` / `luthfi.ufi14@gmail.com`) — same pairing as Journaltime / Wakafyuk. `lamaccatech` / `pbasolusi` = GitHub merges, not feature authors.  
**Repo:** `~/code/pbas-web` (`pbasolusi/pbas-web`) — not under `lamaccatech/`

This document is a **source brief**, not a finished resume entry.

Public listing: Lamacca [work](https://lamacca.com/work) **PBAS Website & Career Information System (2020)** — “corporate website … careers module that runs the company’s new-employee recruitment process end to end.” **Not** on `/products`. **Not** on GitHub Selected Projects. **No MAU / applicant counts** in git — do not invent. HRD dashboard reads `stats/counter` (`openPositions`, `applicants`) at **runtime**.

Your own one-liner is right: **homepage, with additional recruitment.** Git split: **Luthfi led the public site + CMS**; **you owned the career pipeline** (applicant/HRD UI, callables, Algolia career/users, `show` flag).

---

## 1. Project overview

### 1.1 One-sentence pitch

PBAS’s public Next.js site (company, services, media, `/career`) is fed by a CRA admin CMS; applicants register, apply to listed jobs, and HRD walks them through a five-stage funnel ending in `employee`.

### 1.2 Why it exists

Corporate brochure site for a 1996-era energy-services company (copy also says “56+ years” — **do not reconcile**). Extra product: **HRD open-positions + applicant tracking**, not a jammed Google Form. Root `readme.MD`: `pbas-web` = homepage, `pbas-admin` = admin for homepage content, `functions` = Firebase. Career was wired onto that homepage (`integrate career to homepage`, 8 Oct 2020).

### 1.3 Surfaces

| Actor | Surface |
|---|---|
| **Public** | Next.js `pbas-web`: `/`, about, services, projects, news/event/magazine/annual-report, **`/career`** (open jobs `deadlineTimestamp >= now` and `show == true`), `/contact-us`. EN + ID (`next-i18next`). Vercel on GitHub push (2–3 min; **no URL in repo**). |
| **CMS (admin / editor)** | CRA `pbas-admin`: news, event, annual report, magazine, contact, projects, journey, clients, services, officials, awards. Draft.js rich text. |
| **HRD** | Same admin: Career → Open Positions, Applicants. Roles `admin` / `editor` / `hrd`. Career writes: `admin` or `hrd`. |
| **Applicant** | Register + dashboard + apply. Roles `["user","applicant"]`. On hire: applicant dropped, `["user","employee"]`. |
| **System** | Firestore + Functions `asia-northeast1` + Storage + Algolia. Firebase project `pbas-eb262` (earlier seed `pbas-e2abe`; you: `change firebase project`). |

Firestore (rules): `annualReport`, `client`, `contactUs`, `event`, `howWeAddValue`, `journey`, `magazine`, `news`, `project`, `award-and-certification`, `career`, `officials`, `services`, `users`, `stats`.

### 1.4 Recruitment funnel (shipped)

Constants + callable titles:

1. **Administrasi** → 2. **Wawancara** → 3. **Psikotes** → 4. **Tes Kesehatan** → 5. **Wawancara Akhir**

Callables in `functions/src/http/applicant.ts`: `registerApplicant`, `applyForPosition`, `promoteToInterview`, `promoteToPsychologicalTesting`, `promoteToMedical`, `promoteToOffer`, `makeEmployee`, `reject`. Apply writes `applications/{position}` with `stage1`…`stage5`, `currentStage: 1`. Notifications: Undangan Wawancara / Psikotes / Tes Kesehatan / Wawancara Akhir. Scheduled job `functions/src/scheduled/carrer.ts` (**typo in filename**) drops unfilled apps after deadline.

---

## 2. Stack

Not Nx. Not Laravel. Three packages in one git repo.

| Surface | Stack |
|---|---|
| **Public** | **Next 10.2** Pages Router, React 17, Firebase JS 8, Algolia 4 + InstantSearch, next-i18next 10, SWR 0.5, **Bootstrap 4**. `getStatic*` revalidate **300s**. Next APIs: `/api/career`, news, event, magazine, contact-us |
| **Admin** | CRA **react-scripts 3.4**, React 16.13, Redux, react-router-dom 5, Formik, Firebase 8, Algolia |
| **Functions** | Node **14**, TypeScript, firebase-functions 3.14, firebase-admin 9, Algolia. HTTP: applicant, users, `resyncAlgolia`. Firestore triggers: news, magazine, event, annual-report, career, users, notifications. Description generator + Algolia recorder (same family as Journaltime) |

UI leftovers: DexignZone / ThemePunch Revolution under `pbas-web/src/plugins` — **do not claim you wrote that plugin dump** (Luthfi **~1976** path-touches there vs your 162). Unused-looking admin kit: kanban, Apex/Chartist/ECharts, Leaflet, Google Maps.

---

## 3. Contribution

**296 commits.** Human: Luthfi aliases **~156**, you **134**.

**Commits touching trees:**

| | `pbas-web` | `pbas-admin` | `functions` |
|---|---|---|---|
| **You** | 68 | 44 | **23** |
| **Luthfi** | **104** | 47 | **3** |

**Keyword path-touches:** you `career` 32 / `applicant` 53 / `algolia` **9**; Luthfi 14 / 11 / **0**. `functions/src/http`: you **17**, Luthfi **1**. Locales `pbas-web/public/locales`: **you 29, Luthfi 0**.

Luthfi homepage-titled career commits: `[web] update career` (6 Oct 2020) and `feat: career page warning modal` (21 Jun 2021). `career.js` existed in his first drop; **you built the live job/apply/HRD path** (`applicant pages` → `integrate career to homepage` / `add applicant registration` → `finished hrd necessary UI` → `open positions and dashboard for applicant` adding `applicant.ts` → `career alpha version` → `toggle show for career` → career i18n → Algolia recorder).

### 3.1 You

- Applicant self-register, apply-once-per-position, five-stage promotions, hire → employee, reject.  
- HRD open-positions + applicants UI; public `/career` wired to homepage.  
- Functions/HTTP for applicants; Algolia recorder update; career `show` flag.  
- EN/ID locale files.  
- Firebase project cutover (`pbas-eb262`).

### 3.2 Luthfi

- First commit and the **corporate homepage**: overview, projects, journey, clients, “how we add value,” media, plugin/slider vendor, imagery.  
- CMS CRUD for those content types; most `pbas-admin` volume.  
- Tiny Functions (user HTTP, description generator).  
- Career **warning modal** on the public page — not the pipeline.

Interview line: **corporate Next site with Luthfi; I added end-to-end recruitment (HRD + applicant + Functions).** Do not say you designed the brochure homepage alone. Do not say Luthfi owned ATS.

### 3.3 Outcomes you can defend

- Brochure site **plus** a real ATS (five stages, Storage letters, Algolia, scheduled cleanup) — matches Lamacca copy without inflating headcount.  
- Same pairing as Journaltime: he CMS/public, you Functions-heavy product module; here the module is **hiring**, not Flutter.  
- Vercel-from-GitHub for the public site; Firebase for CMS/recruitment data.

---

## 4. Suggested resume copy

### 4.1 Header

**Full-Stack, PBAS website + career system (with one mid)** — Oct 2020 – Feb 2022  
Corporate Next.js site and Firebase applicant pipeline for PT Patra Badak Arun Solusi.

### 4.2 Short blurb

With Luthfi I shipped PBAS’s homepage and CMS; I owned the career add-on — open positions, applicant accounts, and a five-stage HRD funnel (administrasi through wawancara akhir) on Firebase Functions.

### 4.3 Medium blurb

PBAS needed a company site and a way to take applications. Luthfi led the Next 10 / Bootstrap brochure and the CRA content admin (news, projects, journey, clients). From Oct 2020 I added recruitment: public `/career`, applicant register/apply, HRD dashboards, Algolia, and callables that promote or reject through five stages and convert a hire to `employee`. Site year **2020**. No applicant volume in the repo.

### 4.4 Bullets

- Pair-built PBAS corporate web: Luthfi homepage/CMS; I the career/ATS add-on.  
- Five-stage pipeline (Administrasi → Wawancara → Psikotes → Tes Kesehatan → Wawancara Akhir) via HTTPS callables + HRD UI.  
- Next.js public `/career` filtered by deadline + `show`; applicant dashboard in the same Firebase project.  
- Algolia sync and i18n locales (EN/ID) on the public site.

### 4.5 Skills

Next.js, React, Redux, Bootstrap, Firebase (Auth, Firestore, Functions, Storage), Algolia, TypeScript, next-i18next, Formik, Vercel (hosting from GitHub)

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I built the PBAS website” | **Two people.** You recruitment + Functions; **Luthfi homepage/CMS**. |
| “ATS / Workday-class HRIS” | Five-stage Firestore funnel + files. No payroll, no e-sign. |
| “2023 project” | Old marketing year. **Site year is first commit: 2020.** |
| Applicant / hire counts | **Unknown.** Only live `stats/counter`. |
| ThemePunch / Revolution slider | **Vendor** in Luthfi’s early dump. |
| “Patra Logistik” | **Different `/work` card**; WordPress; you **skip**. |
| Firebase `apiKey` in singletons | **Do not paste.** |

---

## 5. Suggested 3-bullet block

**PBAS Website & Career Information System** — Full-Stack with one mid *(2020 – 2022)*  
Corporate site and recruitment for PT Patra Badak Arun Solusi.

- Homepage and content CMS led by Luthfi (Next.js + CRA admin).  
- I added `/career`, applicant accounts, and a five-stage HRD funnel on Firebase Functions.  
- Algolia search sync, EN/ID locales, Vercel for the public site.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **PBAS** | PT Patra Badak Arun Solusi (`pbasolusi.com`) |
| **HRD** | Admin role for Career → Open Positions / Applicants |
| **ATS** | Informal: this applicant funnel, not a product name in code |
| **pbas-admin** | CMS + HRD + applicant dashboards (one CRA app) |

---

*Generated from `~/code/pbas-web` and lamacca.com/work as of 16 August 2026. Split from git shortlog and path-touches; Luthfi = mollfpr / luthfi. No traffic or applicant numbers in-repo.*
