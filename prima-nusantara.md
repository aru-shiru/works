# Prima Nusantara / ePROFMS — Resume Project Brief

**Role:** Client-facing for **PT Prima Nusantara Services (Prinus)** while an **external vendor** built v1; **you did not review** that code. After they left, you **rewrote dashboard + API** and kept the Flutter app alive (small slice). **Not** “I built this in 2022 from scratch.” **Not** sole on the rewrite.  
**Client / product:** **Prinus Building and Asset Management** (`APP_NAME` on Fly). Lamacca `/work`: Property & Facility Management System · **2022**.  
**Git start year (source of truth for lamacca.com):** **2022** — first commit **15 Jan 2022** (mobile). Site badge already **2022**; do not relabel to 2023 just because of the rewrite.  
**Period:** Vendor **Jan–Oct 2022**. Rewrite **28 Jun 2023 – Sep 2024** (peak **Jul–Aug 2023**). Your last: mobile package rename **10 Nov 2024**; dashboard storage (Tigris) **6 Nov 2025**.  
**Team:**  
- **v1 (outsourced):** **Codekaze** / **Deny** (`flutterlabz@gmail.com`, `codekaze.id@gmail.com`) plus danuseptian, Febri, IAMAntaRiksa on mobile. **You: 0 commits** on `manajemen-gedung-dashboard`.  
- **Rewrite / rescue:** you **224** non-merge on `eprofms-dashboard`; **Nana 64**, **Bimo 24**, **Nadella 12**, **zakikii 8**. Mobile keep-alive: **zakikii 34**, **you 11**.  
**Repos:** `~/code/manajemen-gedung-dashboard` · `~/code/manajemen-gedung-mobile` · rewrite `~/code/eprofms-dashboard` (`lamaccatech/…`)

This document is a **source brief**, not a finished resume entry. You already pointed all three folders.

Git matches the nasty memory: you sat in Prinus meetings; v1 landed without your review; it was a **Warehouse App / Ping-CRM fork** that demoed; you later migrated schema and rewrote Inertia in Laravel 10 / React rather than keep patching Vue 3 Mix.

**Not** on GitHub Selected Projects. **No MAU / store counts.** Prinus FM covers Giant / Hero (and former Carrefour) buildings — don’t enumerate sites. Do not paste ImgBB keys, Fly/Neon connection strings, `.env.example` `APP_KEY`, or demo logins.

---

## 1. Project overview

### 1.1 One-sentence pitch

Building FM for Prima Nusantara: web inventory of gedung → lantai → area → ruang → aset (QR), preventive **jadwal maintenance**, corrective **tiket** with assigned teknisi, plus a GetX Android app that still talks to a **legacy `api_token` API**.

### 1.2 Two generations

| | v1 vendor | What you ship |
|---|---|---|
| When | 2022 | Dashboard/API rewrite **2023** (Fly `eprofms` **2 Jul 2023**) |
| Web | Laravel **8** / Inertia **Vue** / Mix, leftover **Warehouse App** README | Laravel **10** / Inertia **React** / Vite / shadcn |
| API for phone | Same messy Laravel | Rewritten `routes/api.php` kept **vendor URL shapes** (`tiket/action/post-update-status/…`) so Flutter did not need a full rewrite |
| Mobile | GetX `warehouse_app`, still `com.prinus.warehouse_app` | **Not rewritten.** Point at new API; ticket/QR/upload fixes |

You chose **not** to rewrite Flutter because adding features on v1 web was already hell; mobile stays the pile that “works for demo,” patched.

### 1.3 Lamacca card vs git

| Card | In these clones |
|---|---|
| GIANT and Carrefour | Same Prinus book as Hero; Carrefour as a banner ended. Don’t list store counts |
| Android + web | Yes. iOS tree exists as Flutter default |
| Asset / PM / CM / teknisi / WO | Aset + QR PDF; jadwal maintenance + status + assign teknisi; tiket + assign; guest can **POST** `/api/tiket` **without** token |
| **2022** | Product start. Rewrite **2023**. Fly still `DB_DATABASE=warehouse_db` |

v1 README still documents **Warehouse App** + Ping-CRM demo user. `User::isDemoUser()` still checks `johndoe@example.com`. Superadmin is hardcoded `superadmin@hero.co.id`. **Do not paste passwords.**

### 1.4 Surfaces (rewrite)

| Actor | Surface |
|---|---|
| **Office** | `AdminLayout`: gedung (PDF/Excel), lantai/area/ruang, aset + QR, jadwal maintenance, tiket, kontrak, perizinan, users / manajemen-user (akses_gedung) |
| **Teknisi / supervisor / guest** | Flutter modules (`module/teknisi`, `supervisor`, `guest`) — QR scan, tickets, maintenance photos |
| **Mobile API** | `EnsureTokenIsValid` matches `Authorization` header to `users.api_token` (**not** Sanctum for those routes). Login/logout JSON |

`https://eprofms.fly.dev` in `fly.toml`. S3 (later Tigris). **Do not paste** the Neon hostname also sitting in that file.

---

## 2. Stack

### 2.1 v1 (do not advertise as yours)

Laravel 8.65, Inertia Vue 3, Mix + SSR, PHP 7.4/8. Flutter 2 / GetX with a kitchen-sink `pubspec` (charts, inappwebview, bitsdojo_window, faker, ImgBB). Commits are often `.` with no message.

### 2.2 Rewrite you led

| Layer | Pinned |
|---|---|
| PHP / Laravel | **8.1–8.2**, Laravel **10.10**, Breeze, Sanctum **on web**; mobile still **api_token** |
| Front | React **18.2**, Inertia React 1, Vite 4, Tailwind 3.2, shadcn/Radix |
| Files | Flysystem S3, DomPDF, Maatwebsite, Simple QRcode |
| Fly | PHP 8.2, `APP_NAME` Prinus Building and Asset Management |

Day-one rewrite commits: install Laravel, **migrate tables/relationships/filters from the old project**.

---

## 3. Contribution

### 3.1 You — meetings, then rescue

**2022:** 0 files on vendor dashboard. Client still saw you (Lamacca) in the room.  
**2023–2025:** Opened `eprofms-dashboard`. First-adds `app/`+`resources/`: you **344** vs Nana 18, Nadella 9, Bimo 7, zaki 1. Owned layout, gedung tree, tiket/maintenance assign, API compatibility. Mobile: 11 commits (gedung query, QR, ticket list/detail, **upload to server not ImgBB**, package rename). ImgBB **URL+key still in** `image_picker_controller.dart` — do not paste; treat as leftover.

### 3.2 Vendor (Codekaze / Deny)

Delivered the demo. Last dashboard **30 Sep 2022**; last mobile Deny **14 Oct 2022**. Then gone. `root@ip-172-31-…` commits on dashboard = their CI box, not you.

### 3.3 zakikii

Heavier on **mobile** (34, Aug–Oct 2023: point app at local/new API, QR, photos) than you. 8 API-alignment commits on eprofms.

### 3.4 Nana, Bimo, Nadella

Helped the **rewrite**, not v1. Nana: exports, user manajemen (last **17 Sep 2024**). Nadella: lantai/gedung export, superadmin-only export. Bimo: area counts, perizinan stats.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead (rescue), Prinus FM** — client 2022; rewrite 2023–2024  
Vendor v1 unmaintainable; I rewrote dashboard + API (Laravel 10 / React). Flutter kept, not rewritten.

### 4.2 Short blurb

Owned the Prima Nusantara relationship while Codekaze built v1 without our review; after they left I rewrote the Laravel API/admin (with Nana, Bimo, Nadella, Zaki) and pointed the existing GetX app at it.

### 4.3 Medium blurb

In 2022 Lamacca outsourced Prinus building-FM (assets, PM schedules, tickets/teknisi, Android) to Codekaze. I stayed in client meetings and did **not** review the repo — v1 is a Warehouse/Ping-CRM Laravel 8 + GetX app that demoed and then stalled when they resigned. From Jun 2023 I rewrote dashboard and API as `eprofms` (Laravel 10 / Inertia React, Fly), keeping vendor route shapes so mobile did not start over. Git start **2022**. Badge **2022**. No store counts.

### 4.4 Bullets

- Client-facing then rescue lead; 0 vendor-dashboard commits, then 224 on the rewrite.  
- Assets + QR, maintenance, tickets; Android stays vendor GetX.  
- Git start 2022; rewrite 2023. Do not list as a 2022 solo build.

### 4.5 Skills

Laravel 10, PHP 8.2, Inertia.js, React 18, Tailwind, shadcn/ui, Sanctum, Fly.io, S3/Tigris, DomPDF, QR, API compatibility with a legacy Flutter/GetX client

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I built Prinus in 2022” | **Vendor built v1.** You comms’d, then rewrote **2023**. |
| “I reviewed the outsource” | **0 dashboard commits.** You have said that was the mistake. |
| “I rewrote mobile too” | **API + web.** Flutter patched (Zaki > you). |
| “Sole rewrite” | Nana/Bimo/Nadella/Zaki on `eprofms-dashboard`. |
| GIANT + Carrefour sites | Same FM family (Hero too). Carrefour banner ended. No counts. |
| Token / guest ticket | Token header ≠ Sanctum. **Unauth POST tiket.** Don’t write that as a boast. |
| Warehouse / johndoe / hero.co.id / ImgBB / Neon | **Leftovers. Do not paste.** |
| SDS | Different client (Sekaiichi), later, different stack. Do not merge bullets. |

---

## 5. Suggested 3-bullet block

**Prinus Building & Asset Management** — Rescue / rewrite lead *(start 2022; rewrite 2023)*  
FM: gedung assets, PM, tickets. Android kept.

- Outsourced v1 (Codekaze); I did client meetings, not code review.  
- Rewrote Laravel 10 / React admin + API after they left; Nana/Bimo/Nadella/Zaki helped.  
- Flutter GetX still the field app (Zaki + a few of my commits).

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Prinus / Prima Nusantara** | PT Prima Nusantara Services — client |
| **ePROFMS** | Rewrite Fly app / repo name |
| **v1** | `manajemen-gedung-dashboard` + `manajemen-gedung-mobile` |
| **Warehouse App** | Vendor’s starter (Ping-CRM-ish); schema name survived on Postgres |
| **Codekaze / Deny / flutterlabz** | Outsourcing shop on v1 |

---

*Generated from the three clones as of 17 August 2026. Git start year 2022 is the source of truth for the later lamacca.com repair (badge already 2022).*
