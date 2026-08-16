# Works

Personal source briefs for projects I have shipped. Each project is one markdown file. These are resume-source documents, not finished resume entries.

Shorter public cards live on [github.com/aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru) (Selected Projects). Company-facing lists: [lamacca.com/products](https://lamacca.com/products) (7 platforms) and [lamacca.com/work](https://lamacca.com/work) (24 engagements). Names and years there do not always match these files or GitHub. Some `/work` cards are **skipped** here (you did not ship them). GitHub Selected Projects is a **slot-limited** ranking — re-rank after this list is complete.

## Contents

| Project | GitHub listing | Role | Period | Status |
|---|---|---|---|---|
| [SimplePol](simplepol.md) | SimplePol — Health Check Management System | Sole Full-Stack Engineer | Feb 2026 – present | Production |
| [ADVLive](advlive.md) | Advansia — Agricultural Sales Force Automation *(Firestore predecessor; card does not yet cover this rewrite)* | Tech Lead | Oct 2025 – present (ID since 2021) | In progress |
| [e-Partai / simPAN](e-partai.md) | e-Partai — Political Party Management System | Sole Full-Stack Engineer | 2021 – present (rewrite Aug 2025) | Production |
| [SDS / Sekaiichi](sekaiichi.md) | SDS — Building Services Management Platform | Sole Full-Stack Engineer | Jul 2024 – present | Production |
| [Pantau Relawan](pantau-relawan.md) | Pantau Relawan — Electoral Volunteer Management & Real-Time Vote Monitoring | Tech Lead | 2018–2019; rewrite 2023–present | Production |
| [e-Watch LSI](e-watch-lsi.md) | e-Watch LSI — Political Fieldwork Monitoring Platform | Sole Full-Stack Engineer (client-facing) | Dec 2022 – present | Production |
| [e-Hibah Polri](e-hibah-polri.md) | e-Hibah Polri — Grant Management System, Indonesian National Police | Tech Lead (v1); sole engineer (Flux rewrite) | Dec 2024 – present | v1 on Fly.io; Livewire rewrite Feb 2026 |
| [SIMPEL](simpel.md) | *(not on GitHub Selected Projects)* Sistem Informasi Manajemen Perlengkapan Jalan | Tech Lead | Jun 2025 – present | Production (3 dinas on Fly.io) |

**SimplePol** — Production health-check platform for Indonesian driving-license (SIM) applicants: identity verification, examination, payment, and official certificates across clinics, mobile units, and police jurisdictions.

**ADVLive** — Advansia’s internal field-operations super-app for Indonesia (live since 2021) and Malaysia. Postgres / Data Connect rewrite of the Indonesia Firestore product after Malaysia HQ requested the same platform (NestJS, Nuxt, Expo).

**e-Partai / simPAN** — Partai Amanat Nasional membership platform (KTA, kepengurusan, volunteers). Firebase simPAN from 2021 (React, Flutter); 2025 cloud-agnostic rewrite (Nest, Postgres, SuperTokens, S3/GCS) merging Pantau Relawan (PAN used that volunteer system for thousands of candidates).

**SDS / Sekaiichi** — Facility-operations platform for PT Sekaiichi Dwiputra Service: GPS + fingerprint attendance, cleaning proof, inventory, ticketing, and hiring-to-PKWT (Expo, Nuxt, Supabase, Fly.io iClock).

**Pantau Relawan** — Multi-candidate volunteer and real-count SaaS. Laravel + React Native on a VPS for 8 Pemilu 2019 candidates; 2023 Firebase rewrite with white-label Flutter, Vue admin, and React/MUI PWA (~20+ candidates on this product; PAN/e-Partai used the same system at party scale).

**e-Watch LSI** — Lingkaran Survei Indonesia (Denny JA) field dropping + persuasi monitor. Flutter + Firebase from 2022; HQ dashboard Vue 3 then rewritten with Nuxt UI Pro (mobile stayed Flutter).

**e-Hibah Polri** — Polri grant lifecycle (Satker → Polda → Mabes). You led Bimo and Nana on Laravel 11 + Inertia + React (not Vue); then rewrote it alone with Flux UI Pro + Livewire 4 (Claude Code, Feb 2026).

**SIMPEL** — Perlengkapan-jalan inventory for three Dinas Perhubungan (Prov. Sulsel, Kab. Paser, Kota Samarinda). Fork-per-dinas Nx template (Laravel/Inertia + Expo) on Fly.io; you led Bimo and Nana (git confirms).

---

## What to brief next

**Suggested next:** [Narrative Phone](https://lamacca.com/work) — already on GitHub Selected Projects; no source brief here yet.

Then leftover **products** (repeatable platforms), then the rest of **work**. Confirm you actually owned each card before writing a brief — these are Lamacca company pages, not a personal resume. Marketing years on `/work` can disagree with git (e.g. Advansia listed 2023; Indonesia product was live since 2021).

### Products — [lamacca.com/products](https://lamacca.com/products)

| # | Public name | Brief? | Maps to |
|---|---|---|---|
| 01 | e-Partai (deployed as SIMPAN / PAN) | Done | [e-partai.md](e-partai.md) |
| 02 | Pantau Relawan | Done | [pantau-relawan.md](pantau-relawan.md) |
| 03 | **Brainplus** (edtech / psychometric) | **Next after GitHub leftovers** | — |
| 04 | **Crowdfunding** (Wakafyuk.id, Bantusesama.id) | Needs brief | same card as work “Wakafyuk & Bantusesama” |
| 05 | Cleaning Service Management (Sekaiichi) | Done | [sekaiichi.md](sekaiichi.md) |
| 06 | **Media Online** (Journaltime.co) | Needs brief | — |
| 07 | SIMPEL | Done | [simpel.md](simpel.md) |

### Work — [lamacca.com/work](https://lamacca.com/work)

Order is the site order. **Next** = no `*.md` yet. **Skip** = still on the company site; do not write a brief.

| Public name (year on site) | Client | Brief? |
|---|---|---|
| SIMPAN — Party Management Super App (2020–2025) | PAN | Done — [e-partai.md](e-partai.md) |
| ADV Live — Agriculture Field Operations (2023) | Advansia ID + MY | Done — [advlive.md](advlive.md) |
| Cleaning Service Management System (2024) | PT Sekaiichi Dwiputra Service | Done — [sekaiichi.md](sekaiichi.md) |
| e-Hibah Mabes Polri (2024) | Mabes Polri | Done — [e-hibah-polri.md](e-hibah-polri.md) |
| e-SIAP Korlantas Polri (2024) | ISDC Korlantas Polri | **Skip** — Figma only; project discontinued |
| e-Watch — Campaign Field Monitoring (2024) | LSI Denny JA · Puspoll | Done — [e-watch-lsi.md](e-watch-lsi.md) *(Puspoll not in that brief)* |
| Pantau Relawan — Multi-Party Campaign Deployments (2024) | PAN, Golkar, PKB, PKS, Demokrat, LSI | Done — [pantau-relawan.md](pantau-relawan.md) |
| **Wakafyuk & Bantusesama — Donation Platforms (2023)** | Wakafyuk.id · Bantusesama.id | Needs brief *(same as product Crowdfunding)* |
| **NarrativePhone — Secure Care Calling (2023)** | xCareField (Japan) | **Next (GitHub Selected)** |
| **PBAS Website & Career Information System (2023)** | Patra Badak Arun Solusi | Needs brief |
| Patra Logistik Corporate Website (2023) | Pertamina Patra Logistik | **Skip** — WordPress; you did not contribute |
| **ISBE Investment Monitoring Dashboard (2024)** | BPKH RI | Needs brief |
| SIMPEL — Road Equipment Management (2023) | Site lists Dishub Samarinda · Paser only | Done — [simpel.md](simpel.md) *(also Sulsel; not on this card)* |
| **SIMAS — Asset Management Information System (2024)** | Biro Umum Setda Kaltara | Needs brief |
| **Standard Price Information System (2023)** | BKAD Kaltara | Needs brief |
| **Asset Reconciliation Information System (2023)** | BKAD Kaltara | Needs brief |
| **Congress Registration & Attendance System (2024)** | PAN | Needs brief |
| **e-KAHMI (2023)** | MN KAHMI | Needs brief |
| **e-Perdatin Jaya (2024)** | Perdatin Jaya | Needs brief |
| **Property & Facility Management System (2022)** | PT Prima Nusantara Services | Needs brief |
| **Room Reservation System (2023)** | Badan Penghubung Kaltara | Needs brief |
| **Banhub Activity Monitoring System (2023)** | Badan Penghubung Kaltara | Needs brief |
| **Presidential Election Situation Room Dashboard (2019)** | LSI Denny JA | Needs brief |
| **Geo-Location Attendance System (2022)** | Dinas Perpustakaan dan Kearsipan Sulsel | Needs brief |

### Also in this repo / GitHub, not on Lamacca pages

| Project | Notes |
|---|---|
| [SimplePol](simplepol.md) | On GitHub Selected Projects. Not on `/products` or `/work` (as of 16 Aug 2026). |

### Suggested briefing order

1. **Narrative Phone** — GitHub Selected, no brief  
2. **Brainplus** → **Crowdfunding / Wakafyuk & Bantusesama** → **Media Online / Journaltime** — leftover products  
3. Remaining `/work` rows still marked Needs brief (Kaltara cluster, e-KAHMI, e-Perdatin, PBAS, PAN congress, 2019 LSI situation room, Sulsel library geo-attendance, Prima Nusantara)  
4. Then re-rank GitHub Selected Projects against the full set  

**Do not brief:** e-SIAP Korlantas (Figma, discontinued); Patra Logistik (WordPress, not your work).
