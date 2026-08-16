# Works

Personal source briefs for projects I have shipped. Each project is one markdown file. These are resume-source documents, not finished resume entries.

Shorter public cards live on [github.com/aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru) (Selected Projects). Names there do not always match these files.

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
