# Muhammad Dzul Arsyil
**Senior Backend Engineer** | Platform and API

Jakarta, Indonesia · +62 821-9262-7926 · dzularsyil.muhammad@gmail.com
https://github.com/aru-shiru · https://www.linkedin.com/in/arushiru/ · lamacca.com

## Summary

Technical lead of **2–5 engineers** and hands-on backend. NestJS and Laravel in production; PostgreSQL after years on Firebase. Owned Partai Amanat Nasional’s move off Firestore (SuperTokens, Drizzle, Terraform on AWS and GCP, ~1.4M members imported) and SimplePol (Laravel Octane, RDS, ~47M-row cutover). Customer success and operators map the domain; I lead the squad or implement in TypeScript, PHP, and Go.

## Skills

**API:** NestJS, Laravel, Node.js, PHP, Go, Cloud Functions
**Data:** PostgreSQL, Drizzle, Eloquent, Redis, BullMQ, Elasticsearch, Meilisearch, Firestore
**Auth:** SuperTokens, Firebase Auth, custom tokens, RBAC
**Cloud:** AWS (RDS, S3, ECS), GCP (Cloud Run, Cloud SQL), Terraform, Docker, Firebase, Laravel Cloud, Fly.io, Cloudflare Workers
**Delivery:** OpenAPI, queues and workers, password-hash import, multi-region cache

## Experience

**Technical Lead and Senior Backend Engineer**
PT Lamacca Kreatif Solusi — Jakarta, Indonesia | 2018 – Present

**Technical lead of 2–5 engineers** on team backends (reviews, API split, architecture). Customer success and the client map the domain; I implement when there is no squad.

**e-Partai / simPAN — Partai Amanat Nasional | Sole engineer | 2021 – Present**
- Firebase era: Nest on Cloud Functions, Elasticsearch, custom-token auth, membership-card PDF pipeline.
- Rewrite: NestJS, PostgreSQL, Drizzle, SuperTokens, Redis (cache and BullMQ), Meilisearch, S3/GCS. Terraform for AWS and GCP so a second party is configuration, not a new Firestore project. Expo member app in the same TypeScript monorepo for shared types.
- Migrated simPAN and Pantau Relawan. Source table ~1.8M rows; ~400K were incomplete voter-roll imports (name and membership number only) and were excluded. ~1.4M members migrated, password hashes imported. RBAC so a national admin cannot page another province’s members.

**SimplePol — driving-license health checks | Sole engineer | 2026 – Present**
- Laravel Octane, PostgreSQL RDS (Jakarta), S3, Redis. Identity-first (national ID number), district-level access control, snapshot billing, certificate jobs, token API for Android.
- Go OCR proxy on Cloud Run. Migrated ~47M legacy rows. ~312K requests/day observed; ~93% cache hit with compute in Singapore and the database in Jakarta. Pest tests.

**ADVLive — Advansia | Tech lead (2 engineers) | Rewrite Oct 2025 – Present (Indonesia live since 2021)**
- Leading two engineers. Nest + PostgreSQL so Indonesia and Malaysia share one codebase with relational constraints. Go migrator for the Indonesia Firestore cutover.

**SDS — Sekaiichi | Sole engineer | 2024 – Present**
- Postgres procedures for attendance, cleaning, inventory, recruitment. Scheduled auto-checkout. Fly.io service so fingerprint clocks write the same attendance rows as the phone.

**SIMPEL — road equipment (Dishub) | Tech lead (2 engineers) | 2025 – Present**
- Led two engineers. Clone-per-dinas Laravel API + Expo on Fly.io for three transport agencies. White-label by git clone, not one shared database.

**Pantau Relawan | Tech lead (5 engineers) | 2018–2019; 2023 – Present**
- Led five engineers on the 2023 rewrite. I wrote the Cloud Functions base, custom auth claims, tenant isolation by candidate, Nest + Elasticsearch voter-roll lookup. 20+ candidate tenants. 2018–19: led two; Laravel on a VPS.

**Additional projects:** Led two engineers on e-Hibah (Polri grants; v1 on Fly.io, later sole Livewire rewrite). Led three on North Kalimantan Laravel systems. Geo-attendance API (Nest on Cloud Functions). Donation payment APIs (Midtrans, LinkAja).

**Co-Founder**
Belliki — Indonesia | 2016 – 2018

Node.js API for an apparel marketplace.

## Education

**Associate Degree, Information Processing** — Seifu Institute of Technology, Osaka
Graduation project recognised as outstanding by a Microsoft Japan representative.
