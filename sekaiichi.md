# SDS / Sekaiichi — Resume Project Brief

**Role:** Sole Full-Stack Engineer (product, architecture, implementation, client communication, ops)  
**Company / product:** SDS — PT Sekaiichi Dwiputra Service (`app.sds.co.id`)  
**Period:** July 2024 – present (as of August 2026; last code landing June 2026)  
**Team:** Sole developer  
**Repo:** `lamaccatech/apps/sekaiichi` (+ `packages/@sekaiichi`) in the Lamacca Tech monorepo  

This document is a **source brief**, not a finished resume entry. Sections 1–3 are detailed on purpose so they can be trimmed later. Section 4 has ready-to-paste resume bullets.

Public GitHub listing for this work: **SDS — Building Services Management Platform** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru).

---

## 1. Project overview

### 1.1 One-sentence pitch

SDS is a production facility-operations platform for PT Sekaiichi Dwiputra Service (Jakarta): field employees clock in with GPS + selfie or fingerprint terminals, execute photo-proven cleaning tasks, move inventory, open building tickets, and progress from public job apply through PKWT/PKWTT contracts — used by workers, applicants, site clients, and office admins.

### 1.2 Problem

A building-services company runs people across **many gedung** (sites): cleaners, TAD/professional staff, QC, HRD, and the client who occupies the building. Attendance used to mean a mix of paper, a device punch, and “I was nearby.” Inventory walked in and out without a scan trail. Cleaning quality was a verbal report. Hiring was email-and-WhatsApp until a Word contract appeared.

SDS is the system of record for that company: one Postgres database (Supabase), one module-based permission model, one mobile app for the field, one Nuxt admin for the office, and a small Fly.io process that speaks **ZKTeco iClock** so physical fingerprint scanners write the **same** check-in records as the phone.

### 1.3 Who uses it

| Actor | Surface | What they do |
|---|---|---|
| **Active employees** (cleaner / TAD with a running contract) | Mobile | Clock in/out (geofence + selfie), cleaning tasks + office assistance, tickets, stock in/out/opname, leave/absence/overtime, own contracts and salary slips |
| **Applicants** | Mobile + public job board | Profile, documents (KTP, KK, ijazah, CV, SKCK, …), vacancies, application pipeline, draft-contract approve/reject |
| **Former employees** | Mobile | Similar to applicant: documents, contracts, password |
| **Site clients** (`id_role = 7`) | Mobile | Monitor attendance and fingerprint logs, cleaning reports, leave/overtime verify, tickets — not the field check-in set |
| **Admin / HRD / ops / QC** | Nuxt dashboard | Master data (buildings, areas, spaces, shifts, geofences), employees, recruitment, attendance reports, fingerprint device registry, inventory, roles/modules, exports, contract PDF release |
| **Public** | Nuxt (no login) | Job board (`/lowongan`), contract verification by QR (`/public/contracts/...`), privacy policy |

A user is building-scoped (`gedung_user`). Admin has a **building switcher**. Contracts distinguish **cleaner** vs **TAD** legal templates.

### 1.4 Core product ideas

**The building is the tenant slice.** Employees, shifts, geofences, scanners, cleaning spaces, and tickets hang off `gedung`. Timezone is per building. “Today,” late seconds, and overnight shifts (`pergantian_hari`) use that zone.

**Attendance is one domain with two capture paths.** Phone: `expo-location` + Turf distance vs `lokasi_absen.radius_dalam_meter`, front-camera selfie to bucket `absensi`, RPCs `transaction_absen_masuk` / `_pulang` / `_update_lokasi` (QC trail on `sesi_kerja_riwayat_lokasi`). Device: ZKTeco ATTLOG → ADMS → `transaction_sync_attendance_log_record` → the **same** `absensi` / `sesi_kerja` rows. Auto-checkout is **pg_cron** every minute (`transaction_auto_checkout`), not a Firebase scheduler.

**Cleaning is photo proof against a space.** Tasks: Daily / General / Wash and Fold / Maintenance, periods Harian…Tahunan, mapped to `bagian_area_gedung`. Realization RPC + photo in bucket `tugas`. Extra work is **Office Assistance**. Admin PDFs are Handlebars + Puppeteer.

**Inventory is QR + photo, not a spreadsheet.** Items Consumable vs Asset. Per-unit `identifikasi_barang.kode_barang` rendered with `uqr`. Mobile stock-out uses `expo-camera` barcode scan. Directions Keluar / Masuk; stock opname RPC; photos in `perpindahan_barang`.

**HR starts on a public URL.** Vacancies → apply (web or app) → phases Tahap 1–3 → Interview → Siap Kontrak → Draft (sent / approved / rejected by candidate) → released PKWT / PKWTT / PHL PDF. Same shape for contract **extensions**. QR on the PDF hits the public contract page.

**Permissions are modules, not a hard-coded role enum.** `role` → `modul_role` → JWT claim `user_role` via a Supabase custom access-token hook → Postgres `authorize('entity.action')` used by **RLS**, plus CASL on both clients, plus the same RPC check on Firebase export routes.

### 1.5 End-to-end journeys (compressed)

**Field clock-in.** Assigned location + shift → walk into the geofence (or “all locations” if the lock flag is off) → selfie → RPC check (active contract at this building, no leave/absence today, window ~1 hour before / during shift, overnight OK) → reverse-geocode via Radar.io edge function → session. Checkout: RPC or midnight-local auto-checkout. Mid-shift location updates for QC.

**Fingerprint punch.** Device serial registered to a building → employees enrolled on the device → iClock ATTLOG → Fly.io ADMS (SQLite buffer) → Supabase sync RPC → attendance session. Admin exports ADMS logs to Excel when a punch didn’t map.

**Cleaning round.** Worker opens today’s tasks for a space → photo → realization. Supervisor/admin pull productivity + PDF reports.

**Stock movement.** Scan unit QR → in/out/opname RPC + photo.

**Hire to contract.** Public apply or in-app → documents → interview notes → draft contract (cleaner vs TAD template) → candidate approve on mobile → Firebase Function generates Puppeteer PDF with QR → storage `dokumen_kontrak` → public verification page.

**Ticket.** Occupant/ops open “Request and Complain” (`open` → `on_progress` → `done`) with optional space + photo.

### 1.6 Production footprint

| Surface | Where |
|---|---|
| Admin | Nuxt, prod `https://app.sds.co.id`, staging `https://staging.app.sds.co.id` |
| Mobile | Expo / EAS, display name **SDS**, bundle `com.sekaiichiservice.mobile` |
| Database / Auth / Storage | Supabase (prod `gogptqowvegvtvexzbeq`, staging `encntgxaulhwnbqbpbru`), Postgres 15 |
| Reports API | Firebase Functions v2 (`sekaiichi-dwiputra-service`), Express `onRequest`, region `asia-southeast1`, 4 GiB / 10 min timeout (Chromium PDFs) |
| Fingerprint bridge | Fly.io `sekaiichi-adms`, Singapore, `shared-cpu-2x` / 512 MB, persistent SQLite volume, HTTP 8080, `min_machines_running = 1` |

Firebase Hosting is **not** used for the dashboard. Overnight checkout is **inside Postgres**.

**Public resume metric (GitHub profile, not in this repo):** 2,000+ field employees using the app daily. Do not invent other volume numbers from the seed dump.

### 1.7 Timeline (from git)

Monorepo history starts at migration, not a pre-2024 standalone repo. **951 commits, single author.**

| Period | What landed |
|---|---|
| **Jul 2024** | Admin migrate into Lamacca Tech; mobile init. Peak month (~150 commits) |
| **Aug–Nov 2024** | Buildings, attendance, cleaning, inventory, recruitment, CASL/RLS loop |
| **Dec 2024** | Firebase Functions export API (Excel + Puppeteer PDFs) |
| **Jan 2025** | Contracts, employee activate/deactivate, salary-slip storage |
| **Feb–Jun 2025** | Lower volume (ops / small features) |
| **Jul 2025** | React 19 / Expo 53 upgrades (merge PR) |
| **Sep 2025** | ADMS Express server (iClock), Fly.io `sekaiichi-adms` |
| **Oct–Nov 2025** | Fingerprint tables, RPCs, device enrollment, attendance-log sync |
| **2026** | Scanner search, contract NRP, query tweaks; last landing 22 Jun 2026 |

### 1.8 Notable subsystems beyond the happy path

- **Leave / absence / overtime** with Supervisor → QC → HRD verification (mobile verification screens, not admin-only).
- **Activity log** (`riwayat_aktivitas`) and in-app **notifications**.
- **Salary slips** uploaded by admin, listed on mobile (`slip_gaji` + storage).
- **NRP** assignment RPC.
- **Building archive / restore**; employee transfer between buildings.
- **Contract regenerate** HTTP route (PR #322).
- **Edge `resync-attendance-log`** to replay punches that failed to map.
- **Public job board** so the company is not the only intake path.
- **Two Firebase function codebases** (`functions` / `functions-staging`) sharing the same Express surface.

---

## 2. Tech stack & technology highlights

### 2.1 Short list (resume-ready)

TypeScript · Expo / React Native · Nuxt 3 (v4 compat) · Supabase (Postgres, Auth, Storage, RLS, Edge Functions, pg_cron) · Firebase Cloud Functions · Puppeteer · ExcelJS · Fly.io · ZKTeco iClock · Mapbox · Google Maps · Radar.io · CASL · Zod · TanStack Query · Pinia · Turborepo

### 2.2 Surfaces

| App | Stack |
|---|---|
| **Admin** (`sekaiichi-admin`) | Nuxt **3.17.6** (`future.compatibilityVersion: 4`), **@nuxt/ui-pro**, Pinia, `@nuxtjs/supabase`, **12 Nuxt layers** (one per domain), Mapbox, TipTap, Unovis, VueUse, CASL |
| **Mobile** (`sekaiichi-mobile`) | Expo **53**, React **19**, RN **0.79**, Expo Router, TanStack Query 5, Legend State, CASL, Google Maps (`react-native-maps`), Turf, Expo Camera / Location / ImagePicker |
| **Reports API** | Firebase Functions **v2** Node 20, Express, Puppeteer 23 + Handlebars, ExcelJS, `uqr` / svg64 |
| **ADMS** | Express + **better-sqlite3**, Zod, Supabase JS; Docker Node 20 on Fly.io |
| **Scripts** | One-off Drizzle + `pg` + csv-parse jobs (import karyawan/gedung, resync attendance, contracts) |

### 2.3 Shared packages (`@lamaccatech-sekaiichi/*`)

| Package | Role |
|---|---|
| `schema` | Zod forms + generated `database.type.ts` |
| `data-access` | Typed Supabase RPC wrappers |
| `authorization` | CASL `defineAbilityUsingPermission` from `kode_modul` |
| `config` | Routes, default geofence radius (50 m), office pin |
| `utils` | Dates, IDR, GeoJSON, contract filenames |
| `ui-mobile` | Shared RN controls |

### 2.4 Data layer (the “we didn’t just bind a table” list)

**Postgres as the API.** Clients call `query_*` / `transaction_*` RPCs (≈109 read + ≈107 write migrations). Direct table writes from the app are the exception. Generated types list ~80 of each alive in the schema.

**~340 SQL migrations.** Pattern: `create_*` → `*_table_policy` → `query_*` / `transaction_*` → `setup_*_storage`. About 52 RLS policy files. Fingerprint domain is ~23 files (Oct 2025).

**JWT hook + RLS.** `custom_access_token_hook` stamps `user_role`. `authorize('absensi.perform')` is security-definer and is what RLS policies call — same string CASL uses. Building rows additionally require `gedung_user`.

**Storage buckets:** `absensi`, `avatars`, `dokumen_kontrak`, `tugas`, `ticket`, `dokumen_user`, `perpindahan_barang`, `slip_gaji`.

**Edge functions (Deno):** `create-user`, `change-email`, `reset-password`, `count-work-session-durations`, `resync-attendance-log`, `translate-coordinate` (JWT verify **off**, Radar reverse geocode). Registration-success mail exists but is **disabled** in `config.toml`.

### 2.5 iClock / ADMS (the talking-point)

ZKTeco terminals speak a small HTTP dialect. The bridge is **not** a Firebase Function (cold start + HTTPS-only would fight device firmware). It is a **always-on Fly machine** in Singapore:

| Route | Role |
|---|---|
| `GET /iclock/cdata?SN=` | Handshake / options (timezone 7, realtime, stamps) |
| `POST /iclock/cdata?table=` | OPERLOG / ATTLOG tab-separated punches |
| `GET /iclock/getrequest?SN=` | Heartbeat → online-status RPC |

Local SQLite survives a bad Supabase blip; then `transaction_sync_attendance_log_record` maps employee-id + timestamp + punch type onto the GPS attendance model. Device enrollment and unsynced-punch counts are admin RPCs.

### 2.6 Reports split

| Job | Tool | Why |
|---|---|---|
| Legal contracts, cleaning packs, overtime letters | Puppeteer + Handlebars | Layout fidelity, QR embed |
| Attendance / employees / tickets / buildings / ADMS logs | ExcelJS | Ops paste into payroll |

Single Express `api` function; each POST checks `authorize(...)` with the caller’s bearer token. CORS allowlist: prod admin origin + localhost. Logo from a GCS bucket `sekaiichi-dwiputra-services-exports`.

### 2.7 Architecture highlights

- **RPC-first writes** so RLS, late-calc, overnight shifts, and “already clocked in” live in one place — phone, scanner, and admin cannot disagree.
- **Two attendance transports, one schema.** Geofence selfie and iClock punch both produce `absensi` + `sesi_kerja`.
- **Module CASL + JWT + RLS** — three layers, one permission vocabulary (`subject.action`).
- **Nuxt layers = domains.** `absensi`, `cleaning`, `gedung`, `inventaris`, `karyawan`, `rekrutmen`, `role`, `landing`, … so the admin app does not become a 1,900-line router.
- **Always-on edge for dumb devices, scale-to-zero for Chrome.** Fly for iClock; Firebase 10-minute / 4 GiB only when someone exports a PDF.
- **pg_cron in the database** for auto-checkout — no second scheduler to forget.

---

## 3. Contribution

### 3.1 Role, in one paragraph

I am the **sole engineer** on SDS / Sekaiichi. Git on `apps/sekaiichi` + `packages/@sekaiichi` is **951 commits, one author: Muhammad Dzul Arsyil** (Jul 2024 – Jun 2026). There is no separate mobile, web, backend, or DevOps person. I talked to the client (PT Sekaiichi Dwiputra Service) about geofences, shift windows, PKWT wording, scanner serials, and what a “client” role on mobile is allowed to see — then implemented it.

The public GitHub blurb says “led a team.” **For this codebase, that is the wrong claim.** Use **sole full-stack engineer, client-facing.**

### 3.2 What “doing it all” covered

**Product & client.** Translated building-services practice (multi-site shifts, overnight, QC location trail, cleaner vs TAD contracts, site-client monitoring) into tables, RPCs, and two UIs. Intake was not a spec dump — it was conversations plus iterating modules when HRD asked for another leave type or a public job URL.

**Architecture.** Chose Supabase (Auth + Postgres + RLS + Storage + Edge) over a custom Nest+Firebase clone of sibling Lamacca apps; kept Firebase only where Chromium must run; put iClock on Fly because the device cannot wait for a cold start.

**Implementation.** Twelve Nuxt layers; Expo Router field app; ~340 migrations and the RPC envelope; CASL package; Puppeteer contract/cleaning/overtime PDFs; Excel exports; Deno edge functions; ADMS iClock server + SQLite buffer.

**IoT.** Wrote the handshake/ATTLOG parser, enrollment RPCs, and the mapping from device punch → work session, with admin Excel for when a serial or NIK did not match.

**Quality & ops.** Shared Zod/schema package so mobile and admin do not drift; staging vs prod Supabase refs in EAS; two Functions codebases; Fly volume for ADMS logs. Scripts for one-off data imports from the client’s workbooks.

### 3.3 What the git history supports

| Signal | Number | How to read it |
|---|---|---|
| Commits as author | **951** (Jul 2024 – Jun 2026) | Only author |
| Other authors | **0** | Sole developer is defensible |
| Peak month | Jul 2024 — ~150 | Greenfield after monorepo migrate |
| ADMS | First commit 30 Sep 2025 | Fingerprint arrived after GPS attendance was already live |
| Functions | First commit 9 Dec 2024 | Reporting came after the operational loop |

PRs in this tree are self-merges (React 19, contract regenerate) — not a review team.

### 3.4 Outcomes you can defend

- A **multi-sided** ops product (employee, applicant, site client, HRD) on one permission vocabulary.
- Attendance that **survives both a phone and a wall-mounted scanner** without a second “device hours” table as source of truth.
- Hiring that is a **URL**, not a WhatsApp PDF — public apply → draft → e-signed-style approve → QR-verifiable PKWT.
- Client conversation to **Postgres RPC** to **store listing**, without a product manager in the middle.
- Published daily-user claim on GitHub: **2,000+ field employees** (cite the profile, not this repo).

---

## 4. Suggested resume copy

Trim or mix. Do not paste the whole brief.

### 4.1 Header line

**Sole Full-Stack Engineer, SDS (PT Sekaiichi Dwiputra Service)** — Jul 2024 – present  
Facility-operations platform: GPS + fingerprint attendance, cleaning proof, inventory, ticketing, recruitment-to-contract. Expo, Nuxt, Supabase, Fly.io iClock bridge. Sole developer.

### 4.2 Short blurb (~40 words)

Sole engineer for SDS, a production facility-ops platform for an Indonesian building-services company: geofenced selfie attendance, ZKTeco fingerprint terminals, photo-proven cleaning, QR inventory, and PKWT PDFs — Expo, Nuxt, Supabase, and a Fly.io iClock bridge.

### 4.3 Medium blurb (~80 words)

Designed and shipped SDS (Sekaiichi) as the sole developer, working directly with the client. The system runs multi-site cleaning and TAD operations: GPS + selfie clock-in (Turf geofence), the same sessions from ZKTeco iClock punches via a Fly.io ADMS, cleaning/task photo proof, scanned inventory, building tickets, and a public job board through PKWT/PKWTT contract PDFs (Puppeteer + QR). Postgres RPCs + RLS + CASL are the contract between Expo, Nuxt, and Firebase export functions.

### 4.4 Bullet options (pick 4–6)

- Sole full-stack engineer for a production facility-ops platform (employees, applicants, site clients, HRD): Expo mobile, Nuxt 3 admin, Supabase Postgres, Firebase PDF/Excel exports — **client communication included**, not a ticket queue.
- Modeled **attendance once**: geofenced selfie (Expo Location + Turf) and **ZKTeco iClock** punches on Fly.io write the same `absensi` / work-session rows; auto-checkout is `pg_cron`.
- Built **RLS + JWT role claim + CASL** from one `entity.action` module table so building-scoped data and export HTTP routes share the same permission check.
- Implemented **RPC-first** writes (~100 `transaction_*` functions) for clock-in, leave chain (Supervisor → QC → HRD), inventory, tickets, and contract draft/approve/release.
- Generated **PKWT/PKWTT/PHL** PDFs (Puppeteer + Handlebars + QR) and a login-free verification page; public `/lowongan` feeds the same recruitment pipeline as the app.
- Bridged **physical fingerprint scanners** (iClock handshake/ATTLOG, local SQLite buffer, enrollment RPCs) after GPS attendance was already live — not a greenfield-only demo.

### 4.5 Skills line

TypeScript, Expo/React Native, Nuxt 3, Supabase (Postgres, RLS, Auth, Storage, Edge, pg_cron), Firebase Cloud Functions, Puppeteer, ExcelJS, Fly.io, ZKTeco iClock, Mapbox, Google Maps, Radar.io, CASL, Zod, TanStack Query, Pinia, Turborepo

### 4.6 Things to say carefully (or not at all)

| Claim | Safer wording |
|---|---|
| “I led a team” (GitHub SDS blurb) | **Sole developer** for this repo. 951/951 commits. |
| “2,000+ employees daily” | Already on the public profile. Do not invent attendance-events/day unless ops gives a number. |
| “I built ZKTeco firmware” | You built the **iClock HTTP ADMS** that those devices already speak. |
| Default passwords / API keys in client apps | Never put them in a resume or this repo of briefs. |
| “Realtime Firebase backend” | **Supabase Postgres** is the system of record; Firebase is the Chromium/Excel worker. |

---

## 5. Suggested 3-bullet resume block (already trimmed)

**SDS (Sekaiichi)** — Sole Full-Stack Engineer *(Jul 2024 – present)*  
Facility-operations platform for PT Sekaiichi Dwiputra Service (attendance, cleaning, inventory, ticketing, hiring-to-contract).

- Shipped Expo + Nuxt + Supabase as the only engineer, talking to the client on shifts, geofences, PKWT templates, and a site-client mobile role.  
- Unified GPS/selfie clock-in and ZKTeco fingerprint punches (Fly.io iClock ADMS) onto one attendance schema with RLS + CASL modules.  
- Closed the loop with Puppeteer contract PDFs (QR public verify), Excel ops exports, photo-proven cleaning tasks, and QR inventory — ~340 Postgres migrations, RPC-first writes.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **SDS** | Product / store name (`app.sds.co.id`) |
| **Sekaiichi** | Internal / legal (PT Sekaiichi Dwiputra Service) |
| **Gedung** | Building / site — primary scope |
| **TAD** | Outsourced office/professional staff (vs cleaner) |
| **PKWT / PKWTT / PHL** | Indonesian contract types |
| **ADMS / iClock** | ZKTeco device push protocol |
| **RPC-first** | Clients call `transaction_*` / `query_*`, not raw table CRUD |
| **Site client** | Occupant role (`id_role = 7`) — monitor, not punch |

---

*Generated from `lamaccatech/apps/sekaiichi`, `packages/@sekaiichi`, git history (Muhammad Dzul Arsyil), and the SDS section of github.com/aru-shiru/aru-shiru as of 16 August 2026. User-volume numbers other than the public 2,000+ claim are omitted on purpose.*
