# Congress Registration & Attendance (PAN) — Resume Project Brief

**Role:** Sole full-stack. No Bimo / Nana / Nadella.  
**Client / product:** **Kongres VI PAN** (branded **Kon6res PAN**). Live host in git: `kon6res.simpan.app` (also `kon6res-pan.web.app`). Lamacca `/work` badge **2024** (matches git).  
**Git start year (source of truth for lamacca.com):** **2024** — first commit **20 Jul 2024** (`feat(simpan): init aplikasi kongres PAN`).  
**Period:** Event build **20 Jul – 6 Sep 2024** (peak **late Aug 2024**, around Kongres VI). Later lands are Turbo/React 19/Biome **Jul 2025**, not a second event.  
**Team:** you only. `apps/simpan` path commits **220 / 220** Muhammad Dzul Arsyil. `(simpan)` scoped messages **142**, same author.  
**Repos:** `~/code/lamaccatech/apps/simpan` (`kongres`, `functions-congress`, congress scripts) + `packages/@simpan/{config,schema,data-access,congress-card}`. Original `~/code/simpan` has **no** kongres code. Rewrite import lives in `~/code/e-partai` (`apps/simpan-migration` → event id **1**). See [e-partai.md](e-partai.md) for membership, not this card.

This document is a **source brief**, not a finished resume entry.

You remembered: congress **inside simPAN**, same Firestore, **dedicated collections**; a **script** pull from simPAN at seed, maybe not realtime; the attendee list **only had nomor KTA**; `apps/simpan` “mostly congress”; moved to e-partai **before** the rest of simPAN landed in the monorepo; **solo**. Git: collections + same project + solo + **KTA-only CSV** are true. NIK on a peserta row is **filled from the simPAN `users` hit** (seed and admin create/update), not from the party list. Folder is **not** only congress (`admin/` is a Next simPAN shell). Timeline is the **other way**: kongres was **born in lamaccatech** (Jul 2024); e-partai import is **Aug 2025+**.

**Not** on GitHub Selected Projects. **No MAU.** Migrator comments (~2,225 peserta / ~2,502 kehadiran) are **batch-size notes**, not a public attendance claim. Do not paste congress admin passwords, scanner-device passwords, or Firebase service-account JSON.

---

## 1. Placement vs simPAN / e-Partai

**Same Firebase project as simPAN (`simpan-3619f`). Dedicated Kongres collections. Not a second product in `~/code/simpan`.**

| Claim | Git |
|---|---|
| Inside simPAN | Yes: Hosting target `kon6res` on the simPAN Firebase app. Copy: “Powered by simPAN.” |
| Same Firestore | Yes. `packages/@simpan/config`: `peserta_kongres`, `agenda_kongres`, `kehadiran_agenda_kongres`, `scanner_peserta_kongres` next to `users`, KTA, TPS, etc. |
| Original Nx `simpan` | **No** kongres strings. Event was written in the monorepo. |
| e-partai | **Later** import of those four collections as **event 1**. Dashboard config still has leftover `/kongres-2024/*` paths; **no** matching pages. Do **not** pitch this card as the 2025 rewrite. |

You said you migrated to e-partai **before** putting simPAN in the large monorepo. Dates: kongres + `apps/simpan` **20 Jul 2024**; e-partai **Aug 2025**. Congress never sat in `~/code/simpan`.

`apps/simpan` after the event also got a Next **admin** rename (**10 Aug 2024**) and split Functions from simPAN-v2 (kepengurusan, e-saksi login, …). That is the Firebase **refresh**, incomplete (`admin` home is still a stub). The **finished** surface in this folder is Kongres.

---

## 2. Project overview

### 2.1 One-sentence pitch

Ops console + door scanners for **Kongres VI PAN**: CSV/admin peserta on Firestore, QR cocoards, session check-in, plenary counts — same Firebase as simPAN, own collections.

### 2.2 Lamacca card vs this tree

| Card | In git |
|---|---|
| Online registration | **Admin + scripts**, not a public form. Create/update peserta is `adminMiddleware`. Seed CSV: wilayah, jabatan (K/S), nama, gender, phone, **nomor KTA only** — no NIK column. |
| Approval | **No** pending-queue. “Empty” list is **unnamed quota slots** (`nama_lengkap` blank / `MEDIA` / `PENDAMPING` / `INTERNAL`). |
| QR / badge | Handlebars cocoards (`@simpan/congress-card`): QR → `kon6res.simpan.app/selamat-datang?peserta=…`. JPEG batch via `generate-co-cards`. |
| Check-in | Device JWT + `qr-scanner` in the **browser**. Category must be in `kategori_yang_diizinkan`. Writes `kehadiran_agenda_kongres`. |
| Dashboard | Next admin: peserta CRUD, agenda, **summary kehadiran** (registrasi, pembukaan, pleno 1–6, penutupan). |
| **Mobile app** scanner | **Not** Flutter / APK. Same Next app, `/scan`, phone camera. Card overclaims a native app. |
| **2024** | Matches git start. |

Agenda seed dates the congress program around **23 Aug 2024** (pembukaan Kongres VI, etc.). Do not recite the full run-of-show.

### 2.3 Surfaces

| Actor | Surface |
|---|---|
| **Peserta** | Printed cocard QR → welcome page (name, today’s agenda). No login. |
| **Panitia / ops** | `/login` → peserta, agenda, summaries. |
| **Door / hall scanners** | Device accounts (`scanner_peserta_kongres`): pick a session, scan QR, POST check-in. |
| **You (ops scripts)** | Go/TS: CSV → lookup `users` by **nomor_kta**, write `peserta_kongres`; cocoards; attendance export (**6 Sep 2024**). |

Host in git: `https://kon6res.simpan.app`, `https://kon6res-pan.web.app` (`functions-congress` CORS).

#### Attendee pull from simPAN (KTA on the list; NIK from a hit)

The party list is **KTA-keyed**. Semicolon CSV: province, wilayah, K/S, name, gender, phone, `nomor_kta`. Converter matches `users.nomor_kta`. On a hit it copies **nik** + foto diri / KTA / KTP. Miss: name/jabatan/phone/KTA still written, **no NIK**. Confirmed with you.

| Path | When | Match |
|---|---|---|
| Go convert/insert (**27 Jul 2024**, fixes **10 Aug**) | Bulk seed | CSV **nomor_kta only** → `users`. NIK is **output**, not input. |
| `storeCongressParticipant` / `updateCongressParticipant` | Admin create or when NIK/KTA **changes** | `users` where `nik` **or** `nomor_kta`. Copies photos. Comment on update: “Load data anggota kta jika data nik atau nomor kta diubah.” |

**Not** a membership trigger. Editing a simPAN user does not update kongres. No realtime listener. Do not say the seed file contained NIKs.

Categories: `PESERTA`, `PANITIA`, `UNDANGAN`, `PENINJAU`, `MEDIA`, `PENDAMPING`, `INTERNAL`.

---

## 3. Stack

| Layer | Choice |
|---|---|
| App | Next **14.2.5** / React 18 at init (**20 Jul 2024**); later Next **15.3** / React **19** (Jul 2025) |
| Scanner | `qr-scanner` on `<video>` (not a native module) |
| API | Firebase Functions v2 Express `functions-congress` (`asia-southeast2` lineage with simPAN) |
| Data | Firestore collections above; Auth for admin + scanner devices |
| Cards | Handlebars + `uqr` SVG → JPEG (`congress-card`) |
| Seed | Go CSV/Firestore, then TS firewalk / cocoards / attendance dump |

Do not treat `apps/simpan/admin` as this product.

---

## 4. Contribution

Solo. **220** commits under `apps/simpan`; kongres UI + functions + `congress-card` **155** path commits (includes 2025 chores).

### 4.1 You

Day one **20 Jul 2024**: kongres Next app. **27–28 Jul**: peserta seed + list UI. **9 Aug**: `functions-congress`, cocoards. **11 Aug**: scanner route. **22–24 Aug**: category-styled cards, unique counters, plenary summaries, scanner demo. **6 Sep**: export kehadiran.

Sep 2024+ `(simpan)` after that is **not** this card (kepengurusan Functions, e-saksi login). Keep them on [e-partai.md](e-partai.md).

### 4.2 Nobody else

Zero other authors on `apps/simpan`.

---

## 5. Suggested resume copy

### 5.1 Header

**Sole Full-Stack Engineer, Congress Registration & Attendance (PAN)** — Jul – Sep 2024  
Kongres VI ops on simPAN’s Firebase: peserta, QR cocoards, door scan, plenary dashboard.

### 5.2 Short blurb

Built Kon6res PAN as a Firestore event on the existing simPAN project (dedicated collections). Admin + CSV seed (list was **nomor KTA only**; NIK/photos copied from a `users` hit), Handlebars cocoards, phone-browser QR check-in, session summaries. Solo.

### 5.3 Medium blurb

For Kongres VI PAN (Jul–Sep 2024) I added a Next.js ops/scanner app on the simPAN Firebase project: `peserta_kongres` / agenda / kehadiran / scanner devices, Functions v2 for CRUD and check-in, QR cocoards that open a welcome page. The attendee CSV only had **nomor KTA**; the loader filled NIK and photos from simPAN `users` when KTA matched. Admin create/update can also match on NIK or KTA — not a live sync. Scanner is `qr-scanner` in the same Next app (`kon6res.simpan.app/scan`), not a Flutter APK. e-Partai later imported the four collections as event 1; this `/work` card is the 2024 Firestore event, not the rewrite.

### 5.4 Bullets

- Solo; same Firestore as simPAN, four kongres collections.  
- Seed list was **KTA only**; NIK/photos from a simPAN `users` hit (admin form can also match NIK). No membership trigger.  
- Cocoards (QR) + browser camera check-in + plenary summary.  
- Later Postgres import in e-partai — do not merge the stories.

### 5.5 Skills

Next.js, React, Firebase Functions v2, Firestore, Firebase Auth, Handlebars, `qr-scanner`, Go/TS seed scripts

### 5.6 Say carefully

| Claim | Safer |
|---|---|
| “Public online registration / approval” | Admin + CSV. Empty slots ≠ approval inbox. |
| “Mobile app scanner” | Phone **browser** on the Next app. |
| “Realtime NIK sync with simPAN” | List was **KTA**. NIK copied on seed hit / form save only. |
| “Seed file had NIKs” | **KTA column only.** |
| “I moved kongres from `simpan` into lamaccatech” | It **started** in lamaccatech. |
| “e-partai before the monorepo” | Monorepo **2024**, rewrite **2025**. |
| “`apps/simpan` is only congress” | Kongres is complete; `admin/` is a stub refresh. |
| Migrator 2,225 / 2,502 | **Import comments**, not MAU. |
| Seed logins / SA JSON | **Do not paste.** |

---

## 6. Suggested 3-bullet block

**Congress Registration & Attendance (PAN)** — Sole full-stack *(2024)*  
Kongres VI on simPAN Firestore.

- Dedicated collections on the existing simPAN Firebase project; Next ops + Functions v2.  
- CSV peserta keyed by **KTA** (NIK from simPAN if found); QR cocoards; browser door-scan; plenary counts.  
- Not a native scanner app; not the 2025 e-Partai rewrite.

---

## 7. Glossary

| Term | Meaning |
|---|---|
| **Kongres VI / Kon6res** | 6th PAN congress (UI spelling). |
| **Cocard** | Printed name badge with QR. |
| **Empty peserta** | Unnamed quota rows, not an approval queue. |
| **`(simpan)`** | Monorepo scope — kongres **and** later Firebase refresh. |

---

*Generated from `~/code/lamaccatech` (`apps/simpan`, `packages/@simpan`, `(simpan)` history) and `e-partai/apps/simpan-migration` as of 17 August 2026. Git start year 2024 is the source of truth for the later lamacca.com repair.*
