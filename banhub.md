# Banhub / SIAP (Kaltara) — Resume Project Brief

**Role:** Tech lead — **not** review-only. You opened Laravel, first-added most `app/`+`resources/`, and still have the most non-merge commits. You also merged PRs.  
**Client / product:** **SIAP** (`APP_NAME` / logo **SIAP #kaltaradihati**) for **Badan Penghubung Provinsi Kalimantan Utara**. Instagram in-app: `@banhub_kaltara`. Lamacca splits this into **two** `/work` cards (both still **2023**). **One repo.**  
**Git start year (source of truth for lamacca.com):** **2023** — first commit **15 May 2023**. Matches both live badges; do not “repair” the year unless the site later invents a range.  
**Period:** **15 May 2023 – 28 Nov 2023** (build; peak **Jun 2023**). Your later commits: **19 Feb 2024** (`chore: remove DS_Store`), **6 Nov 2025** (`feat: migrasi storage`).  
**Team:** same Kaltara four — you, **Bimo Eka Saputra** (`bimoekas`), **Nadella** (`nadella987`), **Nana Sukarna** (`NanaSukarna16`). All four are real builders here (Nana is **not** a SSH cameo). `lamaccatech` = merge-only.  
**Repo:** `~/code/kaltara-banhub` (`lamaccatech/kaltara-banhub`)

This document is a **source brief**, not a finished resume entry.

You already pointed this folder. **Room Reservation** and **Banhub Activity Monitoring** are the public vs admin faces of **one** Laravel app. Menu tiles and the admin sidebar follow **logged-in roles** — not two deployables.

**Not** on GitHub Selected Projects. **No MAU.** Dashboard totals and the pegawai CSV seeder are **not** analytics. Do not paste seeder emails / password hashes.

---

## 1. Project overview

### 1.1 One-sentence pitch

Public/home (banners, fasilitas, berita, guesthouse booking) plus an admin CMS and field-ops logs (BBM, kendaraan, kegiatan pelayanan) for Badan Penghubung Kaltara’s Jakarta/Tarakan liaison office — **one SPA**, menus by role.

### 1.2 Two Lamacca cards vs this clone

| Card | In `kaltara-banhub` |
|---|---|
| **Room Reservation System** | Guest-facing booking after login: dates, kamar, KTP; status booked → confirmed → checked-in / out; hunian Excel |
| **Banhub Activity Monitoring System** | Pegawai/driver/security logs + admin review of **kegiatan pelayanan**, **pengisian BBM**, **penggunaan kendaraan** |
| Online booking / “approval” | **Auth + admin `verified` flag** to *create* a booking. User confirms (`booked` → `confirmed`). Admin check-in/out. Cron auto-cancels unconfirmed. **Not** a multi-dinas SKPD workflow |
| Payment / QRIS | Static **QRIS image** shown after booking: pay **at check-in**. **No** Midtrans/xendit |
| Reporting & analytics | Admin dashboard = **three counts**. Excel exports (reservasi, hunian, BBM, kendaraan, kegiatan). No BI |
| **2023** | Git **2023** (build). Last code **2025** storage |

Do **not** list this as two resume products. Sister of other **Kaltara** work (same four people); **not** BKAD SSH/recon and **not** Setda SIMAS.

### 1.3 Surfaces (one repo)

| Surface | Layout | Who |
|---|---|---|
| **Public / home** | `Welcome/Index` + `MobileLayout` | Unauth: logo Badan Penghubung, banners, fasilitas, berita, login. Auth: greeting + 4 tiles **if** `pegawai` or `admin` |
| **Guest booking** | same mobile app | `auth`; **create** needs `verified`. Role seed default on register: **`user`**. “Reservasi Saya” |
| **Field ops** | mobile | Policies: **pegawai** BBM + kegiatan pelayanan; **security** penggunaan kendaraan; **driver** as the named person on kegiatan |
| **Admin** | `AdminLayout` (desktop sidebar + sheet on small) | `admin` **or** `admin_reservasi` (`IsAdmin`). Vehicle/BBM/kegiatan **nav items hidden** unless `admin`; routes themselves still allow `admin_reservasi` |

Roles (`RoleSeeder`): `admin`, `user`, `pegawai`, `driver`, `security`, `admin_reservasi`. Many-to-many `role_user`. Seed maps jabatan: Kepala Badan → admin; Keamanan → pegawai+security; Pengemudi → pegawai+driver; else pegawai.

`fly.toml`: app **`siap-kaltara`**, `APP_URL` **https://siap-kaltara.fly.dev**, SQLite volume, `FILESYSTEM_DISK=s3`, PHP 8.2. That hostname is **in git**; do not invent uptime.

---

## 2. Stack

**Not** the Laravel-9 / Vue generation (SIMAS, SSH, recon). This is Laravel **10** / **Inertia React** / **shadcn**.

| Layer | Pinned |
|---|---|
| PHP | ^8.1 (Sail **8.2** in README; Fly **8.2**) |
| Laravel | **10.13** (Breeze 1.21) |
| Front | React **18.2**, Inertia React 1.0, Vite 4, Tailwind 3.2, **Radix / shadcn**, TanStack Table, react-day-picker |
| Excel | Maatwebsite **3.1** |
| Files | Flysystem S3 v3 (prod disk s3) |
| Auth | Breeze **email** login (not username). Custom `verified` flag **≠** Laravel email verification |

Stock Laravel README + Sail composer install. Product name lives in Fly env and logo alt text.

---

## 3. Contribution

Non-merge on `main`: **you 196**, Bimo **169**, Nadella **88**, Nana **25**. `lamaccatech` **96** merges, 0 non-merge. First-adds `app/`+`resources/`: you **173**, Bimo 89, Nadella 59, Nana 20.

Bimo last **28 Nov 2023**; Nadella last **28 Nov 2023**; Nana last **6 Jul 2023**. After that, you.

### 3.1 You

`chore: install laravel` / shadcn the first day. Models: Role, User, Kendaraan, KegiatanDriver, PenggunaanKendaraan, PengisianBahanBakar, Kamar, ReservasiKamar, Banner, Berita, Galeri, Fasilitas. Field-ops **policies**. Admin review controllers (`Admin*`). Welcome `_UserNavigation` (Menu Admin vs Reservasi Saya). Guest booking create pages. Dashboard. Last product: storage migrate (S3). Lead **and** reviewer of PRs.

### 3.2 Bimo

Welcome/home (controller, card image, footer, Instagram). First kamar + kendaraan CRUD + galeri. Fasilitas CMS. Policies for kamar/banner/berita/kendaraan. **StatusReservasi** check-in / check-out / admin cancel.

### 3.3 Nadella

Operator modules: **BBM**, **aktivitas kendaraan**, **kegiatan driver**. Then admin **reservasi**, **laporan hunian**, Excel exports, banner/berita/kamar after the first pass.

### 3.4 Nana

**My reservation**, **user verifikasi**, admin **kegiatan pelayanan** list/show/export, some Edit screens. Real builder; smaller slice than SSH-recon Nana.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, SIAP / Banhub (Badan Penghubung Kaltara)** — May 2023 – Nov 2023  
Guesthouse booking + liaison-office ops. Laravel 10 / Inertia / React. Same four as SIMAS/SSH.

### 4.2 Short blurb

Led Bimo, Nadella, and Nana on Badan Penghubung Kaltara’s SIAP: public guesthouse booking and an admin/field app for rooms, BBM, vehicles, and kegiatan pelayanan — one repo, menus by role.

### 4.3 Medium blurb

From May–Nov 2023 we shipped SIAP for Badan Penghubung Pemprov Kaltara (Jakarta / Tarakan): a public Inertia home (fasilitas, berita, room search) and an admin CMS plus pegawai/driver/security logs. Laravel 10 / React / shadcn — not the 2022 Vue Kaltara stack. Lamacca lists this as two `/work` cards; git is **one** app. I opened the repo and first-authored most of the app layer; Bimo owned home + kamar/kendaraan/fasilitas; Nadella owned the field-ops modules and hunian export; Nana owned my-reservation and user verify. Git start **2023** (matches the badges). Fly app `siap-kaltara`. QRIS at check-in, no payment gateway. No MAU.

### 4.4 Bullets

- Tech lead of the Kaltara four; one product covering two company-site cards.  
- Role-gated menus: guest booking vs pegawai BBM/kendaraan/kegiatan vs admin CMS.  
- Laravel 10, Inertia React, shadcn; Fly (`siap-kaltara`) + S3.

### 4.5 Skills

Laravel 10, PHP 8.2, Inertia.js, React 18, TypeScript, Tailwind, shadcn/ui, Breeze, Maatwebsite Excel, Fly.io, S3, role middleware, reservation status machine

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “Two systems” / two resume entries | **One** repo. Two marketing cards. |
| “I only reviewed” | **196** non-merge; initial scaffold + most first-adds. |
| “Vue like SIMAS” | **React 18** / Laravel **10**. |
| “Payment gateway” | **QRIS JPEG** at check-in. |
| “Approval workflow” | User **confirm** + admin **check-in** + cron cancel. |
| Occupancy / user / trip counts | **Unknown.** Dashboard counts live DB; CSV seeder is staff fixture. |
| Pegawai CSV / `@kaltaraprov.go.id` | **Do not paste.** Some rows default `bcrypt('password')`. |
| “2023 only” | Build **2023**; last commit **Nov 2025** storage. Badge year **2023** is the start year. |
| Same as SSH / SIMAS | **Badan Penghubung**, not BKAD / Setda. Same humans, 2023, React. |

---

## 5. Suggested 3-bullet block

**SIAP / Banhub (Badan Penghubung Kaltara)** — Tech lead *(2023)*  
Guesthouse booking + liaison-office activity logs.

- Led Bimo, Nadella, and Nana; I scaffolded Laravel 10 / Inertia React and most of the app layer.  
- One app, two site cards: room reservation (check-in/out, hunian Excel) and BBM / kendaraan / kegiatan pelayanan.  
- Menus by role; Fly `siap-kaltara`; git start 2023.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **SIAP** | Product name in Fly / logo (`#kaltaradihati`) |
| **Banhub** | Badan Penghubung (client + Instagram / repo name) |
| **Kegiatan pelayanan** | Driver/pegawai field log (`kegiatan_driver`) — the “official agendas” card line |
| **admin_reservasi** | Second admin role: same `admin` middleware; sidebar hides BBM/kendaraan/kegiatan |
| **verified** | Custom boolean; admin verifies registered guests before they can **store** a booking |

---

*Generated from `~/code/kaltara-banhub` as of 17 August 2026. Git start year 2023 is the source of truth for the later lamacca.com repair. Two `/work` cards, one clone; do not split resume bullets.*
