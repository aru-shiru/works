# e-Watch LSI (Denny JA) — Resume Project Brief

**Role:** Sole Full-Stack Engineer (product, implementation, ops, **client meetings**)  
**Client / product:** e-Watch for **Lingkaran Survei Indonesia (LSI) / Denny JA** — field canvassing monitor (dropping + persuasi)  
**Period:** December 2022 – present. Original Vue 3 + Flutter firmed up through Jan 2024; **dashboard rewritten Nuxt UI Pro** from **July 2024** (last UI bump Nov 2025).  
**Team:** Sole developer (141 commits on the original Nx repo; 151 on the lamaccatech LSI path — both Muhammad Dzul Arsyil). You were the engineering contact in **client meetings** with LSI / Denny JA as well — no PM/BA in the middle.  
**Repos:** original `~/code/lingkaran-survei-indonesia`; dashboard rewrite `~/code/lamaccatech/apps/lingkaran-survei-indonesia`  

This document is a **source brief**, not a finished resume entry.

Public GitHub listing: **e-Watch LSI — Political Fieldwork Monitoring Platform** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). Public metrics on that card: **~500K** field submissions · **~100** concurrent internal users. Those numbers are **not in these repos** — cite the profile, do not invent extras. **Denny JA / LSI** is the client name you use; git does not contain that string.

---

## 1. Project overview

### 1.1 One-sentence pitch

e-Watch is LSI’s fieldwork app: canvassers submit geotagged dropping and persuasi (survey) evidence from a Flutter phone, and HQ watches regional progress, galleries, and questionnaire stats on a dashboard — Firebase throughout; dashboard moved from a Vue 3 + Bootstrap SPA to **Nuxt UI Pro** for a cleaner ops UI while **mobile stayed Flutter**.

### 1.2 Original stack (you remembered correctly)

You were right: **Vue dashboard + Flutter mobile**. Pin-downs from `~/code/lingkaran-survei-indonesia` (Nx 15.4, first commit **29 Dec 2022**):

| Surface | Stack |
|---|---|
| **Dashboard** | **Vue 3.2** SPA (not Vue 2, **not Nuxt**), vue-router 4, Pinia, VueFire, **Bootstrap 5**, DataTables, ApexCharts |
| **Mobile** | Flutter `dropping_mobile` 1.0.1+2, Riverpod 2.1, sqflite drafts, Geolocator, **no go_router** (named `Navigator` routes) |
| **API** | Firebase Cloud Functions (Node 16 in that repo): custom-token `login`, Firestore triggers, daily counter reset |
| **Maps / reports** | **Google** Geocoding + Static Maps — **not Mapbox** in this tree. Puppeteer HTML→JPEG reports + Sharp for map snapshots |

Firebase project `lingkaran-survei-indonesia`, region `asia-southeast2`. Last original-repo landing **27 Jan 2024** (migration script).

### 1.3 Why the dashboard rewrite

The Vue + Bootstrap ops UI worked; it did not look like a 2024 product. You ported the **same Firebase project** into the Lamacca Tech monorepo and rebuilt HQ as **Nuxt 3.17 + @nuxt/ui-pro 1.8**, Unovis instead of ApexCharts, lightbox galleries — **cleaner UI**, not a new data model. Flutter remaining in the original repo is intentional: field devices already shipped.

### 1.4 Who uses it

| Actor | Surface | What they do |
|---|---|---|
| **Relawan** | Flutter | Username login (no email), GPS + photo **dropping**, **persuasi** + dynamic questionnaire, offline drafts |
| **Admin / monitoring** | Dashboard | Tenant switcher, data masuk (table + gallery), regional/volunteer stats, dropping/persuasi detail + generated report JPEG |
| **Superadmin** | Dashboard | User create, switch `applications` (tenants) |

Tenants are Firestore **`applications`** (`modules: dropping | persuasi`, `regions`, users via `users.aplikasi` + Auth claim `app`).

### 1.5 Field journeys

**Dropping.** Relawan in-range → camera → compress → if offline, **sqflite** `draft.db` (preview blobs) → `QueueService` watches connectivity × auth × tenant and uploads Storage + Firestore. Trigger reverse-geocodes, saves `…-map.jpeg`, later composites `…-report.jpeg` (photo + map + address + time) via Handlebars + Puppeteer.

**Persuasi.** Same geotag/photo path plus per-app questionnaire (`applications/{id}.questionnaire`). Dashboard donuts multiple-choice answers.

**Auth.** Callable `login`: `accounts/{username}` + bcrypt → `createCustomToken` → `signInWithCustomToken`. Same pattern as Pantau Relawan. Relawan never need an email.

**Stats.** onCreate/onUpdate/onDelete bump per-user / per-region / per-app counters. `resetDailyCounter` zeros “hari ini” (scheduler in original used timezone `Asia/Tokyo` — do not pitch that unless asked).

### 1.6 Dashboard rewrite surface (`lamaccatech`, Jul 2024+)

| App | What |
|---|---|
| `e-watch-dashboard` | Nuxt UI Pro, VueFire, Pinia, Unovis (daily bars, questionnaire donuts, region cards), Data Masuk, Statistik, users |
| `functions-users` / `functions-projects` / `functions-chore` | Same login/create-user, dropping/persuasi reports, daily reset — Node 20, firebase-functions 6 |
| `presidential-election` | Separate Nuxt + **Mapbox** fullscreen stub — **unfinished**, no election data. This is why GitHub mentions Mapbox; **e-Watch dashboard itself still has no interactive map** |
| `scripts` | Seed, backfill reports, CSV |

**151 commits, you only.** Latest notable: `chore: update nuxt ui` **19 Nov 2025**.

### 1.7 Timeline

| Period | What |
|---|---|
| **Dec 2022 – Jan 2024** | Greenfield Nx: Vue 3 HQ, Flutter field, Functions, Google static-map reports |
| **Jul 2024** | Lamacca monorepo: Nuxt UI Pro e-Watch dashboard + split functions + presidential-election stub |
| **2025** | Nuxt UI upkeep; Flutter still lives in the 2022 repo |

---

## 2. Tech stack

### 2.1 Short list

TypeScript · Vue 3 · Nuxt 3 · Nuxt UI Pro · Pinia · VueFire · Flutter · Riverpod · sqflite · Geolocator · Firebase (Auth, Firestore, Storage, Functions) · Puppeteer · Sharp · Unovis · Google Maps Geocoding/Static · Nx / Turborepo

### 2.2 Highlights

- **Offline-first Flutter drafts** that survive process death; dual-resolution images through `FormStatus`.  
- **Multi-tenant single APK** — modules and questionnaires from `applications/{id}`.  
- **Evidence pipeline:** GPS → geocode → static map → Puppeteer report JPEG (not an in-browser PDF).  
- **Dashboard restyle without a second backend** — Nuxt UI Pro + Unovis on the same Firestore.  
- Custom tokens so survey temp staff are not Firebase-email users.

### 2.3 GitHub vs this tree

| GitHub card | Actual |
|---|---|
| Nuxt dashboard | True **after Jul 2024**. Original HQ was **Vue 3 SPA + Bootstrap**. |
| Mapbox GL | **Presidential-election stub only.** e-Watch maps are Google static snapshots. |
| Turborepo | Lamacca rewrite. Original is **Nx**. |
| go_router | **No** — named Navigator routes. |

---

## 3. Contribution

Sole engineer on both trees **and** the person in the room with LSI / Denny JA. Requirements (dropping vs persuasi, GPS lock, questionnaire shape, report JPEG HQ could print, username login for field staff without email) came from those meetings, then you implemented them. Git: 141 + 151 commits, one author. No junior split.

You took the product from “Vue CLI + Bootstrap HQ + Flutter dropping app” to “same field APK, Nuxt UI Pro ops console,” including Puppeteer report jobs, custom-token auth, and tenant `applications`. Interview wording: **sole full-stack, client-facing** — same as SDS, not “ticket-queue implementer.”

### Outcomes

- Real field loop: geotagged dropping/persuasi → auto address + report image → HQ gallery/stats.  
- GitHub: **~500K** submissions, **~100** internal users (profile only).  
- Offline queue that does not lose a kabupaten day’s photos.  
- UI cleanup **without** a Firestore rewrite (contrast e-Partai / ADVLive).

---

## 4. Suggested resume copy

### 4.1 Header

**Sole Full-Stack Engineer, e-Watch LSI (Denny JA / Lingkaran Survei Indonesia)** — Dec 2022 – present  
Field dropping + persuasi monitor. Flutter (offline drafts) + Firebase; dashboard Vue 3 then **Nuxt UI Pro**. Sole engineer, **including client meetings**.

### 4.2 Short blurb

Sole engineer for LSI e-Watch — also the client contact: Flutter field app with sqflite draft queue and custom-token login; Firestore geotagged dropping/persuasi; Puppeteer report JPEGs; HQ dashboard originally Vue 3, rewritten with Nuxt UI Pro.

### 4.3 Bullets

- Sole developer for LSI (Denny JA) fieldwork monitoring **and the client meetings**: **dropping** (APK material) and **persuasi** (dynamic questionnaire) with required GPS + photo.  
- Flutter (Riverpod, sqflite offline queue, Geolocator): username/password → Cloud Functions **custom token**; one APK, many `applications`.  
- Firestore triggers: Google reverse-geocode, static map snapshot, Puppeteer/Handlebars **report JPEG**.  
- Rebuilt the HQ dashboard as **Nuxt 3 + Nuxt UI Pro** (Unovis stats, galleries) on the **same Firebase project** — mobile not rewritten.  
- Real-time per-user / per-region / per-day counters with a scheduled daily reset — requirements taken in **client meetings**, not a ticket queue.

### 4.4 Skills

Vue 3, Nuxt 3, Nuxt UI Pro, Flutter, Riverpod, SQLite, Firebase, Puppeteer, Sharp, Google Maps API, VueFire, Pinia, Unovis, Nx, Turborepo

### 4.5 Say carefully

| Claim | Safer |
|---|---|
| ~500K / ~100 users | GitHub card only |
| “Mapbox on e-Watch” | Unfinished **presidential-election** app; field pipeline is **Google Static Maps** |
| “I rewrote the mobile app” | **Dashboard (+ functions packaging) only** |
| Original was Nuxt | Original HQ is **Vue 3 + Bootstrap SPA** |
| Timezone trivia | Don’t lead with `Asia/Tokyo` on the daily reset |

---

## 5. Suggested 3-bullet block

**e-Watch LSI (Denny JA)** — Sole Full-Stack Engineer *(Dec 2022 – present)*  
Field monitoring for Lingkaran Survei Indonesia (dropping + persuasi). Sole engineer, **including client meetings**.

- Flutter field client: custom-token auth, GPS photos, sqflite draft queue that uploads when back online.  
- Firebase pipeline: geocode, static map, Puppeteer report JPEGs, live regional/user stats.  
- Vue 3 + Bootstrap HQ rewritten to **Nuxt UI Pro** on the same project — cleaner ops UI, same mobile APK.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **LSI / Denny JA** | Client (survey / political consulting) |
| **Dropping** | Photo+GPS of campaign material drop |
| **Persuasi** | Survey capture + questionnaire |
| **Application** | Tenant, not a job-application |

---

*Generated from `lingkaran-survei-indonesia` and `lamaccatech/apps/lingkaran-survei-indonesia` plus the e-Watch LSI section of github.com/aru-shiru/aru-shiru as of 16 August 2026. Original dashboard confirmed Vue 3 + Flutter, not guessed.*
