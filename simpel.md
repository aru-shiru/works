# SIMPEL (Dishub perlengkapan jalan) — Resume Project Brief

**Role:** Tech Lead  
**Product:** **SIMPEL** — *Sistem Informasi Manajemen Perlengkapan Jalan* for Indonesian **Dinas Perhubungan**  
**Period:** June 2025 – present (base last landing Feb 2026; the three dinas last landed Nov–Dec 2025)  
**Team:** You as lead + **Bimo Eka Saputra** + **Nana Sukarna** (git confirms the two you remembered). Same pairing as ADVLive / e-Hibah v1.  
**Repos:**  
- Template: `~/code/dishub-perlengkapan-jalan-monorepo` (`lamaccatech/dishub-perlengkapan-jalan-monorepo`)  
- **Prov. Sulawesi Selatan:** `~/code/sulselprov-dishub-pj`  
- **Kab. Paser:** `~/code/paserkab-dishub-pj`  
- **Kota Samarinda:** `~/code/samarindakota-dishub-pj`  

This document is a **source brief**, not a finished resume entry.

**Not on** [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru) Selected Projects as of 16 Aug 2026 — **slot limit**, not a quality judgment. Re-rank the public cards after this works list is complete. There are **no public MAU / install numbers** — do not invent them.

---

## 1. Project overview

### 1.1 One-sentence pitch

SIMPEL is a **fork-per-dinas** road-furniture platform: Dishub staff map and maintain signs, lights, markings, and (in Paser) **street lighting / PJU** on road segments (`ruas jalan`), from an **Expo** field app + **Laravel/Inertia** dashboard, by cloning a Nx monorepo and filling `constants.php` + Fly apps rather than sharing one database.

### 1.2 Why it is not one SaaS tenant

Each dinas wants a slightly different catalog, map centre, admin level, and (sometimes) extra objects. The template (`CEKLIS.md`) is the product: copy the repo, set Kemendagri wilayah, jenis/kategori perlengkapan, Fly `APP_NAME`/`APP_URL`, Google Maps (optional Radar), then `nx deploy`. Interview wording: **white-label by git clone**, not Firestore `id_kandidat` (contrast Pantau Relawan).

### 1.3 The three clients

| Dinas | Admin level | Kemendagri | What is different |
|---|---|---|---|
| **Dishub Provinsi Sulsel** | `provinsi` (`73`) | Provincial | Extra role **Kabid**; `nama_aplikasi` **SIMPEL**; git: **migrasi data dari aplikasi SIMPEL yang lama**; you-only repo (33 commits) |
| **Dishub Kabupaten Paser** | `kabupaten_kota` (`64.01`) | 10 kecamatan | **PJU-only** catalog; pole codes, lamp type (LHE/LED), Pemda/PLN ownership, **panel distribusi** + IDPEL; **public** map/grafik/data views; heaviest team work |
| **Dishub Kota Samarinda** | `kabupaten_kota` (`64.72`) | Full furniture types (APILL, rambu, marka, …) | Extra **Kabid**; fly app `dishub-kota-samarinda`; git: migrated from a previous `dishub-dashboard` repo; almost you-only (Nana 1 commit) |

`sub_nama_aplikasi` is the same everywhere: *Sistem Informasi Manajemen Perlengkapan Jalan*. Fly `APP_NAME` is **SIMPEL**. Paser’s on-screen title is **DISHUB KAB. PASER**. Template default map/kode actually matches **Samarinda** (`64.72`) — the clone for that city is a config-complete copy, not a new domain.

### 1.4 Who uses it

| Actor | Surface | What they do |
|---|---|---|
| **Field / user** | Expo (native + web on Fly) | Sanctum login, GPS + photo perlengkapan, lokasi, gallery, maintenance |
| **Admin / perencanaan / Kabid** (Kabid = Sulsel + Samarinda) | Inertia dashboard | Ruas jalan, inventory, map cluster view, perencanaan, pemusnahan, laporan Excel, users |
| **Public (Paser)** | Unauthenticated pages | PJU map, charts, detail (Radar on public detail) |
| **Superadmin** | Policies | Exists in Laravel Policy classes; **not** in `constants.php` `opsi_role` — don’t recite a complete org chart from memory |

No self-serve dinas onboarding: Lamacca duplicates the monorepo.

### 1.5 Product domains (shared)

- **Ruas jalan** + Kemendagri wilayah (`lamaccatech.github.io/wilayah-indonesia`)  
- **Perlengkapan jalan** on a ruas: jenis, kategori, posisi, condition baik/tidak_baik, gallery, GPS titik  
- **Perencanaan** (need vs installed per km — targets in constants)  
- **Maintenance** history  
- **Pemusnahan**  
- **Laporan** (Maatwebsite Excel)  
- **Activity log**  
- **Users** with role string (not a fancy CASL graph)

Paser extras: **InformasiPeneranganJalanUmum**, **PanelDistribusi**, kode tiang generator, public transparency pages.

---

## 2. Stack

Nx 21 workspace: `apps/dashboard` (Laravel inside the monorepo), `apps/mobile` (Expo 52), `packages/shared/{schema,constant,util}` (Zod schema shared with the phone).

| Surface | Stack |
|---|---|
| **Dashboard** | Laravel **12**, Inertia **2**, **React 19**, TypeScript, Tailwind 4, shadcn/Radix, Recharts, Google Maps (`@vis.gl/react-google-maps` + MarkerClusterer), MapLibre available, Turf |
| **Mobile** | **Expo 52** / RN 0.76, **expo-router**, NativeWind, TanStack Query, react-hook-form + Zod, expo-location, expo-image-picker, react-native-maps, Google Places autocomplete, SecureStore |
| **API** | Laravel **Sanctum** token API under `/api` (login, CRUD perlengkapan, lokasi, maintenance, gallery sync, Google Places **proxy**) |
| **Auth (web)** | Laravel starter-kit session (Breeze-style pages still present) + Policies on dashboard/API |
| **DB / files** | **SQLite on a Fly volume** (same pattern as e-Hibah v1); **Tigris/S3** for photos. Sulsel: Vultr → Tigris storage command |
| **Deploy** | Fly.io Singapore — **one dashboard + one mobile (Expo web) per dinas**. EAS profiles exist for Android APK/AAB |
| **Exports** | Maatwebsite Excel |

This is **Inertia + React**, not Vue/Livewire. Do not mix with e-Hibah’s Flux rewrite.

### 2.1 Per-dinas Fly (production-shaped)

| Dinas | Dashboard | Notes |
|---|---|---|
| Template | `dishub-perlengkapan-jalan-monorepo-dashboard` | 256 MB, auto-stop |
| Sulsel | `sulselprov-dishub-pj-dashboard` | 256 MB, auto-stop |
| Paser | `paserkab-dishub-pj-dashboard` | 512 MB, **min 1** machine |
| Samarinda | `dishub-kota-samarinda` | **1 GB / 2 CPUs**, min 1 |

Mobile Fly apps: `*-mobile` pointing `EXPO_PUBLIC_API_URL` at that dinas `/api`. Native branding still often `EXPO_PUBLIC_APP_NAME=Dishub` even when the dashboard is SIMPEL.

---

## 3. Contribution

### 3.1 You led; Bimo and Nana are in git

You remembered correctly. **No third name** showed up.

| Repo | Commits | Added lines* |
|---|---|---|
| Template | you 91 · Bimo 60 · Nana 23 | you **~88%** · Nana ~6% · Bimo ~6% |
| Paser | you 84 · Bimo 78 · Nana 37 | you **~77%** · Bimo ~12% · Nana ~11% |
| Sulsel | **you 33** | 100% |
| Samarinda | you 36 · Nana 1 | ~100% you |

\*Excluding lock/vendor. Template % is inflated by the initial Laravel/Expo dump — **do not** say “I wrote 88% of SIMPEL.” Interview: you **designed the forkable product**, reviewed, deployed, and did the Sulsel/Samarinda cutovers; Bimo and Nana shipped a lot of **Paser PJU + public pages** and base UI.

**Bimo (from subjects):** shared types, mobile maps, Paser panel distribusi CRUD, kode tiang, PJU fields on phone.  
**Nana (from subjects):** dashboard charts/dark mode, public PJU data/grafik/detail, Radar on public detail, filters.  
**You:** template + `CEKLIS.md`, Fly/Nx deploy, Sanctum API, policies, Excel, data/storage migrations, Paser model `PanelDistribusi`, delete guards, the two “thinner” dinas.

### 3.2 Outcomes you can defend

- One **duplicable** Dishub product instead of a one-off.  
- **Three** live Fly deployments with local catalog/geo/role tweaks.  
- Paser specialized into **PJU + public transparency** without forking a new architecture.  
- Sulsel **imported data from an older SIMPEL** (commit exists; don’t invent the old stack).  
- Field loop: GPS + photos on Expo against a Sanctum API with a Google Places proxy (key stays on the server).

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, SIMPEL (Dinas Perhubungan)** — Jun 2025 – present  
Road-furniture (perlengkapan jalan) inventory for three Dishub: Provinsi Sulsel, Kab. Paser, Kota Samarinda. Nx monorepo (Laravel/Inertia + Expo) cloned per dinas.

### 4.2 Short blurb

Led a 3-person team (Bimo, Nana) on SIMPEL, a fork-per-dinas platform for Indonesian transportation agencies to inventory and maintain road furniture. Laravel 12 + Inertia/React dashboard and Expo field app on Fly.io; Kab. Paser specialized to street lighting (PJU) plus public maps.

### 4.3 Medium blurb

SIMPEL (*Sistem Informasi Manajemen Perlengkapan Jalan*) is a clone-and-configure product, not a multi-tenant SaaS: one Nx template (Laravel 12, Inertia, React, Expo, Sanctum, Google Maps) deployed three times on Fly.io. Dishub Sulsel runs the full provincial catalog and a migration from a previous SIMPEL; Dishub Paser is PJU-only with distribution panels and public charts; Dishub Samarinda is the kota-level full catalog. Tech lead with Bimo Eka Saputra and Nana Sukarna.

### 4.4 Bullets

- Tech lead for a **fork-per-dinas** perlengkapan-jalan product used by **three** Dishub (Sulsel, Paser, Samarinda).  
- Template Nx monorepo: Laravel 12 / Inertia 2 / React 19 dashboard + Expo 52 field app; `CEKLIS.md` to clone, set wilayah, and `nx deploy` to Fly.  
- Sanctum API for GPS+photo inventory, maintenance, gallery; Google Places proxied so the Maps key is not in the APK.  
- Kab. Paser: PJU-only domain (tiang, panel distribusi, IDPEL, lamp type) and **unauthenticated** public map/chart pages.  
- Prov. Sulsel: `level_administrasi = provinsi`, Kabid role, **data migration from earlier SIMPEL**.  
- Led **Bimo** and **Nana**; Sulsel and Samarinda cutovers are almost entirely your commits.

### 4.5 Skills

Laravel 12, Inertia.js, React, TypeScript, Expo / React Native, NativeWind, Laravel Sanctum, Google Maps, SQLite, Fly.io, Tigris/S3, Nx, Zod, Pest, Maatwebsite Excel

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| On GitHub Selected Projects | **Not listed** as of Aug 2026 (limited slots; re-rank later) |
| MAU / number of poles | **Unknown**. Cite three dinas, not traffic. |
| “Multi-tenant SaaS” | **Three git + Fly copies**. Shared code by duplication. |
| “Vue Dishub app” | **Inertia + React** dashboard, **Expo** mobile |
| “Postgres” | Fly **SQLite** volume (like e-Hibah v1) |
| Paser APP_DEBUG | fly.toml has `APP_DEBUG=true` — **do not** brag; don’t put it on a resume |
| Complete role matrix | `opsi_role` vs Policy `superadmin` don’t match 1:1 |
| NTB “SIMPEL PBJ” | **Different product** (procurement). This SIMPEL is **perlengkapan jalan**. |
| “Bimo/Nana built all three” | They are **on the template and Paser**. Sulsel is **you only**; Samarinda **you + 1 Nana commit**. |

---

## 5. Suggested 3-bullet block

**SIMPEL (Dishub)** — Tech Lead *(Jun 2025 – present)*  
Perlengkapan-jalan inventory for three Dinas Perhubungan (Sulsel, Paser, Samarinda).

- Designed a clone-and-configure Nx template (Laravel/Inertia dashboard + Expo field app) deployed separately on Fly.io per dinas.  
- Led Bimo and Nana; Paser specialized to street lighting (PJU, panels, public maps) instead of the full rambu/APILL catalog.  
- Field teams log GPS+photos via Sanctum; Sulsel imported data from an earlier SIMPEL instance.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **SIMPEL** | Sistem Informasi Manajemen Perlengkapan Jalan |
| **Perlengkapan jalan** | Road furniture: rambu, marka, APILL, PJU, guardrail, … |
| **PJU** | Penerangan Jalan Umum (street lighting) — Paser’s whole catalog |
| **Ruas jalan** | Named road segment inventory is attached to |
| **Dishub** | Dinas Perhubungan (transportation agency) |
| **Kabid** | Kepala Bidang — extra role on Sulsel and Samarinda |

---

*Generated from the four `*-dishub-pj` / `dishub-perlengkapan-jalan-monorepo` trees as of 16 August 2026. Team confirmed from git (Bimo + Nana). Not present on github.com/aru-shiru/aru-shiru Selected Projects.*
