# e-Partai / simPAN — Resume Project Brief

**Role:** Sole Full-Stack Engineer (product, architecture, implementation, client/party ops, migration)  
**Company / product:** simPAN — Sistem Informasi **Partai Amanat Nasional** (PAN). Rewrite branded **e-Partai** so the same codebase can be duplicated for other parties.  
**Period:** November 2021 – present. Firebase simPAN from **2021**; cloud-agnostic rewrite **August 2025 – present** (as of August 2026).  
**Team:** Sole developer on the original simPAN **and** the rewrite  
**Repos:** predecessor `~/code/simpan` (`lamaccatech/simpan-monorepo`); rewrite `~/code/e-partai`  

This document is a **source brief**, not a finished resume entry. Sections 1–3 are detailed on purpose so they can be trimmed later. Section 4 has ready-to-paste resume bullets.

Public GitHub listing: **e-Partai — Political Party Management System** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). Public metrics on that card: **1.6M** registered members and volunteers · **50K+** Play Store installs · **~1–2K** daily active users. The migrator comments **~1.45M** simPAN anggota as the Firestore batch size — cite GitHub for the round number; do not invent extras.

The GitHub card says the rewrite ingested **simPAN + Pantau Relawan**. That is accurate in this repo even if interviewers only say “e-Partai / simPAN.” PAN used the **Pantau Relawan volunteer system for thousands of candidates**; that scale is *this* embedding, not Lamacca’s ~20+ white-label/basic Pantau Relawan tenants. See `pantau-relawan.md`.

---

## 1. Project overview

### 1.1 One-sentence pitch

e-Partai is the cloud-agnostic rewrite of PAN’s simPAN membership platform: DPP→PAC administrations, member KTA cards, inbox verification, legislative candidates, and (via merged Pantau Relawan) volunteer trees — NestJS, Postgres, SuperTokens, S3/GCS storage adapters, Expo + TanStack dashboard/PWA — replacing a 2021 Firebase/Firestore stack.

### 1.2 Problem

A national party cannot run 2024-style elections on WhatsApp spreadsheets. simPAN (from 2021) was the production system of record for PAN: register and verify members, issue KTA, place *bacaleg* on dapil, assign *saksi* to TPS, search DPT, crawl party news, export Excel zips of KTP+KTA.

That product was **Firebase-centric**: Firestore as the database, Cloud Functions as the API, Cloud Storage for KTP/KTA, Firebase Auth (plus Realtime Database `accounts` for username login), Elasticsearch as the search sidecar. It worked. It also locked the party to one cloud vendor, made relational integrity a Functions problem, and was a poor base for a **second party** (e-Partai’s documented duplication story).

Same fork as ADVLive: when the next tenant (here: any party, plus Pantau Relawan’s volunteer graph) needed the product, **copying Firestore** was the wrong answer. The rewrite moves to Postgres, self-hosted SuperTokens, and a storage port (`s3` | `gcs`) so AWS and GCP are deploy targets, not the identity of the app.

### 1.3 Who uses it

| Actor | Surface | What they do |
|---|---|---|
| **Anggota** | Expo / PWA | Register, KTP scan, member card, profile, news |
| **Kandidat** | Dashboard + mobile | Self-service Pantau Relawan: volunteer downline, DPT lookup, stats |
| **Relawan** | Expo / PWA | Recruit, tree under a candidate, TPS / calon pemilih |
| **Admin DPP / DPW / DPD / BSN** | Dashboard | Jurisdiction-scoped members, kepengurusan, offices, inbox, exports, news |
| **Superadmin** | Dashboard | Roles, AI analytics chat, full CASL bypass; **not** allowed on mobile |
| **Public** | API | `POST /users/register` (Turnstile in prod), `GET /news` |

Party structure: **pusat DPP → wilayah DPW → daerah DPD → cabang DPC → ranting PAC**. Admins see only their NASIONAL / PROVINSI / KABUPATEN / DAPIL slice.

### 1.4 Core product ideas

**simPAN is the PAN tenancy; e-Partai is the forkable base.** `docs/DUPLICATION.md` is the product: swap `APP_NAME` / domain / KTA template / roles / news crawler, drop `simpan-migration`, ship another party. PAN remains `simPAN` / `simpan.app` / bundle `or.id.pan.simpan`.

**Two Firebase products became one Postgres platform.**  
- simPAN (`simpan-3619f`): members, KTA, kepengurusan, TPS/saksi/real-count, bacaleg, news, kongres leftovers (see [pan-congress.md](pan-congress.md) — do **not** treat Kongres VI as this rewrite).  
- Pantau Relawan (`pantau-relawan-by-lamacca`): candidates, volunteer trees, DPT lock settings.  
Migration app reads both; dashboard has `/pantau-relawan/*`.

**Cloud-agnostic, not “no cloud.”** Domain code talks interfaces. SuperTokens is self-hosted EmailPassword+Session. `StorageService` is S3-compatible (`forcePathStyle`, MinIO locally) **or** GCS. Terraform exists twice (`infra/aws`, `infra/gcp`). Frontends sit on **Cloudflare Workers**. Runtime Nest **does not import firebase-admin**.

**Postgres is the system of record; search is a projector.** Meilisearch indexes `users` and `volunteers` (compose still has a second DPT container; live DPT HTTP still hits `pantaurelawan.lamaccaservices.com`). GitHub’s “dual Meilisearch for users + DPT” is the architecture doc, not what Nest currently queries.

**Errors are values.** Repositories return `neverthrow` `ResultAsync`; services map to HTTP; no try-catch circus on the happy path. Zod schemas in `@e-partai/schema` are shared FE/BE.

**Election observers are honest WIP.** Data and schema migrated (`observers`, `real_counts`, …). `ObserversModule` is **not** wired into `ShellModule`. Dashboard pemilu/tabulasi is a “Fitur Belum Siap” page. Do **not** claim live real-count UI in the rewrite.

### 1.5 Predecessor: simPAN (2021, still the Firestore heritage)

Started **7 Nov 2021**. Repo `~/code/simpan`: Nx 13, **605 commits, sole author**. Last elastic-seed tweak **Jan 2026** — the old stack was still receiving ops while the rewrite shipped.

| Surface | Stack |
|---|---|
| **Dashboard** | React **17** + **react-router-dom 6**, React Query 3, Tailwind 2, Mapbox, Firebase JS 9 |
| **API** | NestJS 8 on a **single** `onRequest` Cloud Function (8 GiB / 540 s) + 1-min Pub/Sub `taskRunner` |
| **Search** | Separate Nest + Fastify + **Elasticsearch 7** (`simpan_users`, offices, administrations, `dpt_raw`, TPS, KPU wilayah) |
| **Auth** | Firebase Auth; usernames in **RTDB `accounts`** + bcrypt → custom token |
| **Files** | Cloud Storage (`ktp/`, `kta/`, exports, bacaleg docs, saksi payments) |
| **Mobile** | **Flutter** companion (Play listing lineage). Not in the Nx tree (`nx-flutter` was in package.json; no `pubspec`). Rewrite mobile is **Expo 54** keeping `or.id.pan.simpan` |

2024 sibling in `lamaccatech/apps/simpan`: still Firebase, Next admin + split function codebases + **Kongres** ([pan-congress.md](pan-congress.md)). Same party, still not cloud-agnostic — the e-Partai rewrite is the actual leave-Firebase step.

Function domains (2021): auth, anggota, kta, offices, administrations, bacaleg, saksi, pelatihan-saksi, export, superadmin. Jobs: stats crawl, news crawl, Excel/KTA zip combiners.

### 1.6 End-to-end journeys (rewrite)

**Join the party.** Public register → Turnstile → membership application → admin inbox (pending / bad NIK / stale region) → verify → KTA number (`pg_advisory_xact_lock`) → PDF+PNG card (Handlebars/Puppeteer) with QR “dikeluarkan oleh DPD PAN …”.

**Scan KTP.** Mobile Gemini (`gemini-2.5-flash`) OCR into identity fields.

**Run a candidate’s field force.** Pantau Relawan roles: koordinator dapil → TPS → calon pemilih. Downline CRUD, Meilisearch volunteers, external DPT lookup.

**Admin export.** BullMQ on Valkey DB 1 (not a Postgres job runner — GitHub lags), ExcelJS stream, 2000-row batches, signed S3/GCS download, 7-day prune.

**Superadmin analytics.** `POST /chat`, Gemini `gemini-2.0-flash`, function-calling tools (candidate rankings, TPS coverage, NIK lookup, …). **Not** raw generated SQL. UI gated; audit stores tool name + params.

### 1.7 Production / infra shape

| Piece | Choice |
|---|---|
| API | NestJS 11, Node 24, Docker |
| DB | PostgreSQL 17, Drizzle 0.45 |
| Auth | SuperTokens core **11.4.5** / node **24** |
| Cache / queue | Valkey 8 — Keyv cache DB 0, BullMQ DB 1 |
| Search | Meilisearch (`users`, `volunteers`); DPT still a separate HTTP API |
| Storage | MinIO / S3 / GCS via `STORAGE_PROVIDER` |
| Frontends | Cloudflare Workers (`simpan-dashboard`, `simpan-pwa`) |
| IaC | Terraform AWS (ECS, RDS, ElastiCache) **and** GCP (Cloud Run, Cloud SQL) |
| Mobile | EAS, app **1.3.5**, Sentry org PT Lamacca Kreatif Solusi |

Leftovers: KTA HTML still references some Firebase Storage PNGs; `.firebaserc` unused at runtime. Gemini API key is required (OCR + chat) — Google as a **capability**, not as the database.

### 1.8 Timeline

| Period | What |
|---|---|
| **Nov 2021** | simPAN greenfield: React+RR dashboard, Firebase Nest functions, Elasticsearch, Flutter mobile |
| **2022** | Peak Firebase product (members, saksi, bacaleg, KTA, DPT) |
| **2024** | `lamaccatech/apps/simpan` Next/split-functions/kongres — still Firestore ([pan-congress.md](pan-congress.md) for the event) |
| **Aug 2025** | e-Partai `Initial commit` — schema for users, wilayah, anggota, kepengurusan, pencalegan, saksi, berita |
| **Sep 2025 – Feb 2026** | Peak rewrite (~1.1k of ~1.46k commits): SuperTokens, adapters, dashboard, Expo, Pantau Relawan, migration |
| **2026** | Duplication docs, pentest/load-test hosts, graphify notes. Last landing 8 Aug 2026 |

### 1.9 Notable subsystems

- Inbox as an **ops work queue**, not a chat app.  
- News crawl (PAN sites) + public list.  
- Banners: live endpoint, currently **hardcoded** two images — do not oversell CMS.  
- Activity log + change-history snapshots (EventEmitter).  
- Tiered throttling (100 / 500 kandidat / 1500 admin / 5000 superadmin per minute).  
- Helmet, Sentry, Turnstile.  
- CLI: seed roles/regions, Meilisearch reindex.  
- E2E: Playwright (dashboard, PWA) + Jest API e2e.

---

## 2. Tech stack & technology highlights

### 2.1 Short list (resume-ready)

TypeScript · Nx · NestJS 11 · PostgreSQL 17 · Drizzle · SuperTokens · neverthrow · Zod · CASL · Expo 54 · React 19 · TanStack Router/Query · Vite 7 · Meilisearch · Valkey · BullMQ · S3 / GCS adapters · Terraform (AWS + GCP) · Cloudflare Workers · Gemini · Playwright

### 2.2 Rewrite surfaces

| App | Stack |
|---|---|
| `apps/api` | Nest 11 host; domains in `libs/api/*` |
| `apps/dashboard` | Vite 7, TanStack Router **1.163**, Query, Table, Form, Tailwind 4, shadcn |
| `apps/mobile` | Expo **54**, RN **0.81**, React Navigation, SuperTokens RN, Gemini KTP |
| `apps/mobile-pwa` | Vite PWA, Konsta UI, same volunteer/member flows (“Pantau Relawan by simPAN”) |
| `apps/cli` | Citty ops |
| `apps/simpan-migration` | firebase-admin + firewalk → Drizzle + SuperTokens passwordhash import (`firebase_scrypt`) |

### 2.3 Domain modules (`ShellModule`)

Wired: Banners, Regions, Identity, Access, Upload, Users, Admins, Me, Stats, News, Log, Administrations, Candidates, Volunteers, Exports, AiChat, Inbox (+ Core/Config). **17** feature modules. GitHub still says 15. **observers** exists on disk, **unwired**.

Concern-split repos (Query / Mutation / List / Creator, plus volunteer Deleter/Updater) where the convention paid off; some modules remain a single repository.

### 2.4 Cloud-agnostic mechanics

| Concern | Mechanism |
|---|---|
| Auth | SuperTokens, not Firebase Auth. Migration imports scrypt hashes. |
| Files | `StorageService` + `S3StorageService` / `GcsStorageService`. Upload lib only mints presigned URLs. |
| Deploy | Terraform **per cloud per env**; `docs/DUPLICATION.md` forbids sharing `simpan-*` names. |
| Search | Meilisearch **or** Postgres fallback factory. |
| Queue | BullMQ; old Firestore `jobs` skipped on migrate. |

### 2.5 Architecture highlights

- **Ports over SDKs** — storage and search are factories; GCP is optional ADC, not a recommended import in services.  
- **Result types as the API contract** — neverthrow repositories, mapped once at the service boundary.  
- **Zod once** — forms, query params, payloads share `@e-partai/schema`.  
- **Jurisdiction in CASL + Valkey** — DPP admin cannot paginate another province’s anggota.  
- **Gemini as tools, not SQL generation** — rewritten after the “model writes SELECT” idea.  
- **Two Firestore graphs, one member table** — pantau volunteers are not a second `users` world after import.  
- **AWS *or* GCP** — same Docker API; ECS+RDS vs Cloud Run+Cloud SQL.

### 2.6 Predecessor stack (for interviews)

React 17 · react-router 6 · Flutter (mobile) · Nest 8 on Cloud Functions · Firestore · Firebase Auth · RTDB · Cloud Storage · Elasticsearch 7 · Mapbox · Nx 13 · Node 14 functions

---

## 3. Contribution

### 3.1 Role, in one paragraph

I am the **sole engineer** on PAN’s information system from the 2021 Firebase simPAN through the 2025–26 e-Partai rewrite. Git: **605/605** on `simpan`; **~1,463** commits, only **Muhammad Dzul Arsyil**, on `e-partai`. I talked to party operators (DPP/DPD inbox, KTA numbering, saksi/TPS rules, Pantau Relawan trees) and implemented both the original product and the leave-Firebase architecture. This is not a “I rewrote someone else’s app” story.

### 3.2 What “doing it all” covered

**2021 product.** React+RR dashboard, Cloud Functions Nest API, ES indexes, KTA Sharp/PDF pipeline, crawlers, Flutter mobile, jurisdiction roles.

**Why rewrite.** Vendor lock, document-store integrity, Pantau Relawan as a second Firebase app, and a documented goal to **clone for other parties**.

**Rewrite architecture.** SuperTokens, Drizzle/Postgres, storage port, Valkey, Meilisearch, Nx 22, TanStack dashboard, Expo (replacing Flutter on the same bundle ID), migration of ~1.45M anggota + volunteers/candidates, password-hash import, dual Terraform.

**Honesty in the cutover.** Real-count/saksi UI not rebuilt yet; DPT search still a Lamacca HTTP service; some KTA assets still on old Storage URLs. Specs and 501 stubs exist so the next cycle is not a surprise.

### 3.3 Git facts

| Repo | Commits | Authors | Span |
|---|---|---|---|
| `simpan` | 605 | 1 | Nov 2021 – Jan 2026 |
| `e-partai` | ~1,463 | 1 | Aug 2025 – Aug 2026 |
| `lamaccatech/apps/simpan` | ~220 | 1 | Jul 2024 – (Firebase Next refresh) |

Peak rewrite months: Dec 2025 – Feb 2026.

### 3.4 Outcomes you can defend

- National party membership **on production Firebase for years**, then a **cloud-agnostic** rewrite **without a team**.  
- **Two Firebase products** (simPAN + Pantau Relawan) → one Postgres model + SuperTokens, with scrypt import so users did not all reset passwords.  
- Storage and deploy are **S3 or GCS / AWS or GCP** — the duplication doc is the business pitch to the next party.  
- GitHub-published scale (cite the profile): **1.6M** members/volunteers, **50K+** installs, **~1–2K** DAU. Volunteer graph includes **Pantau Relawan for thousands of PAN candidates** (not the ~20+ Lamacca SaaS tenants).  
- Functional core (`neverthrow`) and shared Zod — interview sticky, verified in `docs/TECHNICAL_ARCHITECTURE.md`.

---

## 4. Suggested resume copy

Trim or mix. Do not paste the whole brief.

### 4.1 Header line

**Sole Full-Stack Engineer, e-Partai / simPAN (Partai Amanat Nasional)** — Nov 2021 – present  
National party platform (members, KTA, administrations, volunteers). Firebase 2021 → Postgres / SuperTokens / S3-or-GCS rewrite 2025. Sole developer on both.

### 4.2 Short blurb (~40 words)

Sole engineer for PAN’s simPAN since 2021 (React, Flutter, Firestore) and the 2025 e-Partai rewrite: NestJS, Postgres, SuperTokens, S3/GCS adapters, Expo — plus a Firestore→Postgres cutover of simPAN and Pantau Relawan (~1.45M members in the migrator).

### 4.3 Medium blurb (~80 words)

Designed simPAN for Partai Amanat Nasional as the sole developer (2021): React + react-router dashboard, Flutter mobile, Firestore, Cloud Functions, Elasticsearch. In 2025 rewrote it as e-Partai, a forkable party platform on NestJS 11, Postgres 17, self-hosted SuperTokens, and a storage port (MinIO/S3/GCS) with Terraform on AWS and GCP. Merged Pantau Relawan’s volunteer trees into the same database, imported Firebase Auth hashes, and replaced vendor lock with adapters so the next party is a config+branding copy, not a Firestore fork.

### 4.4 Bullet options (pick 4–6)

- Sole developer for PAN’s membership platform **from 2021** (React/react-router, Flutter, Firebase) **and** the **2025 cloud-agnostic rewrite** (Nest, Postgres, SuperTokens, Expo).  
- Replaced Firestore + Cloud Functions + Firebase Auth with **Postgres + SuperTokens + an S3-compatible/GCS storage adapter** so the API is identical on AWS (ECS/RDS) or GCP (Cloud Run/Cloud SQL).  
- Migrated **two** Firebase apps (simPAN + Pantau Relawan), including ~**1.45M** anggota and SuperTokens `firebase_scrypt` password import.  
- Nx monorepo: Nest domain libs (`neverthrow` repositories, CASL jurisdiction, shared Zod), TanStack Router dashboard, Expo + PWA for anggota/kandidat/relawan.  
- Gemini **function-calling** analytics chat (superadmin) and KTP OCR — tools, not model-generated SQL.  
- Wrote **duplication runbooks** (KTA template, roles, news crawler, Terraform `name_prefix`) so e-Partai is a product for other parties, not a one-off PAN repo.

### 4.5 Skills line

TypeScript, Nx, NestJS, PostgreSQL, Drizzle, SuperTokens, neverthrow, Zod, CASL, Expo/React Native, React, TanStack Router, Vite, Meilisearch, Valkey, BullMQ, AWS S3, GCS, Terraform, Cloudflare Workers, Gemini, Firebase (predecessor), Elasticsearch (predecessor), Flutter (predecessor mobile)

### 4.6 Things to say carefully (or not at all)

| Claim | Safer wording |
|---|---|
| “1.6M users” | GitHub Selected Projects. Migrator comment is **~1.45M anggota**. Volunteers extra. |
| “50K installs / 1–2K DAU” | GitHub card only — not in the rewrite repo. |
| “Live real-time vote tabulation in e-Partai” | **Migrated data; UI not rebuilt.** simPAN Firestore had it. |
| “Dual Meilisearch for DPT” | Architecture intent. Nest currently indexes **users + volunteers**; DPT is still an HTTP API. |
| “Postgres-backed excel job queue” | GitHub lags; rewrite uses **BullMQ on Valkey**. Firestore `jobs` were skipped. |
| “15 API modules” | Card is stale; Shell now wires **17**; observers unwired. |
| “I led a team” | **Sole developer** both eras. |
| Flutter in `~/code/simpan` | Dashboard/functions yes; Flutter was a **companion app**, not that Nx tree. |

---

## 5. Suggested 3-bullet resume block

**e-Partai / simPAN (PAN)** — Sole Full-Stack Engineer *(Nov 2021 – present)*  
National political-party platform: members, KTA, kepengurusan, volunteers (Pantau Relawan merge).

- Built production simPAN on Firebase (React + react-router, Flutter, Firestore, Cloud Functions, Elasticsearch) as the only engineer from 2021.  
- Rewrote it in 2025 as cloud-agnostic e-Partai: NestJS, Postgres, SuperTokens, S3/GCS storage port, Terraform on AWS **and** GCP, Expo replacing Flutter on `or.id.pan.simpan`.  
- Cut over two Firebase projects (including ~1.45M members + volunteer trees) with hash import; neverthrow + Zod + CASL jurisdiction as the contract — sole developer throughout.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **simPAN** | PAN’s product name / domain (`simpan.app`) |
| **e-Partai** | Rewrite repo / forkable brand |
| **Pantau Relawan** | Second Firebase app, now volunteer/candidate domain |
| **KTA** | Kartu Tanda Anggota |
| **Bacaleg** | Calon legislatif (dapil placement) |
| **Saksi / TPS** | Poll-station witness |
| **DPT** | Daftar Pemilih Tetap (KPU roll) |
| **DPP/DPW/DPD/DPC** | Party org levels |
| **Storage port** | `STORAGE_PROVIDER=s3\|gcs` |

---

*Generated from `~/code/e-partai`, `~/code/simpan`, git history (Muhammad Dzul Arsyil), and the e-Partai section of github.com/aru-shiru/aru-shiru as of 16 August 2026. GitHub volume numbers are labeled as profile claims; vote-tabulation UI is marked WIP.*
