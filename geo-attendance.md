# Geo-Location Attendance (Sulsel) — Resume Project Brief

**Role:** Sole full-stack — **not** a team lead of others. Git is **you only** (two email spellings).  
**Client / product:** Absensi pegawai for **Dinas Perpustakaan dan Kearsipan Provinsi Sulawesi Selatan**. Android label **Absensi Online**. Login chrome uses the Sulsel seal. Lamacca `/work` badge **2022** (repair later).  
**Git start year (source of truth for lamacca.com):** **2021** — first commit **22 Nov 2021**.  
**Period:** **22 Nov 2021 – 2 Feb 2022** (build; peak **Dec 2021**). Last commit **19 Jan 2024** (`feat: location access dialog`).  
**Team:** solo. 0 `lamaccatech` merges. Second author string `dzularsyil.muhamnmad@gmail.com` is a **typo of your gmail**, not a second person.  
**Repo:** `~/code/absensi-pegawai` (`lamaccatech/absensi-pegawai`) — Nx monorepo: React recap, Flutter app, Nest on Cloud Functions, CLI seed/import.

This document is a **source brief**, not a finished resume entry.

You remembered React dashboard + Flutter mobile and solo work. Git matches.

**Not** on GitHub Selected Projects. **No MAU / headcount.** Recap home stats are **hardcoded placeholders**. Import-user hashes **NIP as password** — do not paste.

---

## 1. Project overview

### 1.1 One-sentence pitch

Staff check in/out on Android from a **100 m circle** around known Makassar office coordinates; Nest/Firebase writes the punch, schedules daily/monthly recap, and a React admin verifies leave / attendance requests.

### 1.2 Lamacca card vs this clone

| Card | In `absensi-pegawai` |
|---|---|
| GPS-validated check-in/out from predefined locations | **Client-side** (`geolocator` vs four hardcoded `LatLng` + 100 m). Nest **stores** lat/lng; it does **not** re-check the fence |
| “Remote and field-based teams” | Office sites (perpustakaan / arsip Makassar). Kepala Dinas & Sekretaris Dinas may use **all four** pins; everyone else **arsip only** |
| Leave request | Real: Flutter submit + admin verify/reject (`Izin`, `Sakit`, several cuti types) |
| Attendance recap & reports | Scheduled Firestore recap + React tables. Recap **dashboard numbers are dummy** (`71,897`) |
| Web admin | React 17 `apps/recap` (Tailwind 2). **No** Firebase Hosting config — no dashboard hostname in git |
| Android app | `com.lamacca.attendance.attendance`. iOS folder is default Flutter scaffold — **card does not claim iOS** |
| **2022** | Git **2021–2022** (plus 2024 location dialog) |

Pengaturan screen is a **dead form** (no save). Radius is a Flutter constant (`TODO: load radius from settings`). News on home is **lorem + picsum**, not a CMS.

### 1.3 Surfaces

| Actor | Surface |
|---|---|
| **Pegawai** (`Role.User`) | Flutter: username (NIP) + password → Nest custom token → Firebase Auth. Check-in map, history, leave, attendance request, photo optional |
| **Admin / Superadmin** | React recap: history, monthly recap per employee, leave queue, attendance-request queue, users. Verify/reject is HTTP + RolesGuard |
| **Schedulers** | Pub/Sub Functions `asia-southeast2`, timezone **Asia/Makassar**: daily recap (weekdays 00:00), monthly (1st), previous-day rollup. `TODO: auto checkout` |

No `fly.toml`. API in git: `https://asia-southeast2-absensi-pegawai-319a5.cloudfunctions.net/api`. Firebase project `absensi-pegawai-319a5`. **Do not paste** web API keys from `recap-feature-shell.tsx` or Maps keys from Functions config.

---

## 2. Stack

One Nx **13.2** workspace (`@nxrocks/nx-flutter` for the app).

| Layer | Pinned |
|---|---|
| Recap (web) | **React 17.0**, React Router 6, React Query 3, Tailwind 2.2, Headless UI, Heroicons, Luxon |
| Mobile | Flutter (Dart `>=2.16`), Riverpod 1, google_maps_flutter, geolocator 8, Firebase Auth/Firestore/Storage/Crashlytics |
| API | **NestJS 8** on **firebase-functions** 3 (Node 14), Passport Firebase JWT, bcrypt, luxon |
| Data | Firestore (+ RTDB rules file present). Geocode via Google Maps (Functions config) |
| Tools | `apps/admin-tools` CSV import / faker seeder |

Stock Nx README. Product title on Android: Absensi Online.

---

## 3. Contribution

**132** non-merge commits, all Muhammad Dzul Arsyil. 110 in **2021**, 22 in **2022**, 1 in **2024**.

Opened Nx + Firebase + Functions on day one (`22 Nov 2021`). Recap shell, login, React Query, then Flutter attendance (maps, occupation-based pins, leave, requests). Nest: username login, punches, leave/attendance-request verify, daily/monthly recap, reverse-geocode address. 2024: location permission dialog.

No juniors. Treat “solo” as accurate.

---

## 4. Suggested resume copy

### 4.1 Header

**Sole Full-Stack, Geo Attendance (Dispusipda Sulsel)** — Nov 2021 – Feb 2022  
Flutter check-in + React recap + Nest on Cloud Functions.

### 4.2 Short blurb

Solo-built GPS attendance for Sulsel’s library/archives dinas: Flutter punches inside office geofences, Nest/Firestore recap, React admin for leave and missed-clock requests.

### 4.3 Medium blurb

From Nov 2021 to Feb 2022 I shipped an Nx monorepo for Dinas Perpustakaan dan Kearsipan Sulsel: Android Flutter (maps + 100 m fence; Kepala/Sekretaris may use four Makassar sites), NestJS on Firebase Functions (username/NIP login, punches, scheduled daily/monthly recap, Asia/Makassar), and a React 17 recap console. Lamacca badges **2022**; git starts **2021**. Fence is **on the device**, not re-validated in the API. Recap home stats and settings save are unfinished. No MAU.

### 4.4 Bullets

- Sole author (Flutter + React recap + Nest Functions).  
- Office geofence check-in/out, leave + attendance-request verify, scheduled recap.  
- Git start **2021** (site still **2022**).

### 4.5 Skills

Flutter, Riverpod, Google Maps, geolocator, React 17, React Query, Tailwind, Nx, NestJS, Firebase Auth/Firestore/Functions, Cloud Scheduler / Pub/Sub, Luxon

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “Team lead” | **Solo.** |
| “Server-enforced GPS” | App checks radius. API accepts coordinates. |
| “Field / remote anywhere” | Four (or one) **office** pins in Makassar. |
| Recap dashboard KPIs | **Placeholder numbers.** Real recap is the employee tables. |
| Settings / live radius | **Stub.** 100 m hardcoded. |
| In-app news | **Mock.** |
| Staff / NIP counts | **Unknown.** Import hashes NIP — **do not paste.** |
| “2022 project” | Badge. **Git start 2021.** |
| iOS app | Template only. Card is **Android**. |
| Firebase web config keys | **Do not paste.** |

---

## 5. Suggested 3-bullet block

**Geo Attendance (Dispusipda Sulsel)** — Sole full-stack *(2021 – 2022)*  
Office-fenced check-in plus admin recap.

- Flutter Android map punch; React recap; Nest on Cloud Functions.  
- Leave and missed-clock queues; scheduled daily/monthly Firestore recap.  
- Git starts 2021 (site still 2022). Fence is client-side.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Recap** | React admin (`apps/recap`), not a separate product |
| **Pengajuan absensi** | Missed/correction clock request (not the GPS punch) |
| **NIP login** | Username + bcrypt password; Nest issues Firebase custom token |
| **occupation pins** | `jabatan` Kepala Dinas / Sekretaris Dinas → four LatLngs; else arsip only |

---

*Generated from `~/code/absensi-pegawai` as of 17 August 2026. Git start year 2021 is the source of truth for the later lamacca.com repair.*
