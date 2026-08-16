# SIMAS (Kaltara manajemen aset) — Resume Project Brief

**Role:** Tech lead — **not** “mostly review.” You scaffolded Laravel, owned domain core (KIB models, mutasi, policies, QR), and still have the most non-merge commits. Bimo, Nana, and Nadella each shipped real features.  
**Client / product:** **SIMAS** — Sistem Informasi Manajemen Aset for **Biro Umum & Perlengkapan, Setda Provinsi Kalimantan Utara**. Lamacca `/work` badge still **2024** (repair later).  
**Git start year (source of truth for lamacca.com):** **2022** — first commit **14 Nov 2022**.  
**Period:** **14 Nov 2022 – 7 Jan 2023** (build). Your last commit **7 Nov 2024** (`chore: laravel sail config`). Peak **Dec 2022**.  
**Team (git confirms all three you named):** **Bimo Eka Saputra** (`bimoekas`), **Nana Sukarna** (`NanaSukarna16`), **Nadella** (`nadella987`). Org account `lamaccatech` = **merge PRs only** (~111).  
**Repo:** `~/code/kaltara-manajemen-aset` (`lamaccatech/kaltara-manajemen-aset`)

This document is a **source brief**, not a finished resume entry.

You remember **supervising and reviewing, helping a little.** The job *shape* is tech lead of three. **Git does not support “I barely coded.”** Same pattern as ISBE: you opened the repo (`chore: install laravel`), then they piled in from day two while you kept landing `feat`/`fix`/`refactor` on models, mutasi, QR, and roles.

**Not** on GitHub Selected Projects. **No MAU / asset-headcount** in this tree — do not invent. A SQL dump under `database/dumps/` is **not** a usage metric (and it contains **user password hashes** — do not paste).

---

## 1. Project overview

### 1.1 One-sentence pitch

A Laravel + Inertia/Vue KIB register for Pemprov Kaltara: land (A), equipment (B), other fixed (E), other assets (L), with QR labels, mutasi, perencanaan/realisasi, penyusutan, and role-gated admin vs gubernur views.

### 1.2 Lamacca card vs this clone

| Card | In `kaltara-manajemen-aset` |
|---|---|
| QR tagging & scanning | **QR PDF / on-page QR** pointing at the **public asset URL**. Phone **camera + browser**, not a custom scanner APK |
| Web + **mobile app** | **Web only** in this clone. No Flutter/`android/` tree. `routes/api.php` is stock Sanctum `GET /user` |
| Asset mutation & handover | **Mutasi aset** (transfer + verify). No module named serah-terima |
| Biro Umum Setda Kaltara | Seeder/roles: Superadmin, Gubernur Kaltara, Admin/TU per biro including **Biro Umum** |

If a native scanner shipped, it is **another repo**. Do not claim Expo/Flutter from this one.

### 1.3 Surfaces

| Actor | Surface |
|---|---|
| **Public / scan** | Vue public layout, search, asset page + QR |
| **Admin biro** | KIB CRUD (policy: `admin_biro`) |
| **Gubernur** | Dashboard charts |
| **PPB / superadmin** | Users, kode barang/lokasi, masa manfaat, tags |

Roles (`Role.php`): `superadmin`, `gubernur`, `admin_biro`, `pejabat_penatausahaan_barang`. Login is **username** (Breeze), not email.

No `fly.toml`. Closest prod hint: commit `feat: persiapan deploy ke simas` (Jul 2023) + dump named for hosting. **No hostname in git.**

---

## 2. Stack

| Layer | Pinned |
|---|---|
| Backend | PHP **8.0.2** (Sail **8.1**), **Laravel 9.39**, Breeze 1.14, Inertia Laravel 0.6.4, Sanctum 2.15 |
| Front | **Vue 3.2**, Vite 3.2, Tailwind 3.2, Inertia Vue 0.6, Chart.js 4.1 |
| Files | `simplesoftwareio/simple-qrcode` 4.2, Maatwebsite Excel 3.1, DomPDF 2.0 |
| Local | Laravel Sail, MariaDB 10, Redis |

Stock Laravel README (+ Sail note). Leftover Tailwind “Your Company.”

---

## 3. Contribution

Non-merge commits on `main`: **you 186**, **Bimo 140** (aliases `bimoekas` + `Bimo Eka Saputra`), **Nana 110**, **Nadella 102**. `lamaccatech` 111 merges. First-added `app/`+`resources/` files: you **133**, Bimo 66, Nadella 43, Nana 42.

### 3.1 You

Opened the app **14 Nov 2022**. KIB **models**, Role/User, **mutasi** (model/controller/Vue), **policies** + Inertia permission bag, QR service + `AssetQR.vue`, dashboard stats, penyusutan value object, public layout/search, users. 104 `feat` subjects. PRs named `review-*` are often **your own** branches, not “I only commented.”

### 3.2 Bimo

KIB E forms, kode-barang seed/factory, **perencanaan** (`PerencanaanAsetController`), penyusutan Excel, dokumen pendukung.

### 3.3 Nana

KIB A + L UI/controllers, **masa manfaat**, **realisasi aset**.

### 3.4 Nadella

KIB B UI, kode lokasi, Excel KIB, **print QR per KIB**, STNK fields, gubernur charts, per-KIB search.

Interview line: **tech lead of four (you + three); I owned the domain core and still wrote a lot.** Not “I reviewed their PRs and rarely opened the IDE.”

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, SIMAS — Pemprov Kaltara asset register** — Nov 2022 – Jan 2023  
Laravel 9 / Inertia / Vue 3 KIB system (QR labels, mutasi, roles) with Bimo, Nana, Nadella.

### 4.2 Short blurb

Led Bimo, Nana, and Nadella on SIMAS for Setda Kaltara: I scaffolded Laravel and the KIB/mutasi/QR/policy core; they shipped per-KIB UI, perencanaan/realisasi, and QR print.

### 4.3 Medium blurb

SIMAS is Pemprov Kalimantan Utara’s barang-milik-daerah register (KIB A/B/E/L) for Biro Umum: photos, supporting docs, QR labels, mutasi, perencanaan and realisasi, penyusutan. Git is Nov 2022–Jan 2023 (Sail bump Nov 2024). I had the most non-merge commits and first-authored the models, mutasi, policies, and QR service. Bimo (perencanaan, KIB E), Nana (KIB A/L, realisasi), Nadella (KIB B, QR print, gubernur charts). Lamacca lists 2024 and a mobile app; this clone is web-only and starts **2022**. No asset counts in-repo.

### 4.4 Bullets

- Tech lead of four on Kaltara SIMAS (Laravel 9, Inertia, Vue 3).  
- Owned KIB domain model, mutasi, authorization policies, QR label service.  
- Team delivered per-jenis KIB screens, perencanaan/realisasi, Excel/QR print.  
- Phone “scanning” in this repo is camera → public web URL, not a separate mobile app.

### 4.5 Skills

Laravel 9, PHP 8, Inertia.js, Vue 3, Tailwind, Laravel Breeze, Laravel policies, QR (simple-qrcode), Maatwebsite Excel, DomPDF, Chart.js, Laravel Sail

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I mainly reviewed / helped a little” | **Memory.** Git: **heaviest author** + domain core. Say **tech lead, hands-on**. |
| “I built SIMAS alone” | **Four people.** Bimo, Nana, Nadella are in git from day 2. |
| Native mobile / Expo scanner | **Not in this repo.** Responsive web + QR to public URL. |
| “Handover module” | **Mutasi.** Don’t invent serah-terima. |
| Asset N / MAU | **Unknown.** Dump ≠ production census. |
| “2024 project” | Live Lamacca badge. **Git start year 2022** — that is what to put on the site when you batch-repair. |
| SQL dump users table | **Do not paste hashes.** Treat as leaked if ever public. |
| Seed password `password` | Dummy. Do not reuse as a credential line. |

---

## 5. Suggested 3-bullet block

**SIMAS (Pemprov Kaltara)** — Tech lead *(2022 – 2023)*  
KIB asset register for Biro Umum Setda: QR labels, mutasi, gubernur dashboard.

- Led Bimo, Nana, and Nadella; I owned models, policies, mutasi, and QR.  
- Laravel 9 + Inertia/Vue; Excel import/export; role-gated KIB CRUD.  
- Card says mobile; this tree is the **web** app (QR opens in the phone browser).

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **SIMAS** | Sistem Informasi Manajemen Aset (this product) |
| **KIB** | Kartu Inventaris Barang (A tanah, B peralatan/mesin, E/L others) |
| **Mutasi** | Asset transfer + verification |
| **PPB** | Pejabat penatausahaan barang |

---

*Generated from `~/code/kaltara-manajemen-aset` and lamacca.com/work as of 17 August 2026. Team names from git shortlog. “Mostly review” rejected by non-merge commit counts and first-add of domain files. No scanner APK in this clone.*
