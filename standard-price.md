# Standard Price / SSH (Kaltara) — Resume Project Brief

**Role:** Tech lead — **not** review-only. You opened the repo, first-added most `app/`+`resources/`, and still have the most non-merge commits. You also merged PRs.  
**Client / product:** **Sistem Informasi Standar Satuan Harga** for **BKAD Provinsi Kalimantan Utara**. Lamacca `/work` badge still **2023** (repair later).  
**Git start year (source of truth for lamacca.com):** **2022** — first commit **24 Sep 2022**.  
**Period:** **24 Sep 2022 – 11 Jan 2023**. Concurrent with **asset recon** (`kaltara-rekonsiliasi-aset`): recon starts **12 Oct 2022**, overlap through SSH’s last day; recon then continues to **26 May 2024**.  
**Team:** same four names as SIMAS — you, **Bimo Eka Saputra** (`bimoekas`), **Nadella** (`nadella987`), **Nana Sukarna** (`NanaSukarna16`). Nana is a **cameo here** (4 commits). On recon she is a real builder (34). `lamaccatech` = merge-only.  
**Repo:** `~/code/kaltara-ssh` (`lamaccatech/kaltara-ssh`) · sibling `~/code/kaltara-rekonsiliasi-aset` (separate brief)

This document is a **source brief**, not a finished resume entry.

Same Kaltara-team pattern as SIMAS: you remember supervising; git shows **you heaviest** (96 non-merge vs Nadella 68, Bimo 55, Nana 4).

**Not** on GitHub Selected Projects. **No MAU / item counts.** Do not invent how many SSH rows BKAD stored.

---

## 1. Project overview

### 1.1 One-sentence pitch

Login-gated Laravel/Inertia catalog so OPD propose unit-price items and BKAD verify or reject them — SSH, HSPK, ASB, SBU — with Excel/SIPD export.

### 1.2 Why it exists

Pemda budgeting (RKA / APBD) needs one **standar harga** list. This app is that list plus a propose → verify workflow. **RKA and APBD documents are not generated here** — Lamacca’s card names those as the *using* processes.

### 1.3 Surfaces

| Actor | Role in code | What they do |
|---|---|---|
| **BKAD** | `admin_bkad` | Users, verify/reject barang, SIPD Excel export |
| **OPD** | `admin_opd` | Create/edit proposed items |
| **Anyone logged in** | `auth` + `verified` | Browse/search catalog (Welcome still behind login) |

Kategori from seeder: **SSH** standar satuan harga, **HSPK** harga satuan pokok kegiatan, **ASB** analisis standar belanja, **SBU** standar biaya umum. Also: kelompok, satuan, rekening belanja, dashboard counts.

No `fly.toml`. `.env.example` localhost. **No prod hostname in git.**

### 1.4 Sibling (same BKAD, same months)

| | SSH (`kaltara-ssh`) | Recon (`kaltara-rekonsiliasi-aset`) |
|---|---|---|
| First | 24 Sep 2022 | 12 Oct 2022 |
| Last | 11 Jan 2023 | 26 May 2024 |
| Git start year | **2022** | **2022** |

Same humans and merge-bot. Recon is kertas kerja + berita acara PDF vs laporan BMD/neraca — **do not mix the two in one resume bullet.** Full recon brief when you point at that folder.

---

## 2. Stack

Same generation as SIMAS (Laravel 9 + Breeze + Inertia/Vue 3 + Vite + Tailwind). **No Livewire.**

| Layer | Lock / constraint |
|---|---|
| PHP | ^8.0.2 (Sail 8.1) |
| Laravel | **9.38** |
| Inertia / Vue | inertia-laravel 0.6, Vue 3.2, inertia-vue3 0.6 |
| Excel | Maatwebsite **3.1** (SIPD export) |
| Auth | Breeze; username login (Nadella). Roles are **string constants**, not Spatie |

Stock Laravel README. Product title lives in `Welcome.vue`.

---

## 3. Contribution

Non-merge on `main`: **you 96**, Nadella 68, Bimo 55, Nana 4. First-adds `app/`+`resources/`: you **92**, Bimo 10, Nadella 6, Nana 0.

### 3.1 You

Initial commit same day the repo exists. Layout, kategori/barang/kelompok, browse/search, SIPD export, login gate, `Role` constants, dashboard polish. Merged PRs #1–#16 (GitHub-noreply) plus `fix: review PR list barang`. Lead **and** reviewer.

### 3.2 Nadella

Satuan, rekening belanja, username login, sidebar-by-role, many seeders.

### 3.3 Bimo

Manajemen user, `AdminBkad` / `AdminOpd`, verifikasi UI/controller, BKAD vs OPD gates.

### 3.4 Nana

Dashboard counts; verify/reject displays user names. **Do not** list her as a SSH feature owner; she is properly owned on recon.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, SSH Kaltara (BKAD unit-price catalog)** — Sep 2022 – Jan 2023  
Laravel 9 / Inertia / Vue 3. Same window as BKAD asset reconciliation.

### 4.2 Short blurb

Led Bimo and Nadella (Nana light) on BKAD Kaltara’s standar satuan harga: OPD propose, BKAD verify, Excel/SIPD export. Built in parallel with rekonsiliasi aset.

### 4.3 Medium blurb

From Sep 2022 to Jan 2023 we shipped BKAD Kalimantan Utara’s SSH catalog (SSH, HSPK, ASB, SBU) in Laravel 9 / Inertia / Vue, overlapping the rekonsiliasi-aset app that started three weeks later. I opened the repo and first-authored most of the app layer; Nadella owned satuan/rekening/login; Bimo owned users and verify. Nana barely appears on SSH (four commits) — her volume is on recon. Lamacca still badges this **2023**; git starts **2022**. Not an RKA/APBD producer. No row counts in-repo.

### 4.4 Bullets

- Tech lead, same Kaltara four (Nana cameo) as SIMAS.  
- SSH/HSPK/ASB/SBU catalog: OPD propose, BKAD verify, SIPD Excel.  
- Laravel 9, Inertia, Vue 3; concurrent with kaltara-rekonsiliasi-aset.

### 4.5 Skills

Laravel 9, PHP 8, Inertia.js, Vue 3, Tailwind, Breeze, role middleware, Maatwebsite Excel, SIPD export

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I only reviewed” | **96** of your non-merge commits + initial scaffold. |
| “Four equal builders” | Nana **4** commits here. Equal-ish: you / Nadella / Bimo. |
| “We built RKA/APBD” | Catalog **used in** those processes. App does not output RKA. |
| Row / OPD counts | **Unknown.** |
| “2023 project” | Live badge. **Git start 2022.** |
| Same as SIMAS | SIMAS is **Setda Biro Umum**, Nov 2022. This is **BKAD**, Sep 2022. Same team, different dinas. |
| Demo seeder passwords | **Do not paste.** |

---

## 5. Suggested 3-bullet block

**Standar Satuan Harga (BKAD Kaltara)** — Tech lead *(2022 – 2023)*  
Unit-price catalog for pemda budgeting.

- Led Bimo and Nadella; I scaffolded Laravel/Inertia and most of the barang/kategori layer.  
- OPD propose / BKAD verify; SSH · HSPK · ASB · SBU; SIPD Excel.  
- Built in parallel with rekonsiliasi aset (same BKAD, separate repo).

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **SSH / HSPK / ASB / SBU** | Four kategori in the seeder (unit price, kegiatan rate, belanja analysis, general cost) |
| **SIPD** | Kemendagri regional planning/finance upload — Excel export in this app |
| **OPD** | Organisasi perangkat daerah (`admin_opd`) |
| **BKAD** | Badan keuangan dan aset daerah (`admin_bkad`) |

---

*Generated from `~/code/kaltara-ssh` plus dates/authors on `~/code/kaltara-rekonsiliasi-aset` as of 17 August 2026. Git start year 2022 is the source of truth for the later lamacca.com repair. Full recon brief not written here.*
