# e-KAHMI — Resume Project Brief

**Role:** Tech lead — **not** “I only reviewed the dashboard.” You opened the repo, owned **all Flutter**, owned Functions, and still have **120** dashboard commits in `e-kahmi`. Bimo is the heaviest **dashboard** author. Then you **alone** ported dashboard + functions into lamaccatech and added a public registration app.  
**Client / product:** **e-KAHMI** for **Majelis Nasional Korps Alumni HMI (MN KAHMI)**. Android `com.lamacca.kahmi`. Firebase project / Hosting site **`e-kahmi`**. Lamacca `/work` badge **2023** (repair later).  
**Git start year (source of truth for lamacca.com):** **2024** — first commit **27 May 2024**.  
**Period:** Team build **27 May – 22 Jul 2024** (peak **Jun 2024**). Functions last in the original tree **2 Aug 2024**. Original dashboard last **1 Nov 2024**. Flutter still lands there (**21 Dec 2025**). Monorepo dashboard/functions **7 Jul 2024 – 18 Sep 2025**.  
**Team (original `e-kahmi`):** you, **Bimo Eka Saputra**, **Nana Sukarna**, **Nadella** (cameo: **5** commits, last **30 May 2024**). **lamaccatech `apps/kahmi`:** you only.  
**Repos:** `~/code/e-kahmi` (`lamaccatech/e-kahmi` — Flutter lives here); dashboard/functions/registration `~/code/lamaccatech/apps/kahmi` (+ `packages/@kahmi`)

This document is a **source brief**, not a finished resume entry.

You remembered Nuxt dashboard + Flutter mobile, same three, **mainly mobile + review**. Git: **sole mobile (87/87)** is true. “Review only” on web is **not**: you scaffolded Nuxt layers/login, and Bimo/Nana’s volume is dashboard while you still commit web. Nadella is a **cameo** (news list/detail, pagination, anggota detail).

**Not** on GitHub Selected Projects. **No MAU / anggota counts.** Emulator `seed/auth_export` is fixture. Do not paste Firebase web config, emulator passwords, or the hardcoded CASL email.

---

## 1. Move confirmation (`e-kahmi` → `lamaccatech/apps/kahmi`)

**Yes — dashboard and the three Cloud Function codebases were moved onto the same Firebase project. Flutter was not. A new public registration Nuxt app was added in the monorepo.** This is a **monorepo extraction / continued dashboard**, not an LSI-style Vue→Nuxt rewrite (original was already **Nuxt 3 + Nuxt UI Pro**).

Walked: full `e-kahmi` log; all `apps/kahmi` path commits; all **67** non-merge messages matching `(kahmi)` (monorepo convention `feat(kahmi): …`).

| Claim | Git |
|---|---|
| Same Firebase | Both `.firebaserc` → project **`e-kahmi`**. Hosting site **`e-kahmi`**, Storage `e-kahmi.appspot.com`, Functions region **asia-southeast2** |
| Dashboard moved? | **Init** `chore(kahmi): init dashboard` **7 Jul 2024** while the team was still building original (Bimo/Nana last **17 / 22 Jul 2024**). Real feature dump in monorepo **31 Oct – 3 Nov 2024** (login, home, berita, kegiatan, banner, anggota, pengurus, KTA, libraries). Original **dashboard last 1 Nov 2024** |
| Functions moved? | Original `functions/{kta,news,user}` last **2 Aug 2024**. Next day **`feat(kahmi): functions kta|news|user`** in monorepo. Same triggers (e.g. `kta/{uid}` → Puppeteer KTA). Later: config from `@lamaccatech-kahmi/*` |
| Flutter moved? | **No** `pubspec.yaml` under `apps/kahmi`. All 87 mobile commits stay on `e-kahmi` (last **21 Dec 2025**, kegiatan link) |
| Extra in monorepo | **`apps/kahmi/registration`** from **2 Nov 2024** — public “melaporkan diri”; not a separate app in the original tree |
| Who committed the move | **You only** on `apps/kahmi` (127 path commits). Zero Bimo/Nana/Nadella |

Original still has `dashboard/` + `functions/` as historical tree; **deploy source of truth after Nov 2024 is lamaccatech** (`firebase.json` there uses `functions-kta|news|user` + Nitro `dashboard/.output/server`). Flutter still calls `asia-southeast2-e-kahmi.cloudfunctions.net/server/api/…`.

Jul 7 init is a **placeholder**; the actual cutover is **late Oct / 1–3 Nov 2024**. Aug 3 is the functions cutover.

---

## 2. Project overview

### 2.1 One-sentence pitch

National KAHMI alumni ops: Flutter e-KTA + berita/kegiatan/pengurus; Nuxt admin (CASL by pusat / provinsi / kab-kota) verifies members; Functions render KTA PDFs; later a public Nuxt “laporkan diri” form.

### 2.2 Surfaces

| Actor | Surface |
|---|---|
| **Anggota** | Flutter (Riverpod, go_router): login, profile, QR, my KTA, berita, kegiatan, pengurus, in-app daftar / laporkan diri |
| **Calon (web)** | `apps/kahmi/registration` (Nov 2024) |
| **Admin pusat / provinsi / kab-kota** | Nuxt UI Pro: anggota (pending/rejected/verify), kepengurusan, kegiatan, banner, berita, notifikasi, log. CASL `admin_pusat` / `admin_provinsi` / `admin_kabupaten_kota` |
| **Triggers** | `functions-kta` PDF+QR; `functions-news`; `functions-user` (e.g. public foto-diri) |

Host in git: `https://e-kahmi.web.app` (storage-cors). Card year **2023**; git **2024**.

---

## 3. Stack

| Layer | Original `e-kahmi` | After move (`apps/kahmi`) |
|---|---|---|
| Dashboard | Nuxt **3.11**, UI Pro **1.2**, VueFire, CASL, Unovis, Quill, Puppeteer | Nuxt **3.17**, UI Pro **1.8**, TipTap, packages `@kahmi/*` |
| Mobile | Flutter SDK ≥3.3, Riverpod, go_router, Firebase — **stays here** | — |
| Functions | Node 20, kta / news / user | Same codebases, shared config/schema |
| New | — | Registration Nuxt, `scripts` (regenerate KTA), seed |

---

## 4. Contribution

Non-merge on `e-kahmi`: **Bimo 255**, **you 234**, **Nana 152**, **Nadella 5**. Split: mobile **you 87**; dashboard Bimo **247** / Nana **151** / you **120** / Nadella **5**; functions **you 10**.

### 4.1 You

Day one (`27 May 2024`): Initial commit, Flutter shell, Firebase, Nuxt 4-style domains/layers, login. **Sole Flutter** (KTA screen, QR, share berita, registration copy “laporkan diri”, BPKH mark on login, Dec 2025 kegiatan link). Functions. Monorepo: packages, dashboard feature-for-feature, registration, KTA scripts, Sep 2025 wilayah seed.

### 4.2 Bimo

Dashboard volume: CASL by tingkat, kepengurusan, anggota payloads, download/regenerate KTA. Last **17 Jul 2024**. Never on lamaccatech kahmi.

### 4.3 Nana

Dashboard + Nitro APIs: kegiatan, berita, anggota/register types, **generate KTA PDF**, pengurus APIs. Last **22 Jul 2024**. One functions commit.

### 4.4 Nadella

Week-one mockups only. Do not list as a KAHMI feature owner.

---

## 5. Suggested resume copy

### 5.1 Header

**Tech Lead, e-KAHMI (MN KAHMI)** — May 2024 – 2025  
Flutter anggota app; Nuxt admin. Then solo monorepo port + public registration.

### 5.2 Short blurb

Led Bimo and Nana (Nadella cameo) on MN KAHMI’s e-KTA platform; I owned Flutter and later moved dashboard/functions into lamaccatech and shipped the public laporkan-diri site.

### 5.3 Medium blurb

From May 2024 we shipped e-KAHMI on Firebase: Flutter for members (e-KTA, QR, berita/kegiatan), Nuxt UI Pro admin with CASL by pusat/provinsi/kab-kota, Puppeteer KTA PDFs. I opened the repo and wrote all 87 mobile commits; Bimo and Nana carried most of the original dashboard. In Aug 2024 I moved the three Functions into `lamaccatech/apps/kahmi`; at end of Oct I rebuilt the dashboard there (same `e-kahmi` project) and added a public registration app. Flutter stayed in `e-kahmi`. Lamacca badges **2023**; git starts **2024**. No anggota counts in-repo.

### 5.4 Bullets

- Tech lead; sole Flutter; Bimo/Nana on original Nuxt admin.  
- e-KTA PDF, wilayah CASL, berita/kegiatan/pengurus.  
- Dashboard + functions to lamaccatech; Flutter not moved.

### 5.5 Skills

Flutter, Riverpod, go_router, Nuxt 3, Nuxt UI Pro, VueFire, CASL, Firebase Functions v2, Puppeteer, Firestore, pnpm/Turbo monorepo

### 5.6 Say carefully

| Claim | Safer |
|---|---|
| “I only reviewed web” | **120** original dashboard + **all** monorepo kahmi. |
| “Four equal builders” | Nadella **5**. Equal-ish web: Bimo / Nana / you. Mobile: you. |
| “Rewrote Vue → Nuxt” | Original **already Nuxt**. Move = monorepo + registration. |
| “Moved the mobile app” | **Did not.** |
| Badge 2023 | **Git start 2024.** |
| Member counts | **Unknown.** |
| Super-ability email / seed passwords | **Do not paste.** |

---

## 6. Suggested 3-bullet block

**e-KAHMI (MN KAHMI)** — Tech lead *(2024 – 2025)*  
Alumni members, e-KTA, Nuxt ops.

- Led Bimo and Nana on the original Nuxt admin; I owned Flutter.  
- Functions + dashboard now in `lamaccatech/apps/kahmi` (same Firebase); public laporkan-diri added.  
- Git starts 2024 (site still 2023).

---

## 7. Glossary

| Term | Meaning |
|---|---|
| **KAHMI / MN** | Korps Alumni HMI / Majelis Nasional |
| **e-KTA** | Digital kartu tanda anggota (Functions + Flutter) |
| **Laporkan diri** | Registration copy (mobile + public Nuxt) |
| **`(kahmi)`** | Monorepo commit scope |

---

*Generated from `~/code/e-kahmi` and every `(kahmi)` / `apps/kahmi` commit on `~/code/lamaccatech` as of 17 August 2026. Git start year 2024 is the source of truth for the later lamacca.com repair.*
