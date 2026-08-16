# ADVLive — Project Brief for Resume

**Role:** Tech Lead  
**Company / product:** ADVLive (Advansia)  
**Period:** October 2025 – present (rewrite; as of August 2026). Indonesia product live since **2021**.  
**Team:** 3 engineers — 1 tech lead + 2 junior–mid developers (Bimo Eka Saputra, Nana Sukarna)  
**Repo:** private monorepo (`lamaccatech/advlive`)

This document is intentionally detailed. Use **§1–3** as source material, then copy **§4 Suggested resume copy** when you need something short.

Public GitHub listing: **Advansia — Agricultural Sales Force Automation** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). That card describes the **live Indonesia Firestore predecessor** (Next.js + Expo + 17 Express Cloud Functions), not this ADVLive rewrite. Public metrics on that card: **50K+** sales records · **~100K** field activities · **100+** internal users.

---

## 1. Project overview

### 1.1 What it is

ADVLive is Advansia’s **internal super-app** for field and office employees. Advansia is an agribusiness (crop-protection / agricultural inputs) operating in **Indonesia** and **Malaysia**. The product replaces a legacy Firestore + Cloud Functions stack with a single TypeScript monorepo that serves both countries.

Employees use it to:

- Log **daily field work** (commercial visits, technical visits, Malaysia-only daily activities)
- Record **sales invoices** against retailers (and, in Indonesia, dealers)
- Track **channel stock** at retailers / dealers
- Register **farmers** and attach them to crops and admin regions
- Clock **attendance** with geofenced locations and work-shift windows
- Request **time-off and overtime** through a layered approval engine (feature-flagged; off by default until a country pays for HRIS extras)
- Maintain a **self-service HR profile** (personal data, family, education, documents, work history)
- Monitor **downline performance** if they are a supervisor on the organizational-unit tree

Admins use a **web dashboard** for master data, visibility, approvals, Excel export, and country-specific overview charts.

### 1.2 Why it exists (the business problem)

Advansia’s field force (Commercial Executives, Technical Executives, and Malaysia Daily Activity roles) works across a territory that does **not** match a simple org chart. A supervisor can cover people in several sales areas; an employee can report to someone outside their geography.

**Trigger.** Indonesia’s instance of this product has been live since **2021** (`lamaccatech/apps/advansia`, Cloud Firestore). When the Malaysia branch (HQ) asked to run the same product, copying that Firestore stack would have meant a second document store, a second function fleet, and the same integrity problems twice. Malaysia also has a different admin geography, product catalog, channel model (no dealers), and language.

**Decision.** Move the platform to **Postgres via Firebase Data Connect** so relations (regions, employees, retailers, invoices, stock, attendance) are constrained in the database, not only in application code. One TypeScript codebase; **one country per deployment**. Indonesia is not abandoned: ADVLive ID sits beside the live legacy Firebase app, and a Go migrator is ready to cut Indonesia over from Firestore when that country is scheduled.

ADVLive is therefore the platform that:

1. Stands up Malaysia as a **greenfield GCP / Firebase project** without forking the Indonesia codebase
2. Replaces Firestore’s document model with **relational Postgres** for data integrity
3. Prepares Indonesia’s **legacy cutover** (same schema, same API, same clients) instead of maintaining two stacks forever
4. Gives both countries one API, one dashboard, and one mobile app

### Predecessor (Indonesia, still live)

The Indonesia product started in **2021** and is still the live ID system. The current codebase lives in the Lamacca Tech monorepo at `apps/advansia` and is the source of the Firestore data the Go migrator reads. Stack:

| Surface | What it is |
|---|---|
| **Dashboard** | React **Next.js 15** admin (`advansia-admin`), static-exported to Firebase Hosting |
| **Mobile** | **Expo / React Native** (`advansia-mobile`), native Firebase Auth + Firestore + Storage |
| **API** | **17 Cloud Functions codebases**, one Express app per domain, each `onRequest` on Node 20 |

Function packages (each an Express router): employee, work (sessions/attendance), distributor, farmer, retailer, product, area, news, report, transaction, export, thumbnail, role, region, crop, technical-executive, stats.

Clients talk Firestore directly for a lot of reads; functions wrap the writes and reports that need extra logic. There are **no database-level foreign keys**. Shared libraries (`@advansia/schema`, `authorization`, `data-access`, `functions-middleware`, …) live next to other Lamacca products in the same monorepo.

That layout is why ADVLive collapsed the function fleet into **one NestJS API** and moved persistence to Data Connect / Postgres: a second country could not inherit “17 Express services + unconstrained documents” and stay coherent.

### 1.3 Clients and how they are used

| Surface | Who | What they do |
|---|---|---|
| **Mobile (Expo)** | Field staff | Home (products, news, farmer registration, accomplishment vs target), Account (activities, sales, stock, attendance, profile), Monitoring (supervisors only) |
| **Dashboard (Nuxt)** | Admins / supervisors | Master data, role & module access, OU tree, activity/sales/stock/attendance overviews, invoice verification, Excel export, approvals |
| **API (NestJS on Cloud Run)** | Both clients | Auth, CRUD, visibility resolution, scheduled jobs, export, news crawl |

Mobile ships as **two store apps** (Indonesia vs Malaysia): separate bundle IDs, branding, Firebase projects, and EAS profiles. The dashboard is a static site on Firebase Hosting, one site per country/env.

### 1.4 Product domains (what the system actually does)

These map 1:1 to the current-state specs in `docs/spec/`.

**Identity and access**

- Firebase Auth (email/password), one project per `(country, env)`
- Roles → modules (`subject.action`, e.g. `dca.create`). Malaysia also has direct per-user module grants
- Client-side CASL on mobile; dashboard route gating by module **groups** (ID) or module **codes** (MY)
- Superadmin bypass (`manage.all`)

**Geography vs visibility (two different trees)**

- **Sales geography:** Regional → Area → admin Region. Territory an employee *covers* (retailers, work areas)
- **Admin regions:** Indonesia = province / regency / district / village; Malaysia = state / district / sub-district (mukim)
- **Organizational units (OU):** the *reporting* tree. Who can see whose field data. Independent of geography, department, and job title. Supports `MEMBER` / `SUPERVISOR`, cross-OU grants, and per-OU **visibility domains** (DCA, DA, TRANSACTION, STOCK, ATTENDANCE, …)

**Field activities**

- **DCA** (Daily Commercial Activity) — both countries. ID create requires farmer, crop, region; MY is looser and maps products to groups
- **DA** (Daily Activity) — Malaysia only
- **TE** (Technical Activity) — Indonesia only
- Category catalogs, yearly/monthly targets, employee stats, country-specific overviews

**Sales (IRA / SRA)**

- Same backend (`/sales`). UI label is **IRA** in Indonesia, **SRA** in Malaysia
- Invoice grouping, line CRUD, identical-invoice detection, manual + scheduled verification
- ID requires a dealer on create; MY is retailer-direct (no dealer entity)

**Channel and stock**

- Retailers in both countries (assignment: ID `BY_AREA`, MY `BY_REGION`)
- Dealers and dealer-stock are **Indonesia only** — branched out of the MY schema, API, and UI
- Stock logs; MY has a retailer-stock overview

**Farmers and crops**

- Survey-driven farmer registration on mobile
- Field area is country-keyed: hectares (ID) vs acres (MY). Locale only translates the words
- Shared crop master data

**Products**

- ID: Category → ProductGroup → Product (SKU / variant)
- MY: Category → flat Product; “group” is a tag (`kind: product_group`)
- SIPP-tagged products (ID) disallow price override

**HRIS and attendance**

- User + `EmployeeInfo` + addresses, family, emergency contacts, documents, education, work history
- Work shifts, attendance locations (lat/lng + radius), work-session policies, employee ↔ policy assignment
- Clock-in / clock-out / request-clock flows; auto-checkout at midnight local TZ
- Approval engine on job-title layers (`UPPER_POSITION` / named employee), used by attendance, leave, overtime
- National ID validation is country-branched (MY 12-digit NRIC)

**Operations**

- News (MY crawls advansia.com every 6 hours, HTML-sanitized)
- Activity logs
- Excel export (retailers, stock, farmers, employees, activities, sales, attendance, …)
- Home stats (farmers, retailers, users, product groups; dealers ID-only)

### 1.5 Multi-country architecture (the hard part)

**One deployment serves one country.** `COUNTRY` (or `EXPO_PUBLIC_COUNTRY`) is resolved at **build / deploy time** into a `countryProfile` (timezone, Firebase project, locale, branding, region labels). There is no per-request country switch.

Divergence is deliberately **narrow**. Most endpoints are 95–99% identical. The convention (authored as `docs/COUNTRY_BRANCHING.md`) is a two-rung ladder:

| Rung | When | Mechanism |
|---|---|---|
| **1 — field** | 1–3 fields differ | Inline `country === 'id' ? … : …` (conditional spread / structural read) |
| **2 — unit** | A whole op or handler differs | One shared, country-**selected** service/provider behind a **typed SDK stub** (`useClass` / ternary). Both impls co-compile |

There is **no “duplicate the 1,900-line file to change 23 lines.”** That anti-pattern was rejected in review and written into the guideline.

**Data Connect compose.** Schema and GraphQL connectors are `shared + overlay`:

```
schema-shared/*.gql  +  schema-<country>/*.gql  →  dataconnect/schema/
connector-<country>/                            →  generated _*.gql
```

`pnpm dataconnect` / `pnpm dataconnect:my` compose one country, then generate the admin + client SDKs. At any moment the SDK exports **only that country’s** operations (`listProvinces` vs `listStates`). Typed stubs (`region.sdk`, `retailer.sdk`, `activity-writer`) let both country implementations type-check in one compile.

**Clients.** Mobile Metro resolves `*.my.tsx` the same way React Native resolves `.ios.tsx` — only the active country’s code is bundled. Dashboard uses filename suffixes for a subset of pages. Shared packages stay country-neutral; divergent Zod fields are tagged `@country-branched`.

**Four GCP projects**, separate billing:

| Env | Region | Notes |
|---|---|---|
| `id-staging` / `id-prod` | Jakarta (`asia-southeast2`) | ID prod is additive beside the live legacy Firebase app |
| `my-staging` / `my-prod` | Singapore (`asia-southeast1`) | Greenfield |

### 1.6 Data and migration

- **Indonesia (legacy cutover, prepared):** one-shot Go migrator (`tools/migration-id`) reads the **predecessor Firestore** (staging first; same collections as production `apps/advansia`) and writes **direct SQL** into Data Connect’s Postgres (`pgx`). The new ID Firebase/GCP project is additive — the live Firestore app stays up until cutover. Per-row inserts from a laptop through the Cloud SQL Auth Proxy were ~140 rows/s; a same-region ephemeral GCE runner (~100× faster) is provisioned on demand via Terraform and torn down after.
- **Malaysia:** no Firestore heritage. Greenfield tenant. `tools/seed-my` seeds from client workbooks (roles, OU tree, products, retailers, regions, crops, employees).
- Local dev uses **Docker Postgres** + Firebase Emulator Suite (Auth, Data Connect, Storage). Emulator Auth/Storage state is imported/exported; relational data lives in Docker so it survives emulator restarts. PGLite was dropped after it crashed (`ECONNRESET`) under seed load.

### 1.7 Runtime and cost shape

- NestJS API on **Cloud Run**: 2 vCPU / 2 GB, `min_instances = 0`, scale to zero overnight
- Production crons are **Cloud Scheduler HTTP POSTs that await the work** (auto-checkout, invoice verify, auto-approval, MY news crawl), not an always-on worker. In-process `@Cron` is a backup when an instance happens to be awake; a lock prevents double-runs
- Weekday keep-warm pings (prod every 10 min, 09:00–17:50 local) so field users do not hit a cold start
- Mobile reads `api_base_url`, `dashboard_url`, `home_slider_urls`, and `feature_*` flags from **Firebase Remote Config** — origin and unpaid-feature changes need no store resubmission
- Staging Cloud SQL can be stopped/started without destroy (`infra/scripts/staging.sh`) to cut idle cost

### 1.8 Timeline

| Period | What landed |
|---|---|
| **2021** | Indonesia product goes live (Firestore + Cloud Functions + dashboard + React Native). Still production for ID. |
| **Oct 2025** | ADVLive rewrite bootstrap after Malaysia HQ asked to run the product: Nest API (replacing 17 Express Cloud Functions), Nuxt dashboard, Expo mobile, Firebase Data Connect / Postgres instead of Firestore, first schemas (HR, attendance, time-off, support), shared packages |
| **Nov–Dec 2025** | Early feature work (lower commit volume) |
| **Jun 2026** | Country-profile package, Data Connect compose, branching guideline, OU visibility, MY seed suite, dealer removal from MY, Remote Config flags, Expo 54→56, Ultracite, dep upgrades |
| **Jul 2026** | MY staging Terraform, EAS country profiles, DA schema/API/mobile, farmer acres vs hectares, store-listing asset pipeline, infra runbooks |
| **Aug 2026** | Mobile monitoring + Victory Native charts, live overviews + Cloud Run scale-to-zero, news crawl, current-state domain specs, dead-code cleanup, OpenAPI alignment |

---

## 2. Tech stack

Grouped the way a hiring manager scans a resume. Prefer the **short list** in interviews; keep the **detail** here for a skills appendix or a “selected technologies” line.

### 2.1 Short list (resume-ready)

TypeScript · pnpm workspaces · Turborepo · NestJS · Nuxt 4 · Expo / React Native · Firebase (Auth, Data Connect, Hosting, Storage, Remote Config) · PostgreSQL · GraphQL · Zod · CASL · Terraform · Cloud Run · Cloud Scheduler · Go · Docker

### 2.2 Monorepo and language

- **TypeScript 5.9**, **Node 22**, **pnpm 9** workspaces, **Turborepo 2** pipelines (`build`, `lint`, `check-types`)
- Shared packages built with **tsup** (CJS + ESM) so Nest (CJS) and Nuxt/Expo (ESM) consume the same code
- **Syncpack** for exact, tree-wide dependency versions
- **Ultracite** (ESLint 10 + Prettier) on API and packages; Nuxt ESLint and `eslint-config-expo` on the clients
- **Go 1.26+** for Firestore→Postgres migration and MY workbook seeders (`pgx`, direct SQL against Data Connect table names)

### 2.3 Backend (`apps/api`)

- **NestJS 11** (Express 5), `class-validator` / `class-transformer`
- **Firebase Admin 14** — ID-token guard on every controller; Data Connect admin SDK for queries/mutations; Storage for attachments
- **Firebase Data Connect** — GraphQL schema → typed SDK → Cloud SQL Postgres. Schema authored as `.gql` with `@table` / `@col`; composed per country
- **Zod 4** shared request/response schemas (`@advlive/schema`) — same objects on API and clients
- **date-fns** + `@date-fns/tz` for country timezones (`Asia/Jakarta` / `Asia/Kuala_Lumpur`)
- **ExcelJS** for dashboard exports
- **Jest + Supertest** for API tests
- OpenAPI kept in line with live Nest routes (artifact for a planned Go port of the API)

### 2.4 Admin dashboard (`apps/dashboard`)

- **Nuxt 4** + **Nuxt UI 4** + **Tailwind CSS 4**
- **Vue 3** Composition API, **Nuxt layers** (one layer per domain: `ira`, `dca`, `employee`, `auth`, …)
- **@nuxtjs/i18n** consuming `@advlive/translations` (id / en / my)
- **TanStack Table**, **Unovis** charts, **VueUse**
- Firebase JS SDK (Auth + Remote Config). Static generate → **Firebase Hosting**
- Country variants via `*.my.vue` / `*.id.vue` filename suffixes

### 2.5 Mobile (`apps/mobile`)

- **Expo SDK 56**, **React Native 0.85**, **React 19**, **Expo Router**
- **NativeWind 4** + **Gluestack UI 3** + **Tailwind 3**
- **TanStack Query 5**, **React Hook Form** + Zod resolvers
- **Legend State** (local UI state), **CASL** (menu / screen ability)
- **i18next** / **react-i18next**
- Maps, camera, image picker, document picker, location, media library
- **Victory Native** + **Shopify React Native Skia** for Malaysia monitoring charts
- Firebase JS Auth with **AsyncStorage** persistence; Remote Config via REST (no RN JS SDK IndexedDB)
- Metro `moduleSuffixes` / `COUNTRY=my` file-swap (`*.my.tsx`) so a Malaysia build never ships Indonesia Data Connect ops
- **EAS** profiles per `(country, env)` for TestFlight / Play internal tracks; store listing assets generated (App Store + Play)

### 2.6 Shared packages (`packages/`)

| Package | Role |
|---|---|
| `@advlive/schema` | Zod entities, enums, create/edit/store/update forms; `@country-branched` tags |
| `@advlive/countries` | Build-time `countryProfile`: TZ, Firebase, branding, region levels, language defaults |
| `@advlive/constants` | Module groups, derived enums (sourced from schema) |
| `@advlive/authorization` | CASL ability factory from `subject.action` module codes |
| `@advlive/translations` | id / en / my trees for mobile + dashboard |
| `@advlive/utils` | Shared helpers (dates, formatting) |
| `@advlive/dataconnect` / `-admin` | Generated, gitignored, country-composed SDKs |

### 2.7 Data and local platform

- **PostgreSQL** (Docker locally; Cloud SQL / Data Connect in GCP)
- **Firebase Emulator Suite** — Auth, Data Connect, Storage; `--import` / `--export-on-exit`
- Compose tool: `dataconnect/compose.mjs` (Node) writes the active schema + connector overlays
- Legacy Indonesia source of truth for migration: **Cloud Firestore**

### 2.8 Cloud / IaC / delivery

- **Terraform** module `advlive-stack` × 4 env roots (`infra/envs/{id,my}-{staging,prod}`)
- **GCP:** Cloud Run, Artifact Registry, Secret Manager, Cloud Scheduler, optional GCE migration runner (IAP-only SSH), Workload Identity Federation deployer SAs
- **Firebase:** Auth, Data Connect, Storage (+ rules/CORS), Hosting (dashboard), Remote Config
- **Google Maps Geocoding API** (server key in Secret Manager) for attendance reverse-geocode
- Additive IAM on ID prod (`google_project_iam_member`, never authoritative bindings) so the new stack sits beside the live legacy app
- Remote Config feature flags: time-off, overtime, HRIS sections, dealer-stock — hide unpaid Malaysia features without a release

### 2.9 Quality and agent/human docs

- Workspace `check-types` must pass under **both** Data Connect composes
- Current-state **domain specs** (`docs/spec/*`) — code wins if they drift
- Country-branching guideline with a **review checklist** (no whole-file duplication, no `any`, no unguarded country SDK calls)
- Runbooks: MY/ID local DB reset, MY staging reset, SQL region move, app-update / Data Connect sync
- Infra README documents per-flavor deploy gotchas for humans **and** coding agents

### 2.10 Notable engineering choices (talking points)

- **Build-time country, not `if (req.country)`.** Correct for store binaries, Firebase projects, and a single-country Data Connect SDK.
- **Typed stubs instead of `require()` or per-country `tsconfig`.** Both country impls co-compile; `check-types` is one pass.
- **OU tree ≠ geography ≠ department ≠ approval chain.** Four concerns that look similar and were deliberately split.
- **Scale-to-zero API + request-scoped Scheduler.** Cron work still completes; idle staging/prod does not pay for a warm instance overnight.
- **Remote Config for origins and unpaid features.** Store review is not on the critical path for a URL change or an HRIS upsell.
- **Postgres / Data Connect instead of cloning Firestore.** Malaysia HQ wanting the product was the forcing function; relational integrity (FKs, typed schema) was the reason not to copy `apps/advansia` wholesale.
- **Indonesia cutover is a migrator, not a rewrite-in-place.** Legacy Next.js + RN + 17 Express functions stay live; Go `COPY`-style SQL load is rehearsed against staging Firestore.
- **Go seeders writing SQL, not the Data Connect SDK.** Survives emulator fragility; same-region GCE runner for multi-million-row ID migration.
- **Specs that describe today, not the roadmap.** Leftovers called out so agents and juniors do not “finish” a cutover that is intentionally gated.

---

## 3. My contribution

**Role in practice:** tech lead on a 3-person team. I review and merge work from two junior–mid developers, write the conventions they implement against, and still own the platform layers end-to-end (schema, API, infra, mobile flavor, seeders, docs). I do not only review — git shows I implement the hard paths myself when the design is new or the blast radius is high.

### 3.1 Leadership and review

- **Routine review of teammate PRs.** The two developers (Bimo — dashboard/mobile/API feature work; Nana — dashboard domains, access, attendance, product, branching) opened the large majority of product PRs (~72 and ~47 respectively). I merge, unblock, and send work back to the branching / visibility / i18n conventions rather than letting country forks accumulate.
- **Wrote the review standard.** `docs/COUNTRY_BRANCHING.md` is an executable guideline: rung decision, typed-stub pattern, anti-patterns to reject (whole-controller duplication, `any`, `require()`, unguarded country SDK calls), and a PR checklist. Live reference impls (`RegionService`, `RETAILER_WRITER`, `ACTIVITY_WRITER`) are the copy-paste targets I point juniors at.
- **Hands-on pairing via code.** When a change is architectural (compose, OU visibility, dealer removal, Remote Config, EAS, Cloud Run jobs), I implement it, then leave the pattern for the next feature PR. When a change is product UI (filters, overviews, form polish), I review and only dive in for type errors, country polarity, or access bugs.
- **Merged integration PRs** from the team (examples from git: approval-logic, model cleanup, filters/translations, invoice price, product/department/position, monitoring charts) and rebased/fixed compile breaks under the Indonesia compose after Malaysia-shaped changes.
- **Agent- and human-facing docs** so review comments do not have to re-explain deploy flavor, OU vs geography, or “don’t duplicate the file.” Domain specs, infra gotchas, and runbooks are written as current-state, not aspiration.

### 3.2 Platform I designed and implemented

These are the pieces I authored or drove (commit history: country profile, compose, guideline, OU, Terraform, flags, EAS, specs).

**Monorepo foundation (Oct 2025)**  
Initialized the workspace: Nest API, Nuxt dashboard, Expo app, Firebase, first Data Connect schemas (users/HR, attendance, time-off, support), and the shared library layout (`schema`, `utils`, later `countries`, `authorization`, `translations`).

**Country-as-a-profile**  
`@advlive/countries` — timezone, Firebase identity, branding (ADV Live MY / Advansia Sdn Bhd vs Indonesia), language defaults (ID: `id`/`en`; MY: `my`/`en`), region level labels. ID is the default polarity everywhere (`=== 'id' ? … : …`) so a third country does not flip existing branches.

**Data Connect composition**  
`compose.mjs` + `schema-shared` / `schema-{id,my}` / `connector-{id,my}`. Generated `_*.gql` and SDKs are gitignored. Typed stubs so Nest modules type-check under either compose. Malaysia-only ops stay out of the shared connector.

**Organizational-unit visibility**  
Designed the reporting tree (`OrganizationalUnit`, `EmployeeOURole`, `EmployeeAccessGrant`, `OrganizationalUnitCoverage`), `OuHierarchyService`, domain-aware resolvers (DCA / DA / TRANSACTION / STOCK / …), supervisor check for the mobile Monitoring tab, dashboard OU coverage editor, and both seed paths (ID from geography / Firestore; MY from client spreadsheet).

**Malaysia tenant, without a fork**  
- Schema overlays (Region 3-level, `EmployeeInfo` statutory fields, DA, no Dealer)  
- API: dealer module registered only for ID; retailer/activity writers country-selected; DA module; SRA overview  
- `tools/seed-my`: roles, OU tree, products (family + tags), retailers (`BY_REGION`), regions, crops, employees  
- Mobile Metro flavor + EAS `COUNTRY` profiles + store listing generator  
- Dashboard filename suffixes, MY access pages, dealer menu hidden  

**Infra and cost**  
Terraform stack for four projects; Cloud Run scale-to-zero; Scheduler-backed jobs; keep-warm pings; Geocoding secret bootstrap; staging stop/start; ephemeral GCE migration runner; Remote Config for API/dashboard origins and `feature_*` flags (HRIS extras ship hidden on MY until paid).

**Client platform work I still write**  
- Mobile: country flavor resolution, farmer registration hardening, acres vs hectares, DA screens, OU monitoring + Victory Native charts, environment badge, photo-picker permissions, EAS monorepo/TestFlight/Play paths, About/news alignment with advansia.com  
- Dashboard: feature-flag route guards, country-specific component resolution, i18n from the shared package, MY access scoping  
- API: ActivityWriter / RetailerWriter / RegionService, live DCA/TE overviews (no snapshot cron), news crawl + HTML sanitize, OpenAPI sync  

**Housekeeping that keeps a 3-person team fast**  
Expo 54→56, ESLint 10 / Ultracite, exact dep pins, type-check cleanup across all three apps, dead-code inventory and deletion (unused PGlite, leftover MY TE, unused packages), current-state specs for every domain so juniors and agents do not invent architecture.

### 3.3 What the git history supports (facts, not fluff)

| Signal | Number | How to read it |
|---|---|---|
| Commits as author | ~389 (Oct 2025 – Aug 2026) | Second-highest after the most active junior; volume is not the point — see themes |
| Lines touched | ~346k added / ~263k deleted | Inflated by lockfiles, generated SDKs (later gitignored), assets. Use as “I touch the whole tree,” not as a vanity metric |
| Paths | Dashboard, mobile, API, `packages/*`, `dataconnect/*`, `infra/*`, `tools/migration-id`, `tools/seed-my`, `docs/spec`, runbooks | Full-stack + platform, not a single layer |
| Commit mix | ~149 `feat`, ~51 `fix`, ~30 `refactor`, ~27 `docs`, plus `chore` / `style` | Features and design docs, not only cleanup |
| PRs I opened | 9 (large, architectural) | Country alignment, translations, schema, MY seed + OU, dealer removal, MY product seeder, tooling upgrade, monitoring charts |
| PRs I merged | 14 merge commits in git | Includes teammate feature PRs, not only my own |
| Peak month | Jun 2026 — 165 commits | Country compose, OU, MY seed, dealer cut, flags, Expo/Ultracite — the platform month |

**PRs I authored (titles):**

- Alignment W4+W5: country profile, per-country Data Connect, mobile/dashboard app layer  
- Consolidate translations; source dashboard i18n from `@advlive/translations`  
- Unreference Drizzle; hand-write mobile Zod schemas  
- Deps upgrade, Expo 56, Ultracite  
- Malaysia tenant seed suite + OU visibility  
- Remove Dealer from Malaysia  
- MY product seeder (family grouping + Advansia images)  
- Victory Native monitoring charts for Malaysia  

### 3.4 How I work with the two developers

Typical split, visible in PR titles vs my commits:

| They take | I take / I review for |
|---|---|
| Dashboard list/filter/sort/overview polish | Country polarity, access rules, shared components |
| Mobile UI migration (Gluestack), navigation, form styling | Flavor files, Data Connect imports, Remote Config, store/EAS |
| Feature CRUD (attendance, product tags, employee detail, export) | Schema overlays, Nest module registration, Zod `@country-branched` |
| Invoice verify / approval flow tweaks | Approval engine invariants, feature-flag gating |
| Seed extras for a screen they are building | Canonical seed order, OU coverage, role → module grants |

I still jump into their layer when the bug is a compose mismatch, a visibility leak, or a type error that only appears under the other country’s SDK.

### 3.5 Impact (qualitative — no vanity user counts)

- **One codebase, two countries, four environments** — Malaysia did not become a fork of the Indonesia Firestore app.  
- **Stack change had a reason.** Malaysia HQ’s request is why Postgres/Data Connect replaced Firestore: data integrity first, then a second country, then Indonesia’s prepared cutover.  
- **Legacy Indonesia data** has a repeatable Firestore → Postgres path from the live `apps/advansia` predecessor; Malaysia has a workbook → seed path.  
- **Unpaid HRIS / leave / overtime** can be turned on per Firebase project without a mobile release.  
- **Cloud Run idle cost** is near-zero; jobs still run.  
- **Juniors have a written rung system** instead of copying 1,900-line controllers.  
- **Specs and runbooks** make the next country (called out as SG in the guideline) a profile + overlay, not a rewrite.

---

## 4. Suggested resume copy

Trim or mix. Do not paste the whole brief.

### 4.1 Header line

**Tech Lead, ADVLive (Advansia)** — Oct 2025 – present (Indonesia product since 2021)  
Internal field-operations platform for an agribusiness in Indonesia and Malaysia. NestJS, Nuxt, Expo, Firebase Data Connect / Postgres, Terraform. Team of 3.

### 4.2 Two-sentence overview

Led a 3-person team building ADVLive, Advansia’s internal super-app for field sales, stock, farmer registration, attendance, and HR. When Malaysia HQ asked to run the Indonesia product (live since 2021 on Firestore), designed a Postgres / Data Connect rewrite on one TypeScript monorepo — NestJS, Nuxt, Expo, Cloud Run — so a second country is a compose overlay, not a fork, and Indonesia has a prepared cutover.

### 4.3 Bullet options (pick 4–6)

- Led a 3-engineer team (2 junior–mid) as hands-on tech lead: routine code review, branching/visibility conventions, and implementation of the platform layers (schema, API, infra, mobile flavor).
- Designed a **build-time country model** (typed SDK stubs, Data Connect `shared + overlay` compose, Metro `*.my.tsx` flavors) so Indonesia and Malaysia share ~95% of the code without runtime country switching or file-level forks.
- Built **organizational-unit visibility** (reporting tree, domain coverage, cross-OU grants) so supervisors see the right field data independently of sales geography, department, or approval chain.
- Replaced the live Indonesia Firestore stack (in production since **2021**: Next.js dashboard, React Native, 17 Express Cloud Functions) with **Postgres via Firebase Data Connect** after Malaysia HQ requested the same product — for relational integrity, not a document-store clone.
- Stood up **four GCP environments** with Terraform (Cloud Run scale-to-zero, Cloud Scheduler jobs, Remote Config feature flags) and a Go Firestore→Postgres migrator for the Indonesia predecessor plus a Malaysia workbook seeder.
- Shipped **Expo** field apps for both countries (EAS profiles, store listings, Remote Config origins) covering DCA/DA/TE activities, IRA/SRA invoicing, stock, geofenced attendance, and farmer registration.
- Wrote current-state domain specs, a country-branching review checklist, and deploy runbooks so product PRs stay type-safe under both Data Connect composes.

### 4.4 Skills line

TypeScript, NestJS, Nuxt 4, Expo/React Native, Firebase (Auth, Data Connect, Remote Config, Hosting), PostgreSQL, GraphQL, Zod, CASL, Terraform, Cloud Run, Go, pnpm/Turborepo, i18n (id/en/my)

### 4.5 What not to claim

- Do not invent MAU, revenue, or “migrated N million rows in production” unless you have the number from ops. The runner design is “~100× vs laptop proxy”; the prior-project anecdote in infra docs is not automatically this project’s prod result.
- GitHub Selected Projects lists this as **Advansia**, not ADVLive, and still describes the **Firestore + 17 Cloud Functions** Indonesia app. Interviewers who only read the profile will hear that stack. This brief is the rewrite (Data Connect / Malaysia).
- Public metrics already on that GitHub card: **50K+** sales records, **~100K** field activities, **100+** internal users — cite the profile for the Indonesia predecessor; do not treat them as Malaysia launch numbers.
- GitHub `reviewed-by:aru-shiru` is empty — reviews happened as merges, comments outside the review API, and in-person/async guidance. Say **“reviewed and merged teammate PRs / set the review standard”**, not “N GitHub reviews.”
- Line-count (~346k / ~263k) is a weak resume number (lockfile + generated + assets). Prefer domains and architecture.

---

## 5. Glossary (if a recruiter asks)

| Term | Meaning |
|---|---|
| **DCA** | Daily Commercial Activity — field visit log (both countries) |
| **DA** | Daily Activity — Malaysia-only field log |
| **TE** | Technical Activity — Indonesia-only |
| **IRA / SRA** | Invoice / sales transaction. Same API; IRA = Indonesia label, SRA = Malaysia |
| **CE** | Commercial Executive (ID column name; MY says “User”) |
| **OU** | Organizational unit — reporting tree for data visibility |
| **Compose** | Generate one country’s Data Connect schema + SDK from shared + overlay |
| **Predecessor / `apps/advansia`** | Indonesia product, live since 2021: Next.js + Expo RN + Express Cloud Functions on Firestore |
| **Rung 1 / Rung 2** | Field-level vs unit-level country branch; never duplicate the whole file |
| **SIPP** | ID product tag that locks invoice price |

---

*Generated from the ADVLive repository (specs, packages, infra, and git history for Muhammad Dzul Arsyil / `aru-shiru`), the live predecessor in `lamaccatech/apps/advansia`, and the Advansia section of github.com/aru-shiru/aru-shiru as of 16 August 2026. Facts are from the repos; user counts and commercial outcomes beyond the public GitHub card are omitted on purpose.*
