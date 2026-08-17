# Presidential Election Situation Room (LSI) — Resume Project Brief

**Role:** On the **2018–2019 Pantau Relawan roster** (you + **two** engineers). GitLab lived on **one teammate’s account**; you no longer know which person or which GitLab. **Contribution split unknown.** Do not claim sole. Do not name Bimo / Nana / Nadella / Irfan / zakikii.  
**Client / product:** Situation-room dashboard for **LSI Denny JA** — **Pilpres 2019** (Jokowi–Ma’ruf vs Prabowo–Sandiaga). Lamacca `/work` **Presidential Election Situation Room Dashboard**, badge **2019**. Also listed on lamacca.co.id as **Dashboard Situation Room**.  
**Git start year:** **Unknown — no clone.** Site year **2019**. Do **not** invent a first-commit date for the later lamacca.com repair. Treat **2019** as the public badge only.  
**Period:** Built with the early Pantau Relawan team around **Pemilu 2019** (you: GitLab of an **2018** teammate). Exact start/stop without git: **do not invent months**.  
**Repos:** **None on this machine.** Not in `~/code/lingkaran-survei-indonesia`. **Not** `lamaccatech/apps/lingkaran-survei-indonesia/presidential-election` (that is a **2024 Nuxt stub** — see below).  
**Still up (dummy):** `https://presidential-election-20bd7.web.app` — Firebase Hosting SPA. **Mapbox token in the baked bundle is expired**; there is no source left here to rotate it and redeploy.

This document is a **source brief**, not a finished resume entry.

You remembered: no repo, teammate GitLab lost, dummy demo still hosted, Mapbox dead. That matches. The shipped title is **Dashboard Pemilihan Presiden** (PWA `lsi-client`). Data is **not** Firestore: the bundle calls **`http://lsi-api.pantaurelawan.com`** (plain HTTP). Hosting is Firebase; the API is Pantau Relawan’s domain.

**Not** on GitHub Selected Projects. **No MAU.** Numbers still painted on the dummy UI (population/tone, canvassing 186/2.414) are **fixtures**, not production stats. Do not paste Mapbox `pk.` tokens, login payloads, or admin CRUD URLs as a how-to.

---

## 1. What we can and cannot prove

| Claim | Evidence |
|---|---|
| Repo | **Gone.** Early Pantau Relawan GitLab; owner forgotten. |
| Git authors / % | **Unknown.** Same three-person 2018–19 roster as [pantau-relawan.md](pantau-relawan.md) — do not expand names. |
| Demo | Hosting **still serves** the 2019 SPA with dummy figures. Map tiles fail (expired key). Top 5 Newstrend empty (API likely dead). |
| Stack | Read from the **built** `app.*.js` / `chunk-vendors.*.js` + `index.html`, not from source. |
| 2024 monorepo app | `apps/lingkaran-survei-indonesia/presidential-election` is a **fullscreen Mapbox stub** (Nuxt 3, no using data). **Do not** brief that as this `/work` card. That stub is why the **e-Watch GitHub** line mentions Mapbox — see [e-watch-lsi.md](e-watch-lsi.md). |

House rule “git start year is source of truth for later site repair” **does not apply** until a clone exists. Leave the badge at **2019**.

---

## 2. Project overview

### 2.1 One-sentence pitch

HQ wall dashboard for Pilpres 2019: Indonesia choropleth plus live-ish **media sosial**, **media konvensional**, and **canvassing** layers so LSI could read province dynamics at a glance — Vue 2 on Firebase Hosting, Mapbox, Laravel-era API on `pantaurelawan.com`.

### 2.2 Lamacca card vs the demo

| Card | Shipped SPA |
|---|---|
| Centralised real-time viz of the national landscape | Vuex store: date windows, last/next update, live toggle per channel. “Real-time” was **polling/scheduled updates** from `lsi-api`, not Firestore listeners. |
| Social media sentiment | Medsos sentiment + Facebook / Twitter / Instagram engagement; per-candidate (Jokowi, Ma’ruf, Prabowo, Sandi). Top-issues **admin** pages. |
| Traditional media coverage | Tone pie: positif / negatif / netral. |
| Field survey results | **Progress canvassing** (count / target) + calendar. Not the 2022 e-Watch dropping/persuasi product. |
| Regional dynamics at a glance | Mapbox GL **province GeoJSON fill** (choropleth). **This is the map the expired key breaks.** |
| **2019** / Web | Matches ticket (Jokowi–Ma’ruf vs Prabowo–Sandiaga) and Vue 2.6-era PWA. **No git year.** |

### 2.3 Surfaces (from the bundle)

| Actor | Surface |
|---|---|
| **Analysts** | `/` and `/dashboard/` — map, sentiment, tone, canvassing, calendars, empty “Top 5 Newstrend” when API is down |
| **Ops / admin** | Login → `/dashboard/admin-list`, `admin-input`, `admin-edit/:id` — medsos **top issues** (keterangan, kesimpulan, kandidat, tanggal) |
| **Public? ** | Hosting is an open URL today with dummy data. Do not claim it was public vs LSI-only without git. |

Channels in **Pengaturan**: Media Sosial, Media Konvensional, Canvassing — each with date range and last/next update.

### 2.4 Same family as Pantau Relawan, not e-Watch

API host **`lsi-api.pantaurelawan.com`**. 2018–19 Pantau Relawan was Laravel + RN on a VPS ([pantau-relawan.md](pantau-relawan.md)). This dashboard is a **Vue situation room on that API + Mapbox**, not a second mobile app.

**e-Watch (2022)** is a different LSI product: Flutter field dropping/persuasi + Firebase. Do not merge.

---

## 3. Stack (from the hosted artefact)

| Layer | What the build shows |
|---|---|
| UI | Vue **2.6.10**, Vue CLI hashed chunks, Vuex, PWA (`lsi-client` / “dashboard pilpres”) |
| Map | Mapbox GL JS **0.46** (CSS from `api.tiles.mapbox.com/.../v0.46.0`). Province `geojson` source + fill layer. Custom Studio styles + `mapbox/dark-v9`. |
| Charts | Chartist (donut/pie/bar) |
| Time | moment.js |
| Hosting | Firebase Hosting project id in the hostname: **`presidential-election-20bd7`**. **Zero** firebase/firestore strings in `app.js`. |
| API | `http://lsi-api.pantaurelawan.com` — `/login`, `/medsos/issues-admin/*`. Plain **HTTP**. |

Mapbox access token is compiled into the JS. Rotating it needs a **rebuild**. You have no repo, so the demo map stays broken.

---

## 4. Do not confuse with the 2024 stub

`~/code/lamaccatech/apps/lingkaran-survei-indonesia/presidential-election` (from **Jul 2024**, with e-Watch’s Nuxt move): Nuxt UI Pro + `nuxt-mapbox`, empty fullscreen map, **no** election widgets, **no** `lsi-api`. Started as a possible revival; **unfinished**. Different Mapbox wiring. **This `/work` card is the 2019 SPA**, not that folder.

Do not paste tokens from either tree.

---

## 5. Contribution

**Unknown lines.** You were on the three-person 2018–19 team. The GitLab remote was a teammate’s. Do not say “I solely built LSI’s situation room” unless you later remember a split you can defend without git.

Safer interview frame: Lamacca / Pantau Relawan built LSI a **Pilpres 2019 situation room** (Vue + Mapbox + canvassing/media layers). You were **on that team**. Source is gone; a dummy Hosting site remains.

---

## 6. Suggested resume copy

### 6.1 Header

**Engineer, Presidential Election Situation Room (LSI Denny JA)** — ~2018–2019  
Pilpres 2019 HQ map: social/conventional media + canvassing. Early Pantau Relawan team. **No surviving clone.**

### 6.2 Short blurb

On the 2018–19 Pantau Relawan team that shipped LSI’s Vue 2 situation room for Pilpres 2019 (Mapbox choropleth, medsos/legacy-media/canvassing). GitLab was a teammate’s and is gone; a dummy Firebase Hosting demo still loads, map key expired.

### 6.3 Medium blurb

For the 2019 presidential election we built LSI Denny JA a situation-room dashboard: Vue 2 + Vuex PWA on Firebase Hosting, Mapbox GL province fills, Chartist for sentiment and media tone, canvassing progress, and an issues admin against `lsi-api.pantaurelawan.com`. Jokowi–Ma’ruf and Prabowo–Sandiaga are the tickets in the dummy UI. The GitLab repo sat on a teammate’s account from the early Pantau Relawan crew and we no longer have it — so there is no git-start year and no authorship %. A 2024 Nuxt Mapbox page in lamaccatech is an empty stub, not this product.

### 6.4 Bullets

- 2018–19 Pantau Relawan team (three people; **split unknown**) — LSI Pilpres situation room, not e-Watch.  
- Vue 2 + Mapbox choropleth + medsos / koran / canvassing layers; API on Pantau Relawan’s domain.  
- Dummy demo still at `presidential-election-20bd7.web.app`; Mapbox key expired, source gone.

### 6.5 Skills

Vue 2, Vuex, Mapbox GL JS, Chartist, Firebase Hosting, Vue CLI / webpack PWA

### 6.6 Say carefully

| Claim | Safer |
|---|---|
| Sole engineer | **Team of three.** GitLab not yours (or not retained). |
| Git start 2019 | **Unknown.** Badge 2019 only. |
| “Real-time Firestore” | Hosting is Firebase; **data was HTTP API**. |
| Dummy 46M / 8% canvassing | **Fixtures.** |
| “Mapbox on e-Watch” | **This 2019 SPA** and/or the **2024 stub** — e-Watch field maps are Google static. |
| 2024 `presidential-election` folder | **Empty Nuxt map.** Not the /work card. |
| Mapbox / login | **Do not paste.** No way to rotate the demo key without source. |

---

## 7. Suggested 3-bullet block

**Presidential Election Situation Room (LSI Denny JA)** — Team engineer *(~2018–2019)*  
Pilpres 2019 HQ dashboard. Repo lost.

- Vue 2 situation room: Mapbox provinces, social + conventional media, canvassing.  
- API on the Pantau Relawan domain; Firebase Hosting only.  
- Dummy demo still hosted; Mapbox token expired; **no git**.

---

## 8. Glossary

| Term | Meaning |
|---|---|
| **Situation room** | HQ wall / ops map for election week, not a volunteer mobile app |
| **Canvassing (this card)** | Progress widget on the 2019 dashboard — **not** 2022 e-Watch dropping |
| **`lsi-client`** | Vue CLI project name in the PWA noscript / apple-mobile-web-app-title |
| **`20bd7`** | Firebase Hosting project suffix still in the public URL |

---

*Generated from the public dummy at `presidential-election-20bd7.web.app` (index.html + hashed JS, 17 August 2026), Lamacca `/work` copy, and cross-checks against [pantau-relawan.md](pantau-relawan.md) / [e-watch-lsi.md](e-watch-lsi.md). No application Git clone exists. Do not treat 2019 as a verified git start year.*
