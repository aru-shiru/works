# e-Perdatin Jaya — Resume Project Brief

**Role:** Tech lead / reviewer — you opened Laravel and merged PRs. **Nana is the main driver** (heaviest author). You are **not** sole builder. **Bimo and Nadella are not in this clone.** Fourth human is **Irfan** (`irfanzafar.dev@gmail.com`).  
**Client / product:** **Perdatin Jaya** — Perhimpunan Dokter Spesialis Anestesiologi dan Terapi Intensif (IDI-aligned specialist society, Jaya chapter). Fly `APP_NAME` **Perdatin Jaya**. Lamacca `/work` badge **2024** (repair later).  
**Git start year (source of truth for lamacca.com):** **2023** — first commit **31 Mar 2023**.  
**Period:** **31 Mar 2023 – 21 May 2024** (peak **May 2023**). Irfan last **9 Jun 2023**. Your later: README/php **21 Jun 2024**; **`feat: aktivasi kembali aplikasi perdatin` 25 Nov 2025** (storage migrate + Fly).  
**Team:** **Nana Sukarna** (205 non-merge), **you** (134), **Irfan** (66). `lamaccatech` = merge-only (~99). **No Bimo. No Nadella.**  
**Repo:** `~/code/perdatin-jaya` (`lamaccatech/perdatin-jaya`)

This document is a **source brief**, not a finished resume entry.

You said **same team as KAHMI (Bimo, Nana, Nadella)** and asked to confirm a **lamaccatech move** like KAHMI. Git: **Nana + Irfan**, not the Kaltara/KAHMI four. **No move.**

**Not** on GitHub Selected Projects. Card is **Web App** only — this clone is Inertia Vue, **no Flutter**. **No MAU.** Dashboard `User::count()` is live DB, not a résumé metric. Do not paste `UserSeeder` `*@example.com` logins.

---

## 1. Move confirmation (`perdatin-jaya` → lamaccatech?)

**No. This product did not move into `~/code/lamaccatech`.** Same check as KAHMI (`(kahmi)` scopes + `apps/kahmi`):

| Check | Result |
|---|---|
| `git log --all --grep='(perdatin)'` on lamaccatech | **0** |
| `git log --all --grep='perdatin'` | **0** |
| `apps/*perdatin*` / `packages/*perdatin*` | **none** |
| Deploy tree | Stays on this repo: Fly app **`perdatin-jaya`**, `https://perdatin-jaya.fly.dev`, SQLite volume, S3 |

KAHMI moved dashboard/functions and left Flutter behind. Perdatin is a **single Laravel app** that never got a monorepo port. Last code here is the **2025 reactivation** (`MigrasiStorage` + Fly), mot a Nuxt rewrite.

---

## 2. Project overview

### 2.1 One-sentence pitch

Login-gated ops site for Perdatin Jaya: anggota (praktek locations, iuran, tabsol, KTA/QR), surat masuk/keluar + disposisi, keuangan pemasukan/pengeluaran, and permohonan PDFs (SIP baru/perpanjangan, rekomendasi STR, mutasi).

### 2.2 Lamacca card vs this clone

| Card | In `perdatin-jaya` |
|---|---|
| Member / mail / dues & tabsol / practice locations / STR / users | **Yes** (tabsol + iuran; SIP/STR/mutasi under permohonan; custom Role+Permission) |
| Personal dashboard | Home counts + own iuran-lunas flag |
| Web only | **Yes** |
| **2024** | Git **2023–2024** (reactivate **2025**) |

Roles (`config/constants.php`): superadmin, dewan-pembina, ketua, wakil-ketua, sekretaris, bendahara, ketua-bidang-I/II/III, anggota. Gates via `has_permission`.

No prod hostname besides Fly. SQLite on the VM — not Postgres.

---

## 3. Stack

Laravel **10** / PHP **8.1–8.2** / Breeze / Inertia **Vue 3** / Vite 4 / Tailwind 3 / Headless UI / Heroicons. Excel Maatwebsite, DomPDF, S3, qrcode.vue. **Not** the KAHMI Nuxt/Firebase stack. **Not** Banhub’s React Inertia.

Stock Laravel README (+ Sail note you added Jun 2024).

---

## 4. Contribution

Non-merge: **Nana 205**, **you 134**, **Irfan 66**. First-adds `app/`+`resources/`: Nana **142**, Irfan **88**, you **72**.

### 4.1 You

`chore: Initial commit` **31 Mar 2023**. Early `review: struktur data`. Many `review:*` (invoice, keuangan, PDF, KTA, surat, iuran). Policies, SIP-baru tahapan service, profile/KTA bugs. Merged most PRs. 2025: activate again + storage migration.

You **reviewed a lot**; you also **shipped** policies and SIP/PDF glue. Do not claim you barely coded — also do not claim you out-wrote Nana.

### 4.2 Nana

Breeze+Vue layout from day four. Heaviest through **21 May 2024**: anggota, persuratan, keuangan filters, search debounce. Primary product builder on UI/API in this tree.

### 4.3 Irfan

Models/migrations first week. KTA mockup, QR, SK STR/SIP PDFs, surat masuk/keluar + disposisi, permissions UI, keuangan. Last **9 Jun 2023**. Not Bimo/Nadella.

---

## 5. Suggested resume copy

### 5.1 Header

**Tech Lead, e-Perdatin Jaya** — Mar 2023 – May 2024  
Laravel 10 / Inertia Vue. Nana heaviest; Irfan on domain/PDF. Not the KAHMI four.

### 5.2 Short blurb

Led Nana and Irfan on Perdatin Jaya’s member/mail/finance/SIP-STR ops app; I opened Laravel, reviewed/merged, and owned policies plus SIP tahapan. Reactivated on Fly in 2025.

### 5.3 Medium blurb

From Mar 2023 we shipped Perdatin Jaya (anesthesiologist society) in Laravel 10 / Inertia Vue: anggota + praktek, iuran/tabsol, surat + disposisi, keuangan, SIP/STR/mutasi PDFs, role-permissions. Nana first-authored most of the app layer; Irfan the schema and SK/KTA PDFs (through Jun 2023). I reviewed heavily and still have 134 non-merge commits. **Not** Bimo/Nadella. **Not** moved into lamaccatech (unlike e-KAHMI). Badge **2024**; git starts **2023**. Fly `perdatin-jaya`. No member counts.

### 5.4 Bullets

- Tech lead; Nana volume, Irfan models/PDF; I review + policies.  
- Members, correspondence, tabsol/iuran, SIP/STR permohonan.  
- Git start **2023** (site still **2024**). Stayed in this repo.

### 5.5 Skills

Laravel 10, PHP 8.2, Inertia.js, Vue 3, Tailwind, Breeze, role-permission, DomPDF, Maatwebsite Excel, Fly.io, S3

### 5.6 Say carefully

| Claim | Safer |
|---|---|
| “Same team as KAHMI/Kaltara” | **Nana + Irfan.** No Bimo, no Nadella. |
| “I only reviewed” | **134** non-merge + initial commit + policies. Nana still **heavier**. |
| “Moved to lamaccatech” | **Did not.** |
| “2024 project” | Badge. **Git start 2023.** |
| Headcount / iuran totals | **Unknown.** Dashboard sums the DB. |
| Seeder `*@example.com` | **Do not paste.** |

---

## 6. Suggested 3-bullet block

**e-Perdatin Jaya** — Tech lead *(2023 – 2024)*  
Society ops: members, mail, dues, SIP/STR.

- Led Nana (heaviest) and Irfan (schema/PDF); I reviewed and owned policies.  
- Laravel 10 / Inertia Vue; Fly `perdatin-jaya`.  
- Not in the lamaccatech monorepo. Git starts 2023 (site still 2024).

---

## 7. Glossary

| Term | Meaning |
|---|---|
| **Perdatin** | Perhimpunan dokter spesialis anestesiologi & terapi intensif |
| **SIP** | Surat izin praktik (baru / perpanjangan) |
| **STR** | Surat tanda registrasi (rekomendasi PDF) |
| **Tabsol** | Member tabsol records (`Tabsol` model) — card “dues & tabsol”; do not expand unless you remember the expansion |
| **Disposisi** | Routing on surat masuk/keluar |

---

*Generated from `~/code/perdatin-jaya` plus a full grep of `~/code/lamaccatech` for perdatin as of 17 August 2026. Git start year 2023 is the source of truth for the later lamacca.com repair. No monorepo move.*
