# ISBE Investment Monitoring Dashboard (BPKH) — Resume Project Brief

**Role:** Tech lead for **one junior** — review + client/comms **and** hands-on senior code (filters, Excel import, logging, roles). **Not** comms-only. **Not** sole builder.  
**Client / product:** Internal dashboard for **BPKH Dewan Pengawas, bidang ISBE**. UI: **“DASHBOARD DEWAS BIDANG ISBE”**. Lamacca `/work`: **ISBE Investment Monitoring Dashboard (2021)** · BPKH RI.  
**Period:** **24 Dec 2021 – 18 Jan 2022** (~26 days). Junior first (`inisialisasi`). You last (`fix: data table tidak menampilkan data sesuai filter`). Lamacca `/work` year is **2021** (first commit; was wrongly **2024**).  
**Team:** **Renaldi Apriyanto** (`Sylents-Sys` / `renaldiapriyanto419@gmail.com`) — junior who bootstrapped Laravel + Skote and most CRUD/UI. **You** — 54 unique commits on `main` vs his 31. No Luthfi, Bimo, Nana.  
**Repo:** `~/code/dashboard-bpkh` (`lamaccatech/dashboard-bpkh`)

This document is a **source brief**, not a finished resume entry.

You remember mostly **reviewing** and **talking to the client**. That is a fair *job description* for a three-week agency gig. **Git does not support “I barely coded.”** After he landed the Skote screens, you spent New Year week extracting repositories, view-models, `FilterPeriod`, Excel import, `ActionLogger`, and role/division query rules. Treat memory as rusty; treat `main` as the record.

**Not** on GitHub Selected Projects. **No AUM, gold tonnes, MAU, or user counts** in the repo. Charts read the DB; axis copy is “dalam Milyar.” Dummy seed users (`bp@test.com` / `12345`) are **not** production metrics.

---

## 1. Project overview

### 1.1 One-sentence pitch

A private Laravel dashboard so BPKH Dewas ISBE can watch RKAT (kegiatan + realisasi output/anggaran), investment **portofolio** by instrument, and a short **permohonan persetujuan ISBE** workflow (kajian files, status proses/revisi/ditolak/diterima).

### 1.2 Why it exists (Lamacca vs code)

Company card: oversee/evaluate investment in **securities and gold**. In code:

| Domain | What shipped |
|---|---|
| **RKAT** | List kegiatan; realisasi output; realisasi anggaran; Excel import/export |
| **Portofolio** | SDHI, SBSN (IDR/USD), Sukuk korporasi, RD syariah variants, KIK EBA, **Emas** |
| **Permohonan ISBE** | Upload kajian investasi/risiko; tahapan; status |
| **Ops** | Link referensi; users; riwayat aktivitas |

Division labels in your view-model: **ISB** (Investasi Surat Berharga) and **ISBELA** (Investasi Emas & Surat Berharga Lainnya). Roles: `badan_pelaksana`, `anggota_dewas`, `sekretariat_dewas`, `kip`.

This is a **staff tool**, not a public investment product. Heroku Procfile exists; **no production URL** in git.

---

## 2. Stack

| Layer | What |
|---|---|
| Backend | **Laravel 8.65**, PHP 8 in practice (`match` in `UserRole`; composer still allows 7.3) |
| Auth | Laravel UI 3.3, `auth` middleware. No Spatie in composer (Skote Spatie seeders commented). Simple `users.role` string (`UserRole.php` is yours) |
| UI | **Skote 3.3.1** (Themesbrand) via Mix, Bootstrap 5, jQuery, ApexCharts, Yajra DataTables 9 |
| Excel | PhpSpreadsheet (dev branch pin — fragile) |
| DB | MySQL (`.env.example`) |
| Deploy | Heroku (`heroku-php-apache2 public/`) |

README is stock Laravel + `# dashboard_bpkh`. Leftover Skote ecommerce sidebar, chart tooltip **“points”**, unused Roles/Permission controllers.

---

## 3. Contribution

`main` unique commits: **you 54**, **Sylents-Sys 31**. `--all` looks like 81 vs 32 because of your feature branches (`filter-data`, `app-log`, `refactor-view-models`, `staging`). His **line counts are huge** because of `public/` Skote assets — not a reason to say he “wrote more product.”

### 3.1 Junior (Renaldi / Sylents-Sys) — 24–31 Dec 2021

`inisialisasi`, `Mega Update`, portofolio, Heroku, `Dashboard selesai`, user management, numbered UI fixes. Touched `resources/` and `public/` most. He **stood up** the dashboard, CRUD screens, and template.

### 3.2 You — 31 Dec 2021 – 18 Jan 2022

Dense, conventional `feat:` / `fix:` / `refactor:` on `app/` (not PR comments):

- Period filters (portofolio, output, anggaran); `FilterPeriod` VO; filter Blade + `DivisionQueryEnhancer`; year/divisi on kegiatan  
- `KegiatanRepository`; strip logic out of controllers into repos/view-models  
- Excel **import** (kegiatan / output / anggaran; skip if over pagu)  
- `ActionLogger` + AppLog migration; riwayat aktivitas  
- Load statistik; ISBE counter by status  
- Login throttle, password rule, timezone  
- User management/creation + role-scoped kegiatan options  
- Last: DataTable **not matching filter** (18 Jan)

That is **senior pairing**: you reviewed by rewriting the layers he would have struggled to extract. Client comms can sit on top of this. What you **cannot** say: “I only coordinated” / “I did not write Laravel on this.”

### 3.3 Outcomes you can defend

- Three-week internal Dewas dashboard, one junior + you.  
- He shipped the Skote UI; you shipped filters, import, audit log, role/division constraints.  
- Small agency piece — do not put it next to e-Partai or SimplePol in scale.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech lead (1 junior), BPKH ISBE dashboard** — Dec 2021 – Jan 2022  
Internal Laravel dashboard for Dewan Pengawas bidang investasi surat berharga / emas.

### 4.2 Short blurb

Led one junior on BPKH’s Dewas ISBE dashboard: he built the Skote/Laravel screens; I reviewed, talked to the client, and implemented period filters, Excel import, activity logging, and role/division query rules.

### 4.3 Medium blurb

Over ~four weeks at New Year 2021/22, Renaldi Apriyanto bootstrapped a Skote Laravel admin for BPKH Dewan Pengawas (RKAT, portofolio including emas, permohonan persetujuan ISBE). I was the senior: client communication, review, and the backend slice git still shows — FilterPeriod, repositories/view-models, PhpSpreadsheet import, ActionLogger, login throttle, and division-aware queries. Company site year: **2021**. No AUM in source.

### 4.4 Bullets

- Tech lead for one junior on a BPKH Dewas ISBE internal dashboard (Laravel 8 / Skote).  
- Junior: template + CRUD/UI. Me: filters, Excel import, activity log, role/division constraints.  
- Client-facing for a ~four-week delivery; not a long-running product I still operate.

### 4.5 Skills

Laravel 8, PHP, Eloquent, Blade, Laravel Mix, Bootstrap, DataTables, PhpSpreadsheet, Heroku *(deploy target)*

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I mainly reviewed / I barely coded” | **Memory.** Git: **54** of your commits on `main`. Say **lead + hands-on senior slice**. |
| “I built the ISBE dashboard” | **He** scaffolded UI/CRUD. You the extraction/filters/import/log. |
| “Tech lead of a team” | **One** named junior: Renaldi Apriyanto (`Sylents-Sys`). |
| AUM / “gold portfolio of N trillion” | **Unknown.** Charts are DB-backed; leftover Apex copy says “points.” |
| “2024 project” | Was the old marketing year. **Site year is first commit: 2021.** |
| Spatie permissions / huge admin kit | Skote leftovers. Live roles are **four strings**. |
| Production URL / MAU | **None in repo.** Heroku Procfile only. |
| Seed `12345` passwords | Test users. Do not paste as credentials. |

---

## 5. Suggested 3-bullet block

**ISBE dashboard (BPKH Dewas)** — Tech lead, 1 junior *(Dec 2021 – Jan 2022)*  
Internal monitoring for RKAT, investment portofolio, and ISBE approval requests.

- Mentored a junior who delivered Laravel + Skote CRUD.  
- Implemented period/division filters, Excel import, and activity logging.  
- Client communication on a short agency delivery — not a platform I owned for years.

*Use this only if the resume needs a BPKH line. Easy to drop if you are slot-limited.*

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **BPKH** | Badan Pengelola Keuangan Haji |
| **Dewas** | Dewan Pengawas |
| **ISBE** | Bidang on this dashboard (UI name); Lamacca glosses as investment monitoring (securities + gold) |
| **ISB / ISBELA** | Surat Berharga vs Emas & surat berharga lainnya |
| **RKAT** | Annual work/budget plan (kegiatan + realisasi) |
| **SBSN / SDHI / Sukuk** | Instruments on the portofolio screens — do not lecture unless asked |

---

*Generated from `~/code/dashboard-bpkh` and lamacca.com/work as of 16 August 2026. Junior identified as Renaldi Apriyanto (`Sylents-Sys`). “Comms only” rejected by unique-commit and `app/` file history. No AUM/MAU in-repo.*
