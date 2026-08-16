# Asset Reconciliation (Kaltara) — Resume Project Brief

**Role:** Tech lead — **not** review-only. Heaviest non-merge author; after late Oct 2022 the log is **you only** (kertas kerja engine, berita acara PDF, lock, rekap, impersonate).  
**Client / product:** **Sistem Informasi Rekonsiliasi Aset** for **BKAD Provinsi Kalimantan Utara**. Lamacca `/work` badge still **2023** (repair later).  
**Git start year (source of truth for lamacca.com):** **2022** — first commit **12 Oct 2022**.  
**Period:** **12 Oct 2022 – 26 May 2024**. Peak **Oct–Dec 2022**. Overlaps **SSH** (`kaltara-ssh`: 24 Sep 2022 – 11 Jan 2023). Bimo / Nadella / Nana last commit **26–29 Oct 2022**.  
**Team:** same four as SSH/SIMAS — you, **Bimo Eka Saputra** (`bimoekas`), **Nadella** (`nadella987`), **Nana Sukarna** (`NanaSukarna16` — **real builder here**, unlike SSH). `lamaccatech` = 5 merge-only PRs of **your** feature branches.  
**Repo:** `~/code/kaltara-rekonsiliasi-aset` (`lamaccatech/kaltara-rekonsiliasi-aset`)

This document is a **source brief**, not a finished resume entry.

You already pointed this folder when we did SSH. Same BKAD, same weeks, **different product** — typed KKR workbook vs unit-price catalog. **Do not** claim an import from SIMAS (`kaltara-manajemen-aset`): no HTTP, no shared DB in this clone.

**Not** on GitHub Selected Projects. **No MAU.** Seeder ~574 UPB `User::create` is **fixture size**, not analytics.

---

## 1. Project overview

### 1.1 One-sentence pitch

Operators fill a **kertas kerja rekonsiliasi (KKR)** — saldo awal, mutasi tambah/kurang, saldo akhir across KIB A–F; BKAD locks the period, recaps, impersonates, and prints three **berita acara** PDFs (Laporan BMD vs Neraca).

### 1.2 Lamacca card vs this clone

| Card | In this repo |
|---|---|
| Reconcile physical assets vs **SIMAS** | **No SIMAS integration.** Guzzle unused. `sync` is **cell-to-cell inside KKR** |
| Audit trail | Eloquent rows + lock flag + cached table-view. No dedicated audit-log table |
| **2023** | Git **2022–2024** (`tahun_kkr` config **2023**) |

### 1.3 Surfaces

Custom `roles` + `HasRole`. Seed: `superadmin`, `admin`, `operator`. Username login (Nadella). Hierarchy via `id_atasan`.

| Actor | Gate | What |
|---|---|---|
| **Anyone auth** | `auth`+`verified` | Dashboard, KKR view, Excel KKR export, leave impersonate |
| **Superadmin (BKAD)** | `role:superadmin` | Users (Data UPB), saldo awal, lock, rekap, 3× berita acara PDF, impersonate |
| **Operator** | `role:operator` + not locked | Create/store KKR nilai, rincian belanja, sync |
| **Admin (SKPD)** | seeded | Sees KKR through atasan tree; **no dedicated route group** |

No `fly.toml`. `.env.example` localhost `:8100`. **No prod hostname.**

---

## 2. Stack

Same generation as SSH/SIMAS. **No Livewire.**

| Layer | Pinned |
|---|---|
| PHP | ^8.0.2 (Sail **8.1**) |
| Laravel | **9.52** |
| Inertia / Vue | inertia-laravel 0.6, Vue 3.2, Vite 3.1, Tailwind 3.1 |
| PDF / Excel | DomPDF 2.0, Maatwebsite 3.1 (**export only**, no SIMAS import) |
| Auth | Breeze 1.19, Sanctum 2.15 |

Stock Laravel README. Title in KKR Vue: Pemerintah Provinsi Kalimantan Utara.

---

## 3. Contribution

Non-merge: **you 127**, Nadella 71, Bimo 54, Nana **34**. First-adds `app/`+`resources/`: you **123**, Nadella ~8 unique, Bimo 10, Nana 8. **302 commits in 2022**, 7 in 2023, 1 in 2024.

### 3.1 You

Opened Laravel. User/Role/`id_atasan`, KKR/rincian/satuan/rekening/barang models, `HasRole`. After the others stop: DB-backed KKR grid, `TableViewService`, mutasi badges, cell sync, Excel export, lock, year config, **three berita acara PDFs**, saldo awal, impersonate, rekap. `refactor: review modul *` = lead **and** reviewer. Last: `fix: fix styling halaman kertas kerja` (26 May 2024).

### 3.2 Nana

Rekening belanja CRUD + **KKR table shell** (`Kkr/Index`, first `KkrController`, mutation labels). Not BA/PDF/impersonate.

### 3.3 Nadella

Username login, **barang** CRUD (later **removed from nav**; files remain), rincian belanja input. Early KKR input; you rewrote load/store onto cache + events.

### 3.4 Bimo

Satuan barang UI, **manajemen user** (create/edit, atasan, roles). Superadmin still owns user CRUD.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, Rekonsiliasi Aset (BKAD Kaltara)** — Oct 2022 – May 2024  
KKR workbook + berita acara PDF. Same team/window as SSH; you finished it after they left.

### 4.2 Short blurb

Led Bimo, Nadella, and Nana on BKAD Kaltara’s asset-reconciliation kertas kerja; I owned the grid engine, lock/rekap, impersonate, and berita acara PDFs after Oct 2022.

### 4.3 Medium blurb

From 12 Oct 2022 (three weeks after SSH) we shipped BKAD Kalimantan Utara’s KKR: UPB/OPD enter KIB A–F figures; BKAD sets saldo awal, locks, impersonates, and prints BMD-vs-neraca berita acara. Laravel 9 / Inertia / Vue. The others last commit that October; I carried the product through May 2024. Lamacca still badges **2023** and implies a SIMAS feed — this clone is **standalone**, not wired to `kaltara-manajemen-aset`. Git start **2022**. No MAU. Do not mix with SSH.

### 4.4 Bullets

- Tech lead of the Kaltara four; after Oct 2022, solo on the KKR engine.  
- Kertas kerja, lock, rekap, impersonate, three berita acara PDFs.  
- Not a SIMAS import. Sister of SSH, not of Setda SIMAS.

### 4.5 Skills

Laravel 9, PHP 8, Inertia.js, Vue 3, Tailwind, Breeze, DomPDF, Maatwebsite Excel, role middleware, impersonation

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I only reviewed” | **127** non-merge; whole product after Oct 2022 is you. |
| “Integrated with SIMAS” | **Not in this clone.** Card overclaims. |
| “Physical barcode rec” | Operators **type** KKR cells. |
| 574 users | **Seeder fixture.** |
| “2023 project” | Badge. **Git start 2022**; last **2024**. |
| Superadmin seeder password | **Do not paste.** Other UPB passwords are `Str::snake(unit name)`. |
| Policy “hanya pemilik” | Looks inverted — don’t claim tight row ACL. |

---

## 5. Suggested 3-bullet block

**Rekonsiliasi Aset (BKAD Kaltara)** — Tech lead *(2022 – 2024)*  
KKR workbook vs laporan BMD / neraca.

- Same team as SSH; I owned the grid, PDFs, lock, and impersonate after they left.  
- Laravel 9 / Inertia / Vue; not wired to SIMAS.  
- Git starts 2022 (site still 2023).

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **KKR** | Kertas kerja rekonsiliasi |
| **UPB** | Unit pengelola barang (operator login) |
| **BMD** | Barang milik daerah (PDF/form label, not a SIMAS pull) |
| **Berita acara** | Three DomPDF formats in `pdf/first\|second\|third` |

---

*Generated from `~/code/kaltara-rekonsiliasi-aset` as of 17 August 2026. Git start year 2022 is the source of truth for the later lamacca.com repair. SSH overlap noted; SIMAS is a different dinas.*
