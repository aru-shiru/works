# SimplePol — Resume Project Brief

**Role:** Sole Full-Stack Engineer (product, architecture, implementation, ops)  
**Period:** February 2026 – present (greenfield rewrite; production since mid-2026)  
**Domain:** Health-check management for Indonesian driving-license (SIM) applicants  
**Status:** Live in production (Laravel Cloud + AWS), serving multiple Polda jurisdictions  

This document is a **source brief**, not a finished resume entry. Sections 1–3 are detailed on purpose so they can be trimmed later. Section 4 has ready-to-paste resume bullets.

Public GitHub listing: **SimplePol — Health Check Management System** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). The profile card is shorter and slightly stale vs this brief (it still says SQLite / 24 models / 5 roles). Public metrics already on that card: replacing a legacy app with **1.5M Play Store installs** and **~200K monthly health checks**.

---

## 1. Project Overview

### 1.1 One-sentence pitch

SimplePol is a production health-check platform that lets Indonesian driving-license applicants complete mandatory medical screening — identity verification, examination, payment, and official certificate — across clinics, mobile units, and police jurisdictions, with role-specific tools for doctors, health workers, registration agents, police officers, and administrators.

### 1.2 Problem

Before a person can obtain or renew a SIM (Surat Izin Mengemudi), Indonesian regulation requires a medical examination. The previous generation of this product was a legacy MySQL system with year-partitioned tables, limited identity integrity, and operational workflows that did not scale cleanly across Polda/Polres jurisdictions, payment methods, or modern mobile use.

SimplePol is a **full rewrite**: identity-first data model, passwordless and social auth, AI-assisted KTP capture, jurisdiction-aware authorization, pluggable payments, official certificate generation, and a native practitioner API — plus a one-time migration of tens of millions of historical records.

### 1.3 Who uses it

| Actor | What they do |
|---|---|
| **Applicants (patients)** | Self-register via magic link or Google/Apple, upload KTP, complete an online self-assessment, pay, visit any authorized location under the chosen Polres, download a QR-verifiable certificate. Can also register a health check for someone else (family). |
| **Doctors** | Walk-in registration, examination, certificate signing, staff assignment, location/recommendation management, settlements, monitoring, bulk print. Must hold a valid SIP and a police recommendation for each Polres they serve. |
| **Health workers (nakes)** | Assist under a supervising doctor at assigned locations: walk-in intake, vitals/self-assessment handling, transactions. Cannot sign certificates. QR pairing for device/location access. |
| **Registration agents (kolektor/biro)** | Batch-register patients at service locations (KTP OCR, batch payment), used for high-volume field operations. Superadmin-verified. |
| **Police officers** | Jurisdiction-scoped oversight. Polda-level: Kapolda, Kabiddokkes, Dirlantas. Polres-level: Kapolres, Kasidokkes, Kasat Lantas. Doctor verification, medical-record views, monitoring/exports. |
| **Superadmins** | Platform ops: identities, fraud/link-request triage, doctors/agents, rates, refunds, settlements, master data, impersonation (read-only), exports. |

A user can hold multiple roles. Active role is session-switched; officer jurisdiction lives on the `user_role` pivot (`id_polda` / `id_polres`).

### 1.4 Core product ideas

**Identity is the source of truth, not the login account.** Government ID (`nomor_identitas` / NIK) is unique and permanent. One identity can have many user accounts (forgotten email, clinic-first registration). Health checks, payments, and certificates always hang off `identity_id`, never `user_id`. Immutable identity fields (NIK, name, DOB, gender, nationality) are locked after verification; only superadmin can correct them, with an audit reason.

**Two registration paths.**  
- *User-first:* applicant magic-links in, then uploads KTP.  
- *Identity-first:* doctor/agent/health worker captures KTP at the counter; email is optional. The person can claim the identity later via an ownership request.

**Jurisdiction is a first-class constraint.** Indonesian police hierarchy is Polda (province) → Polres (district) → service location (clinic, mobile unit, mall booth, office). Doctors need a recommendation per Polres, then an assignment to specific locations. Health workers are assigned under a doctor *at* a location. Triple-layer validation (form + model hook + FK) keeps denormalized `id_polda`/`id_polres` consistent.

**Examination is not always “the doctor typed everything.”** Patients can complete a self-assessment (vision/Ishihara, hearing, joints, history) before arriving. At the location, a doctor or health worker reviews, completes physicals, and validates. Certificates always show the *responsible doctor* (`dokter_penanggung_jawab`); `dilakukan_oleh` records whether a health worker performed the exam.

**Billing is policy-driven per Polda, priced per Polres.** Most Polda charge once per health check; Polda Metro Jaya (DKI) charges per SIM type. Rates cascade: type-specific override → group-level (A/B/C/D). Component breakdown (examination fee, service fee, optional application fee) is snapshotted onto the payment so later rate changes never rewrite history. Fee waivers: authority recommendation (*bebas biaya*) or 14-day overlapping-SIM grace (*ditanggung sebelumnya*).

**Certificates are official documents.** Two layouts: legacy and SE Kapolri 4/VIII/2023. Per-Polres paper (A4 / stacked A5), header/logo stripping, QR to a public verification URL, cached PDF generation, print tracking, queue-backed bulk print. Demo/trial certificates are watermarked so they cannot be mistaken for production.

### 1.5 End-to-end journeys (compressed)

**Online applicant**  
Magic link / social login → KTP OCR + review → personal + emergency contacts → choose Polda/Polres + SIM type(s) → self-assessment → pay (or waiver) → visit any location under that Polres within the validity window → doctor/nakes completes exam → certificate issued.

**Walk-in**  
Patient arrives with physical KTP → staff activate location context → search or register via camera OCR → exam + payment (often cash) → certificate printed on the spot.

**Agent batch**  
Agent registers many patients at a location (including multi-KTP OCR) → batch payment → clinic processes the queue → bulk certificate print.

**Identity claim (clinic-first user later opens an account)**  
User uploads KTP for an NIK that already exists → system never creates a second identity → pending `IdentityLinkRequest` + selfie → queued Gemini comparison may auto-approve only role-free patient identities that clear confidence thresholds → otherwise superadmin decides. Approval links the account, syncs sibling roles, transfers files, notifies the user.

### 1.6 Production footprint (as of mid-2026)

**Live topology**

- **Compute:** Laravel Cloud, Singapore (`ap-southeast-1`), Octane + FrankenPHP. App cluster Pro 2 vCPU / 8 GiB, autoscale 1–10. Separate worker cluster 2 vCPU / 4 GiB, autoscale 1–10, 4 queue processes/replica, scheduler only on workers.
- **Database:** AWS RDS PostgreSQL 18.3 in Jakarta (`ap-southeast-3`), `db.m7g.large` (Graviton3), Multi-AZ, encryption at rest, Performance Insights. Cross-region RTT ~30–60 ms — master data is cached and eagerly loaded on purpose.
- **Files:** AWS S3 in Jakarta (`simpelpol-storage`), signed URLs for private KTP/SIP/signature/certificate files.
- **Cache / queue:** Laravel Cloud managed Valkey (Redis), in-region with compute. ~93% cache hit rate on observed busy days.
- **OCR sidecar:** standalone Go proxy on GCP Cloud Run in Jakarta; Laravel mints RS256 JWTs; the proxy is the only process that holds the Gemini key.

**Observed load (8 Jun 2026, busiest documented day)**

- ~312,000 requests / 24h, 0.01% 5XX
- App cluster peaked ~40% CPU / 29% memory at 2 replicas
- Worker memory is the tight resource (Chromium PDF jobs, ~1.2K certificates/day)
- RDS peaked ~7.8% CPU, ~16 connections

**Jurisdictions configured in app policy:** Jawa Tengah (JTG), Jawa Barat (JBR), Jawa Timur (JTM), DIY (JGJ), Polda Metro Jaya (DKI). DKI is the per-SIM billing polda and the cross-Polres examination pilot.

**Legacy migration (one-time, custom Go tool)**

| Phase | Volume | Time |
|---|---|---|
| Users | ~1.61M | ~1.5 min |
| Identities (NIK-deduped) | ~7.0M | ~4.5 min |
| Emergency contacts | ~2.2M | ~3.3 min |
| Health checks + payments + SIM apps | ~9.5M + ~9.5M + ~8.7M | ~17 min |
| Rate-snapshot backfill | ~9.5M | ~7.5 min |
| **Total** | **~47M rows** | **~37 minutes** |

Post-migration FK checks passed; sequences reset; `ANALYZE` run. This replaced year-sharded MySQL tables with a single normalized PostgreSQL schema.

### 1.7 How the product is built (process)

The repo is **spec-driven**. Feature specs in `specs/features/` (~100 files) and user journeys in `specs/user-journeys/` are the contract; when code and spec diverge, the spec wins. Coding standards, architecture, and product rules live in `docs/`. Tests are treated as executable proof (~320 Pest files; full suite is large enough that it is run in parallel on 8 processes, ~9 minutes).

The application is Indonesian-first in the data model (column names, enum values, UI copy) and UTC-at-rest for timestamps, with display conversion through a single `DateFormatter` that reads the user’s timezone cookie (fallback `Asia/Jakarta`).

### 1.8 Notable subsystems beyond the happy path

- **Trial / demo isolation:** `APP_TRIAL_MODE` + immutable `is_demo` stamps so training traffic never contaminates production settlement, reporting, or certificate sequences. Audited commands promote or cut over trial data.
- **Settlements & revenue split:** rate components are tagged `dokter` vs `pengelola`; split is frozen at payment time; doctor-direct and KRI settlement modes; PDF settlement packs.
- **Refunds:** gateway-capable auto-dispatch + reconciliation jobs; admin retry UI; expired-payment auto-resolution; consolidated refunds for batches.
- **Mobile service (SIM Keliling):** stops, shifts, auto-close of open shifts, location stamping on health checks.
- **Monitoring & exports:** officer/admin dashboards (daily service, doctor ranking, SLA, trend, medical metrics, deleted-data reports); queued chunked exports (Excel/PDF) so large reports do not block the web tier.
- **Impersonation:** read-only admin impersonation; sensitive role/jurisdiction actions re-authorized server-side.
- **PWA:** Vite PWA plugin, standalone manifest, custom service worker — field staff often work from phones/tablets.
- **Analytics:** PostHog via a managed proxy; autocapture only on an explicit low-risk route allowlist so medical/identity forms stay pageview-only.
- **Feature flags (Pennant):** identity-scoped pilots — thermal 58 mm receipts, bulk print, paper-form layout, health-worker QR pairing, superadmin sensitive-data views.

---

## 2. Tech Stack & Technology Highlights

### 2.1 Stack at a glance

| Layer | Choice | Why it matters here |
|---|---|---|
| Language / runtime | **PHP 8.5**, **Laravel 13**, **Octane 2 (FrankenPHP)** | Persistent workers on Laravel Cloud; scoped bindings instead of request-leaking singletons. |
| UI | **Livewire 4**, **Flux UI Pro 2**, **Alpine.js**, **Tailwind CSS 4**, **Vite 7** | Server-driven app with client-side Alpine for modals/pickers so clinic pages do not round-trip for every UI affordance. `#[Lazy]` + shared skeletons on heavy dashboards. |
| Auth | **Fortify** (passwordless magic link), **Socialite** (Google + Apple), **Sanctum** (native API), email/password only for non-production ops | Fits a population that will not remember passwords; doctors/nakes on Android use Google ID-token → Sanctum bearer. |
| AI / identity | **Google Gemini** via a **Go OCR proxy** (Cloud Run, Jakarta) | KTP/passport extraction + authenticity/quality scoring; queued face+document compare for ownership claims. API key never shipped to the browser in the live path. |
| Payments | Strategy **`PaymentGateway` contract**: **Xendit** (default), **Midtrans**, **Flip**, **IFortePay**; cash; fee waiver | Swap provider by config. Webhooks, status poll, cancel, refund. Rate snapshot + revenue split persisted on the payment. |
| Database | **PostgreSQL 18** (prod RDS Jakarta), **SQLite :memory:** (tests) | `jsonb` for payment details, rate snapshots, change-history state. `timestamptz` everywhere. |
| Cache / queue | **Valkey/Redis** (Laravel Cloud) | Sessions, master-data cache, queues: `default`, `background`, `certificate`. |
| Search | **Laravel Scout** (database driver) | Doctors, Polres, locations. |
| Flags | **Laravel Pennant** | Gradual rollout of print/receipt/pairing features. |
| Files | **S3 / MinIO**, polymorphic `files` table, signed URLs | KTP, SIP, signatures, waiver letters, certificates. Soft-delete; storage is not auto-purged (audit). |
| PDF | **spatie/laravel-pdf** (Browsershot/Chromium) + **DomPDF** | Official SKHP certificates, settlements, monitoring reports, thermal/A4 receipts. |
| Spreadsheets | **PhpSpreadsheet**, **OpenSpout** | Admin/officer exports. |
| Observability | **Laravel Nightwatch**, **Clockwork** (dev) | Errors, performance, logs in every environment. |
| Product analytics | **PostHog** (proxied) | Privacy-constrained frontend analytics. |
| Local / CI | **Laravel Sail**, **Pest 4** (+ browser plugin), **Paratest**, **Pint**, **k6** | Dockerized dev; ~320 test files; parallel suite; monthly staging load test. |
| Native clients | Versioned **`/v1` REST API** + **OpenAPI** contract | Android doctor/health-worker app: Google auth, location activation header, walk-in, queues, certificates, OCR tokens. |
| PWA | **vite-plugin-pwa** (injectManifest) | Installable clinic/field UI. |
| Sidecar / tools | **Go** OCR proxy; **Go** `tools/migrate` (pgx `COPY`, goroutines) | Hot-path OCR next to users in Jakarta; 47M-row migration in ~37 minutes. |

### 2.2 Architecture highlights (the “we didn’t just CRUD this” list)

**Identity-first domain model**  
`Identity (1) → Users (N)`. Health checks, contacts, doctors, agents all key off identity. Duplicate NIK never inserts a second row; it opens an ownership claim.

**Denormalized jurisdiction + triple validation**  
`id_polda` + `id_polres` on locations, recommendations, and health checks so Polda filters are index lookups (documented ~10–15× vs JOIN). Integrity is enforced three times: Form Request, model `saving` hook, database FKs. Helper `setPolres()` always writes both IDs.

**RBAC with jurisdiction on the pivot**  
`SystemRole` enum: superadmin, six officer ranks, dokter, nakes, kolektor. Officers only see their Polda or Polres. Role switcher + `HomePathResolver` after login.

**Payment as a port, not a vendor**  
`PaymentGateway` interface + DTOs (`CreatePaymentRequest/Response`, `PaymentResult`, refund types). `PaymentServiceProvider` binds the configured driver. Webhooks verify signatures. Missing-reference recovery handles “staff switched to cash, then the gateway settled late.”

**OCR security boundary**  
Laravel signs short-lived RS256 JWTs (`iss=simpelpol-laravel`, `aud=simpelpol-ocr-proxy`, operation-bound `op`, `sub` as rate-limit key). Session tokens (30 min, extract-only) for the browser picker; 60 s single-use tokens for server calls. Proxy rate-limits per subject + IP and holds a Gemini concurrency semaphore. Fallback direct-Gemini path exists only when the proxy flag is off.

**Async fraud check**  
`VerifyIdentityLinkRequestJob` after commit. Auto-approve is narrow (patient identity, no role-bearing linked account, files + confidence thresholds). Everything else stays pending for a human.

**Concurrency at the clinic**  
Multiple staff can see the same pending exam. Claim uses atomic `WHERE … IS NULL` + `UPDATE`; validate/cancel use `lockForUpdate()` inside a transaction so the last writer cannot silently overwrite.

**Certificate pipeline**  
Format stamped on the health check at validation (`SE_2023` vs legacy) so later config flips do not rewrite issued PDFs. QR → public view. Cached generation. `GenerateCertificatePdfJob` + chunked bulk print. ULID `kode_tes` as the public route key (no sequential IDs in URLs).

**Octane-safe design**  
No request/config injected into singleton constructors; prefer `scoped`. Static accumulators forbidden. Worker cluster isolated from the web cluster so the scheduler cannot double-fire.

**Audit & compliance posture**  
`ActivityLog` (what happened) + `ChangeHistory` (JSONB before/after). Soft deletes on identities, health checks, payments, files. Never-delete policy on audit tables. Immutable vs mutable identity fields. UU PDP-oriented handling: private S3, signed URLs, HTTPS, retention of medical/audit history. Read-only impersonation.

**Config-as-policy for rare business rules**  
Per-Polda billing, examination format, certificate format, document enforcement, and cross-Polres examination live in `config/polda.php` (code-reviewed, no cache-invalidation dance). Per-Polres paper/header in `config/polres.php`.

**Spec + test as the contract**  
~100 feature specs, OpenAPI for `/v1`, Pest feature tests as the default, factories + states, parallel in-memory SQLite so the large suite stays isolated per worker.

**Performance tactics that match the topology**  
Master-data caches (Polda/Polres/location/SIM/rates) because compute is in Singapore and Postgres is in Jakarta. Dashboard count versioning. Reporting connection for heavy exports. Eager-load rules. Alpine-first UX on end-user pages to cut Livewire chatter.

### 2.3 Application surface (approximate, this repo)

| Area | Scale |
|---|---|
| Commits (sole author) | ~1,750 |
| PHP classes under `app/` | ~520 |
| Eloquent models | 42 |
| Enums | 42 |
| Livewire page/components | ~175 |
| Domain services | ~100 |
| Feature/unit test files | ~320 |
| Feature specs | ~100 |
| Migrations (additive after staging deploy) | ~87 |
| Console commands (ops/backfill/reconcile) | ~25 |
| Queue jobs | certificate PDF, bulk print chunks, identity-link AI, expired payments, refunds, exports, medical-record chunks |
| Native API controllers | `/v1` auth, locations, dashboard queues, walk-in, health-check actions, certificates, transactions, OCR token, reference data |

### 2.4 External systems integrated

- **Google Gemini** — KTP OCR, document-quality/authenticity signals, identity-link face+document compare  
- **Google OAuth + Sign in with Apple** — consumer and practitioner login  
- **Xendit / Midtrans / Flip / IFortePay** — VA, e-wallet, cards; webhooks + refunds  
- **Mailtrap** — transactional email (sandbox vs sending domain)  
- **AWS RDS + S3** (Jakarta)  
- **Laravel Cloud** (Singapore) — app, workers, Valkey, Nightwatch  
- **GCP Cloud Run** — OCR proxy  
- **PostHog** — product analytics  
- **Android practitioner app** — first-party client of `/v1` (Google ID token exchange)

A POLRI machine-to-machine monitoring API (OAuth client credentials + mTLS) is **designed, not shipped**.

---

## 3. Contribution

### 3.1 Role, in one paragraph

I am the **sole engineer** on SimplePol. There is no separate backend, frontend, mobile-API, DevOps, or QA team on this codebase. From February 2026 I took a Laravel Livewire starter and designed, implemented, deployed, migrated, and operate the production system: domain model, every role’s product surface, payments, certificates, AI identity pipeline, native API, infra, and the ~47M-row legacy cutover. I work **alone with AI coding agents** (Claude Code, OpenAI Codex, and Cursor) as a force multiplier — I own the architecture, the specs, the production decisions, and the review of every change.

Git history on this repository is a single author: **Muhammad Dzul Arsyil**, ~1,750 commits.

### 3.2 What “sole developer” actually covered

**Product & domain**  
Translated Indonesian SIM-health-check regulation and field practice (walk-in vs online, SIP + police recommendation, SE Kapolri 2023 forms, per-Polda billing quirks, *bebas biaya*, keliling units, KRI settlement) into a coherent identity-first model and role matrix.

**Architecture**  
Chose the identity-vs-user split, denormalized jurisdiction, payment port, OCR trust boundary, Octane worker split, trial-mode silo, and “specs are the source of truth” process. Wrote the coding guideline that agents and future humans follow.

**Implementation**  
~175 Livewire screens, ~100 services, 42 models, Fortify/Socialite/Sanctum auth, Gemini + Go proxy identity capture, four payment drivers, certificate/PDF pipeline, `/v1` OpenAPI API, Pennant-flagged pilots, Scout search, PWA, PostHog, Nightwatch.

**Data & migration**  
Designed and ran a Go streaming migrator (`pgx` COPY, latin1→UTF-8, NIK dedup, enum maps) that moved ~47 million rows from year-partitioned MySQL into PostgreSQL in ~37 minutes, then verified FKs and reset sequences. Subsequent schema changes are **new migrations only** (staging already live).

**Quality**  
Pest suite at hundreds of files / thousands of tests; parallel runner; Pint; feature specs before/with the code; concurrency tests for clinic races; gateway fakes; browser tests where they earn their keep.

**Production operations**  
Laravel Cloud + RDS + S3 topology, right-sizing after real traffic (web and DB downgraded once idle capacity was proven; Multi-AZ turned on before Metro Jaya scale-up), queue/scheduler isolation, cache strategy for the Singapore↔Jakarta RTT, certificate-job memory watch, k6 staging load tests, Shannon pentest runbook, refund/payment reconciliation commands, trial cutover commands.

**Security & fraud**  
OCR JWT design, rate limits, identity-link AI with a narrow auto-approve path, immutable identity fields, audit/change history, read-only impersonation, webhook signature verification, private file access via signed URLs.

### 3.3 How AI agents were used (honest, resume-safe)

I treat agents as a **high-bandwidth pair-programmer**, not as an unsupervised code generator:

- I write or approve the **spec** (business rules, edge cases, authorization) before or while implementing.  
- Agents draft implementations, tests, and refactors against those specs and the project’s coding guideline.  
- I keep **architecture and production judgment** (what to denormalize, what must never auto-approve, how to size RDS, when a payment driver is wrong).  
- I run the test suite and review diffs; nothing ships because an agent said it was done.  
- Tooling evolved with the work: Claude Code and Codex for long spec-driven features; Cursor for the current inner loop.

This is a useful talking point in interviews: shipping a regulated, multi-sided production system as one person is only tractable if you are disciplined about specs, tests, and review — agents amplify that, they do not replace it.

### 3.4 Outcomes you can defend in an interview

- Greenfield rewrite **in production** within ~4 months of the first commit (Feb → live topology documented by May/June 2026), then continued jurisdiction and payment hardening.  
- **~312k requests/day** on a right-sized 2 vCPU web tier with <0.02% 5XX on the busiest measured day.  
- **~47M historical rows** migrated with a purpose-built Go tool in under 40 minutes, NIK-deduped to ~7M identities.  
- Multi-sided marketplace of **patients, doctors, nakes, agents, and six officer ranks**, with jurisdiction isolation.  
- AI identity pipeline that **does not trust the browser with the model key** and **does not auto-link role-bearing identities**.  
- Payment stack that survived **provider swaps** (Flip → Midtrans → Xendit/IFortePay) behind one contract.  
- Official certificates that match **SE Kapolri 4/VIII/2023** and remain stable after config changes because format is stamped at validation.  
- A test-and-spec culture large enough that the default full run is **parallel Pest**, not a hope-and-click QA pass.

---

## 4. Ready-to-paste resume material

Trim, pick 3–6 bullets, and drop the rest. Prefer outcomes + constraints over tool lists.

### 4.1 Title line options

- Sole Full-Stack Engineer, SimplePol — production health-check platform for Indonesian driving-license medical screening  
- Founding / sole engineer for a multi-sided Laravel platform (identity, payments, AI OCR, official certificates) used by doctors, police jurisdictions, and applicants  

### 4.2 Short blurb (~40 words)

Sole engineer for SimplePol, a production Laravel platform that runs mandatory driving-license health checks in Indonesia: identity-first records, Gemini KTP verification, jurisdiction-aware RBAC, pluggable payments, and official PDF certificates — including a 47M-row legacy migration.

### 4.3 Medium blurb (~80 words)

Designed and shipped SimplePol as the sole developer (AI-assisted). The system replaces a legacy MySQL health-check product with an identity-first PostgreSQL/Laravel 13 app: passwordless and social auth, a Go OCR proxy in front of Gemini, doctor/nakes/agent/officer workflows, per-Polda billing, and SE Kapolri 2023 certificates. Operates it on Laravel Cloud + AWS RDS/S3 (Jakarta), with Octane workers, ~312k requests/day observed, and a custom Go migrator that moved ~47 million historical rows in ~37 minutes.

### 4.4 Bullet bank (mix and match)

- Sole engineer for a production multi-sided health-check platform serving patients, doctors, health workers, registration agents, and jurisdiction-scoped police officers across multiple Indonesian Polda.  
- Modeled the domain as **identity-first** (NIK as source of truth, many users per identity) so clinic-registered people can later claim records without duplicating medical history.  
- Built AI KTP capture through a **Go OCR proxy** (Cloud Run, Jakarta): RS256 operation-bound JWTs, server-side rate limits, Gemini key never exposed to the browser; queued Gemini compare for ownership-claim fraud.  
- Implemented a **pluggable payment port** (Xendit, Midtrans, Flip, IFortePay) plus cash and two waiver types; froze rate-component snapshots and doctor/platform revenue splits at payment time.  
- Generated official SKHP PDFs (legacy + **SE Kapolri 4/VIII/2023**), QR public verification, cached/queued render, bulk print — format stamped at validation so policy changes cannot rewrite issued documents.  
- Exposed a versioned **`/v1` Sanctum API** (OpenAPI) for the Android practitioner app: Google ID-token exchange, header-scoped location activation, walk-in, work queues, certificates.  
- Wrote a **Go migration tool** (streaming CSV, `pgx` COPY) that loaded ~47M rows from year-partitioned MySQL into PostgreSQL in ~37 minutes, including ~7M NIK-deduped identities and ~9.5M health checks.  
- Operated Laravel **Octane/FrankenPHP** on Laravel Cloud with a split web/worker topology, RDS Multi-AZ in Jakarta, and cache/eager-load strategy for 30–60 ms cross-region RTT; right-sized infra after measuring ~312k req/day.  
- Enforced clinic correctness with **atomic claim updates** and `lockForUpdate` validation, plus a large Pest suite (~320 files, parallel CI) and spec-driven feature contracts.  
- Used Claude Code, Codex, and Cursor as a disciplined pair-programmer against written specs and tests while retaining sole ownership of architecture, security, and production operations.

### 4.5 Skills line (keywords)

PHP 8.5 · Laravel 13 · Livewire 4 · Octane/FrankenPHP · PostgreSQL · Redis/Valkey · REST/OpenAPI · Laravel Sanctum/Fortify/Socialite · Gemini / OCR · Go (migration + proxy integration) · AWS RDS/S3 · Laravel Cloud · payment gateways (Xendit, Midtrans) · Pest · spec-driven development · RBAC · PDF generation · PWA · production operations

### 4.6 Things to say carefully (or not at all)

| Claim | Safer wording |
|---|---|
| “I built the Android app” | You built the **backend `/v1` API** the Android app consumes (unless you also own that repo). |
| “POLRI API integration” | **Designed**, not implemented. |
| “AI wrote the product” | **Sole developer, AI-assisted.** You own decisions, specs, review, and production. |
| Exact user/revenue numbers | Prefer ops numbers in this brief (~312k req/day, ~47M migrated rows, jurisdictions). GitHub already publishes **1.5M Play Store installs** (legacy) and **~200K monthly health checks** — cite the profile, do not invent extras. |
| GitHub “Laravel + SQLite + 24 models” | That card lags the rewrite. Live stack is Laravel 13 / Postgres / ~42 models; use this brief. |
| “100% test coverage” | Large, parallel Pest suite; do not invent a coverage percentage. |

---

## 5. Suggested 3-bullet resume block (already trimmed)

**SimplePol** — Sole Full-Stack Engineer *(Feb 2026 – present)*  
Production platform for mandatory driving-license health checks in Indonesia (patients, doctors, health workers, agents, police officers).

- Designed an identity-first Laravel 13 / Livewire system (NIK as source of truth, jurisdiction-aware RBAC) and operate it on Laravel Cloud + AWS RDS/S3; ~312k requests/day on a right-sized Octane cluster.  
- Shipped Gemini KTP verification behind a Go OCR proxy, a provider-agnostic payment layer (Xendit/Midtrans/Flip/IFortePay), and SE Kapolri 2023 PDF certificates with QR verification.  
- Migrated ~47M legacy rows into PostgreSQL with a custom Go `COPY` pipeline (~37 minutes) and back the product with spec-driven development plus a large parallel Pest suite — sole developer, working with Claude Code / Codex / Cursor.

---

*Generated from the SimplePol repository (product spec v5, architecture, deployment notes, migration summaries, and git history) and the SimplePol section of github.com/aru-shiru/aru-shiru as of 16 August 2026. Not an official company brochure.*
