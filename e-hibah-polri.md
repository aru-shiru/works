# e-Hibah Polri — Resume Project Brief

**Role:** Tech Lead (v1 with Bimo + Nana); **sole engineer** on the Flux / Livewire rewrite  
**Client / product:** e-Hibah — grant lifecycle for the **Indonesian National Police (Polri)**  
**Period:** December 2024 – present. v1 (Inertia/React) Dec 2024 – Nov 2025; **rewritten Livewire + Flux UI Pro** mid-February 2026.  
**Team (do not mix)**  
- **v1 (`e-hibah-polri-archived`):** You as tech lead + **Bimo Eka Saputra** + **Nana Sukarna** (same two as ADVLive). 723 commits.  
- **Rewrite (`e-hibah-polri`):** You alone — 76 commits, all Muhammad Dzul Arsyil — **with Claude Code**, after buying **Flux UI Pro**.  
**Repos:** v1 `~/code/e-hibah-polri-archived` (`lamaccatech/e-hibah-polri-archived`); rewrite `~/code/e-hibah-polri` (`lamaccatech/e-hibah-polri`)

This document is a **source brief**, not a finished resume entry.

Public GitHub listing: **e-Hibah Polri — Grant Management System, Indonesian National Police** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). That card already describes the **Livewire rewrite** (from Laravel 11 + Inertia + React). **No MAU / install numbers** on the card — do not invent them.

---

## 1. Project overview

### 1.1 One-sentence pitch

e-Hibah is Polri’s web app for taking a grant from a **satker proposal** through **Polda then Mabes review** (four mandatory aspects), official numbering, signed agreement, and a **Sehati** form for Ministry of Finance data — planned grants (`terencana`) run Planning → Agreement; **direct** grants (`langsung`) skip Planning.

### 1.2 Why it exists

Polri receives grants (money, goods, or services) from external donors. The paper path is a **three-tier hierarchy**: Satuan Kerja (work unit) drafts; Satuan Induk / Polda assesses; Mabes assesses, verifies, and issues an official number. Revision requests bounce back to Satker. The product encodes that cycle, auto-generated PDFs, and an audit trail instead of email-and-Word.

### 1.3 Two eras (do not collapse)

**v1 — Laravel 11 + Inertia + React (you remembered React; it was not Vue).**  
First commit **2 Dec 2024** (`chore: Install laravel` / shadcn). Last archived landing **29 Nov 2025**. Deployed on **Fly.io** Singapore (`e-hibah-polri.fly.dev`, fly.toml dated 20 Apr 2025): PHP 8.2, **SQLite on a volume**, files on **Tigris S3**. 247 PHP files + 170 TSX pages. You led Bimo and Nana; they last committed mid-July 2025; you kept merging and shipping (including the Fly deploy).

**Rewrite — Laravel 12 + Livewire 4 + Flux UI Pro.**  
Trigger: buy **Flux UI** and rebuild for a cleaner ops UI without an Inertia/React SPA. First commit **15 Feb 2026** (`Inital commit` → `chore: install flux ui pro`). Last local landing **19 Feb 2026**. Same domain (22 Indonesian status enums, 4-aspect pengkajian, Sehati form). Docs in that tree still say **development-phase** (edit existing migrations). Do **not** claim the rewrite is the Fly.io production box unless you confirm cutover later.

### 1.4 Who uses it

| Actor | What they do |
|---|---|
| **Satker** | Create planned or direct grants; fill donor, proposal chapters, 4-aspect self-assessment, agreement/harmonization; revise when asked; upload signed naskah; fill Sehati |
| **Polda / satuan induk** | Assess 4 aspects → verify (forward to Mabes) / request revision / reject |
| **Mabes** | Same 4-aspect review; final verify → auto-number; user/chief/tag admin; activity log |
| **No self-registration** | Mabes provisions accounts |

Grant **form**: uang / barang / jasa. Donor is `pemberi_hibah`.

### 1.5 Approval workflow (both stages)

```
Satker submit → Polda assess (4 aspects) → verify to Mabes
                  ↳ request revision / reject
Mabes assess (4 aspects) → verify → official number
                  ↳ request revision / reject
```

Four aspects: **teknis, ekonomis, politis, strategis**. Each: terpenuhi / revisi / ditolak. All fulfilled → verify; any ditolak → reject; else revision.

After agreement numbering: upload signed naskah, then **Sehati** fields (recipient, funding source/type, withdrawal method, dates). That is a **form stored in Postgres**, not a live Kemenkeu API.

### 1.6 Org tree in seed data

Rewrite seeders load **1,504** unit accounts: 1 Mabes, **57** satuan induk (Polda + central bodies), **1,446** satuan kerja. That is the **Polri hierarchy in the seeder**, not proven concurrent users or MAU. Cite as provisioned org units, not traffic.

---

## 2. Stack

### 2.1 v1 (archived) — not Vue

| Surface | Stack |
|---|---|
| **App** | Laravel **11**, PHP 8.2, **Inertia Laravel** + **React 18** + TypeScript, Vite 5, Tailwind 3 |
| **UI kit** | **shadcn / Radix**, TanStack Table, TipTap, Recharts, react-dropzone, sonner |
| **Auth** | Laravel **Breeze** (no TOTP 2FA in this tree) |
| **PDF** | barryvdh/laravel-dompdf |
| **Files** | Flysystem S3 (production Tigris) |
| **Tests** | Pest 3 — **126** `it`/`test` cases |
| **Deploy** | Fly.io `sin`, 1 shared CPU / 1 GB, auto-stop machines, SQLite volume |

pnpm. Tightenco Ziggy for routes. Indonesian copy mostly in the React pages (not a `__()` lang tree).

### 2.2 Rewrite — Flux UI was the point

| Surface | Stack |
|---|---|
| **App** | Laravel **12**, Livewire **4**, **Flux Pro** (`livewire/flux-pro` via composer.fluxui.dev) + Flux free, Tailwind **4**, Vite 7 |
| **Auth** | Laravel **Fortify** — password + **TOTP 2FA** + recovery codes (starter-kit settings). **No registration** (Mabes provisions users) |
| **DB** | Sail **Postgres 18**; Pest on **SQLite**. `.env.example` still sqlite — production Postgres is the **architecture doc** + GitHub card, not the archived Fly sqlite box |
| **Files** | AWS S3 (prod intent); MinIO via Sail in docs |
| **PDF** | spatie/laravel-pdf + DomPDF (proposal / kajian / readiness) |
| **i18n** | `__()` — `lang/id/page.php` etc. (commit: replace hardcoded Indonesian strings) |
| **Access** | Middleware `EnsureSatker` / `EnsurePolda` / `EnsureMabes` on `unit.level_unit` — **not** a folder of Laravel Policy classes |
| **Audit** | `ActivityLog` + `ChangeHistory` snapshots (`recordCreation` / `recordChange` / `recordDeletion`) |
| **Structure** | Livewire → ViewModel → Repository → Model (docs). **23** models, **44** Livewire classes, **13** repositories |
| **Quality** | Pest **4**, **~506** tests, **14** feature specs + product/journey specs. Git commit claims **100% spec-to-test mapping**, not line coverage |
| **AI inner loop** | Specs + `CLAUDE.md` / `AGENTS.md` / coding guideline — Claude Code as pair-programmer |

GitHub tags Alpine.js (Flux/Livewire) and “eliminating the JS build pipeline for most features” — Vite still builds Tailwind; **pages are Blade**, not a React SPA.

### 2.3 What the GitHub card gets right vs stretch

| Card | Safer |
|---|---|
| Rewrite Inertia/React → Livewire | True |
| TOTP 2FA | **Rewrite / Fortify**, not v1 Breeze |
| PostgreSQL | **Rewrite target** (Sail + docs). **v1 Fly prod was SQLite** |
| “Granular authorization policies” | **Unit-level middleware**, not Eloquent Policy classes |
| Audit / change history / soft deletes | True on the rewrite |
| Alpine.js | Present via Livewire/Flux, not a separate SPA |

---

## 3. Contribution

### 3.1 v1 — you led Bimo and Nana

Three-person Laravel/Inertia app. You started the repo (Laravel, shadcn, domain entities), reviewed/merged (~PRs through July), deployed Fly, and kept the tree after the juniors left.

**Git (archived, added lines excluding lock/vendor):** you **~40%**, Bimo **~38%**, Nana **~22%**. **Commits:** Bimo 321, you 277, Nana 123. Do **not** lead with “40% of the code” — Bimo out-committed you; you were **lead** (architecture, merges, Fly, remaining work Aug–Nov). Interview: **tech lead, still hands-on**, same pairing as ADVLive.

From commit subjects (not a pie chart of “who owns UI”):

- **Bimo:** Inertia grant/agreement flows, Polda/Mabes dashboard stats, Indonesian validation copy, review redaksi, kepala unit.  
- **Nana:** Donor listing/stats, Dropzone, extra kajian aspects, Inertia assert tests, filters, reset-password UI.  
- **You:** Bootstrap, merges, formatting, Fly.io, late hardening.

Same names as ADVLive — this is an earlier overlap (e-Hibah v1 started Dec 2024), not a third person.

### 3.2 Rewrite — sole + Claude Code (honest)

You owned the decision (Flux Pro for UI/UX), the spec tree (`PRODUCT_SPEC.md`, journeys, 14 feature specs), architecture notes, and every git commit. Claude Code drafted against those specs over a **~4-day** git burst (15–19 Feb 2026). That is **sole developer, AI-assisted**, same wording as SimplePol — not “Claude wrote e-Hibah.”

You do **not** claim Bimo/Nana worked on the Flux tree.

### 3.3 Outcomes you can defend

- Encoded Polri’s three-tier hibah process (planned vs direct, 4-aspect pengkajian, auto-number, Sehati **form**).  
- Shipped a real v1 with two juniors on **Inertia/React**, Fly.io.  
- Rewrote the same product as **Livewire + Flux Pro** so ops UI is Blade, with Fortify 2FA, activity/change history, `__()` Indonesian, and a large Pest suite mapped to specs.  
- Seeded the **national org tree** (~1,504 units) rather than a toy tenant list.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, e-Hibah Polri (Indonesian National Police)** — Dec 2024 – present  
Grant lifecycle (proposal → Polda/Mabes review → agreement → Sehati). Led Bimo and Nana on Laravel 11 / Inertia / React; **sole rewrite** to Laravel 12 / Livewire 4 / Flux UI Pro (AI-assisted).

### 4.2 Short blurb

Led a 3-person team building Polri’s grant platform (Satker / Polda / Mabes, 4-aspect review). Then rewrote it alone with Flux UI Pro + Livewire so HQ UI is Blade instead of an Inertia React SPA — Fortify 2FA, audit trail, spec-driven Pest suite.

### 4.3 Medium blurb

e-Hibah manages Polri grants (uang/barang/jasa) across ~1,500 seeded work units: Satker drafts planned or direct grants; Polda then Mabes assess four aspects (teknis/ekonomis/politis/strategis); official numbers, signed agreements, and a Sehati form for Kemenkeu data. Tech lead on Laravel 11 + Inertia + React (Bimo, Nana). After purchasing Flux UI Pro, sole rewrite to Laravel 12 + Livewire 4 with Claude Code against written specs — not a second team.

### 4.4 Bullets

- Tech lead for Polri e-Hibah: three-tier approval (Satker → Polda → Mabes), planned vs direct grants, 4-aspect pengkajian, PDF naskah, donor registry.  
- Led **Bimo Eka Saputra** and **Nana Sukarna** on the Laravel 11 / Inertia / **React** (TypeScript, shadcn) v1; deployed Fly.io (Singapore).  
- Rewrote the product after buying **Flux UI Pro**: Laravel 12, Livewire 4, Blade + Tailwind 4 — dropped the React SPA; GitHub card is this rewrite.  
- Fortify **TOTP 2FA**, Mabes-provisioned accounts (no public register), unit-level middleware for Satker/Polda/Mabes.  
- Activity log + change-history snapshots; auto-generated proposal/kajian/readiness PDFs; Sehati **data form** (not a Kemenkeu API).  
- Spec-driven rewrite: product spec + 14 feature specs, ~500 Pest tests; sole author, **Claude Code as pair-programmer**.  
- Org seeder: **1,504** Polri unit accounts (1 Mabes, 57 induk, 1,446 satker) — hierarchy size, not MAU.

### 4.5 Skills

Laravel 11/12, Livewire 4, Flux UI Pro, Inertia.js, React, TypeScript, Tailwind, Laravel Fortify (2FA), PostgreSQL, SQLite, Pest, Fly.io, AWS S3 / Tigris, DomPDF, spec-driven development, RBAC (org-unit levels)

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “Vue + Laravel” | v1 is **Inertia + React**. No Vue in either tree. |
| “I was sole developer the whole time” | **Lead + two** on v1; **sole** on the Flux rewrite. |
| “Production Postgres on Fly” | **v1 Fly was SQLite + Tigris**. Postgres is the **rewrite/Sail** target. |
| “Kemenkeu / Sehati API” | **Form fields** stored as `informasi_hibah_untuk_sehati`. |
| “Laravel Policies” | **Middleware on `level_unit`**. |
| “100% test coverage” | **~506 tests mapped to 14 specs**, not a coverage %. |
| “Claude wrote it” | **Sole developer, AI-assisted** (same as SimplePol). |
| 1,504 users / MAU | **Seeded org units**, not traffic. GitHub has **no** usage numbers — do not invent. |
| Rewrite is live on Fly | Last rewrite git is **Feb 2026**; CLAUDE.md still “development phase.” v1 is what fly.toml describes. |

---

## 5. Suggested 3-bullet block

**e-Hibah Polri** — Tech Lead *(Dec 2024 – present)*  
Grant lifecycle for the Indonesian National Police (Satker / Polda / Mabes).

- Led two engineers on Laravel 11 + Inertia + React: proposal/agreement workflows, 4-aspect review, Fly.io deploy.  
- Rewrote the app (Flux UI Pro + Livewire 4) as sole engineer for a Blade ops UI, Fortify 2FA, and a spec-mapped Pest suite.  
- Models planned vs direct grants, official numbering, PDF naskah, and a Sehati form for Ministry of Finance data across the Polri org tree.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Hibah** | Grant (from an external donor) |
| **Satker / satuan kerja** | Work unit that drafts |
| **Satuan induk / Polda** | First-level review |
| **Mabes** | Headquarters — final review + admin |
| **Pengkajian** | 4-aspect assessment |
| **Terencana / langsung** | Planned (has Planning stage) / direct (Agreement only) |
| **Sehati** | Kemenkeu-oriented data capture in-app |
| **Flux UI** | Livewire component kit; this rewrite uses **Pro** |

---

*Generated from `e-hibah-polri-archived` and `e-hibah-polri` plus the e-Hibah Polri section of github.com/aru-shiru/aru-shiru as of 16 August 2026. v1 confirmed Inertia + React, not Vue.*
