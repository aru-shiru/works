# Media Online / Journaltime — Resume Project Brief

**Role:** Flutter + Cloud Functions (with one mid on dashboard / public web)  
**Product:** Lamacca **Media Online** — publishing stack for a news outlet (articles, video news, CMS, search, push). Live brand: **Journaltime.co**. Tagline *“Nalar dan Terpercaya”* / *“Menjaga Nalar, Menyampaikan Kebenaran”* (about page, August 2020).  
**Period:** Web + CMS + Functions **8 Jul 2020 – 30 Oct 2020** (main ship). Luthfi two small web/CMS fixes **5 Apr 2022**. Flutter **30 Jul – 12 Aug 2020** (12 commits, last message “version 1.0.1”; pubspec still `1.0.0+7`).  
**Team:** **Muhammad Dzul Arsyil** + **Mohammad Luthfi Fathur Rahman** (`mollfpr` / `luthfi.ufi14@gmail.com`) — same pairing as Wakafyuk. snyk-bot and `lamaccatech` merge PRs are not staff.  
**Repos:** `~/code/journaltime` (`lamaccatech/journaltime` — dashboard, Next `web`, Functions); `~/code/journaltime_mobile` (`lamaccatech/journaltime_mobile`)

This document is a **source brief**, not a finished resume entry.

Public listing: Lamacca [products](https://lamacca.com/products) **Media Online** (Journaltime.co). **Not** on `/work`. **Not** on GitHub Selected Projects. **No MAU / installs / traffic** in these trees — do not invent. Play Store package `co.journaltime.journaltime_app` is linked from the site; do not quote a download count unless you get it from Play Console.

Same split as Wakafyuk: **you owned Flutter**; **Luthfi heavier on the public Next site and CMS**; **Functions together** (you wrote most of the Firestore triggers).

---

## 1. Project overview

### 1.1 One-sentence pitch

Journaltime is an Indonesian digital newsroom: editors publish articles and JW Player video news from a Bootstrap CMS; readers get a Next.js site plus an Android Flutter app, with Algolia search, guest comments, share-to-web, and FCM “Berita Baru!” on new posts.

### 1.2 Why it exists

Lamacca product **Media Online**: a reusable publishing platform (CMS + web + Android + push + social), first shipped as **Journaltime.co**. Capabilities on the company card that these repos actually have: news CMS, video news, push, social share/embeds, Algolia search, category taxonomy, sharded view counts + GA (gtag). **Tags** are leftover template on `web-deprecated`, not a shipped taxonomy. **No ads SDK, no paywall, no reader login on mobile.**

### 1.3 Surfaces

| Actor | Surface |
|---|---|
| **Readers** | Next `web/` (`journaltime.co`); Flutter Android (`journaltime_app`) — Home / Kategori / Video News. Guest comments (name + email + body). No `firebase_auth` on mobile. |
| **Editors / admin** | CRA dashboard (Sofbox): News, Categories, Vid News, Vid Channels, JW Videos library, User List (roles `admin` / `editor`) |
| **System** | Firestore + Cloud Functions (slugs, sanitize-html, Algolia, FCM, daily export, top-views). Next `/api/*` proxies Firestore + YouTube/Twitter/Instagram widgets + a COVID stats endpoint the Flutter app also hits. |

Firestore: `news` (comments + `_counter_shards_` views), `vidNews`, `vidChannels`, `categories`, `users`, `stats/news`, `stats/vidNews`, `stats/{col}/topViews`. Algolia indexes `news` and `vidNews`.

Public categories (nav): Internasional, Nasional, Regional, Economy, Sports, Entertainment, Video News, Opinion, Citizen Journalism.

### 1.4 What is not this product

- Not a SaaS multi-tenant CMS — one Firebase project `journaltime-6c5eb`.
- `web-deprecated/` (Next 9 + StoryHub leftovers) is the discarded first public template; Dzul swapped to the current `web/` on **15 Jul 2020**.
- Sofbox leftover screens (charts, mail, mapbox) in the dashboard are **template**, not features you shipped.

---

## 2. Stack

### 2.1 `journaltime` — dashboard, public web, Functions

Not Nx. Four folders at the repo root.

| Surface | Stack |
|---|---|
| **Dashboard** | React **16.12** CRA, Redux 4 + thunk, react-router-dom 5, **Bootstrap 4 / Reactstrap**, Formik + Yup, Firebase JS 7, Algolia InstantSearch, **JW Platform** + `react-jw-player`, CKEditor 5 + Summernote |
| **Public web** | **Next 10**, React 17, Firebase 7, Algolia InstantSearch, **SWR 0.2**, Bootstrap 4, Formik, `react-jw-player`, react-share, gtag `UA-175172406-1`. Mix of `getStaticProps` / `getServerSideProps`. Hosting: `web/out` |
| **Functions** | Node **10**, TypeScript 3.8, firebase-functions 3.6, firebase-admin 8, algoliasearch 4, sanitize-html. Firestore triggers on news / vidNews / categories / users; HTTP callable `register` + Algolia resync; Pub/Sub daily Firestore export to GCS and 3-hourly top-views |
| **Dead** | `web-deprecated/` Next **9.4** + TypeScript + styled-components + Disqus |

Firebase Hosting targets: public `journaltime-6c5eb`, dashboard `dashboard-journaltime-6c5eb`. Canonical share URLs: `https://journaltime.co`.

### 2.2 `journaltime_mobile` — Flutter 1.x

| | |
|---|---|
| Identity | Package `journaltime_app`; Android `co.journaltime.journaltime_app`; iOS bundle `co.journaltime.journaltimeApp` (no `GoogleService-Info.plist` in this tree) |
| SDK | Dart `>=2.7 <3.0` — **pre-null-safety**. Flutter 1.x, Provider **4.3**, Firestore **0.13**, FCM **6.0**, Algolia Dart **0.1.7**, `video_player` + JW CDN, `flutter_widget_from_html` |
| Architecture | Flat `lib/pages` + `lib/services` `ChangeNotifier`s (not feature-sliced, not Riverpod) |
| **Absent** | Login, bookmarks, IAP, ads, maps |

FCM topic `news-post` matches Functions `NotificationManager` (`FLUTTER_NOTIFICATION_CLICK`). Share URLs match the Next routes `/news/{slug}` and `/vid-news/{slug}`.

### 2.3 Push / search / video / “analytics”

| Lamacca card | In code |
|---|---|
| Push notifications | FCM topic `news-post`, title `Berita Baru!` |
| Search | Algolia InstantSearch on web/dashboard; Dart `algolia` client on mobile (`news` index) |
| Video news | JW Player library in CMS; `react-jw-player` on web; Flutter `video_player` of JW MP4s |
| Social | react-share; footer Facebook / Instagram / Twitter / YouTube; Next API proxies for those widgets |
| Reader analytics | Sharded **view counters**, scheduled **topViews**, **gtag**. Not Mixpanel. Do not invent “dashboard of unique readers.” |

**No** AdSense/GPT, **no** Midtrans / subscriptions.

---

## 3. Contribution

Human commits on `journaltime` (`--all`, excluding snyk-bot 102 and lamaccatech merges 11): **Luthfi ~122** (113 + 9 as `mollfpr`), **you 76**. First commit is yours (8 Jul 2020). Last human: Luthfi, 5 Apr 2022 (mWeb menu + editors list). Your last commit in that repo: **30 Oct 2020**.

**Commits touching a path (human, approx.):**

| | dashboard | functions | web | Flutter repo |
|---|---|---|---|---|
| **You** | 21 | **20** | 42 | **12 / 12** (100% Dart) |
| **Luthfi** | **43** | 7 | **67** | 0 |

That matches what you remember, with one extra you should say: **Luthfi also owned most of the public Next site**, same pattern as Wakafyuk’s MUI web — not only the CMS.

### 3.1 You

- Scaffolded the Firebase project, news/vidNews/category triggers (slug, sanitize, Algolia, FCM, view counters), daily GCS export, 3-hourly top-views rollup.  
- Sofbox CRA dashboard: news / category / vidNews + rules (JW video library is Luthfi-early).  
- Public web: template swap off `web-deprecated`, author pages, COVID widget, Next 10, Algolia debounce, SSG revalidate. Some **`web/.next` artifacts** landed in your history — do not treat as product work.  
- **Entire Flutter client** in two weeks: Provider, infinite Firestore lists, Algolia search, FCM + view counters, guest comments, pull-to-refresh, share to `journaltime.co`.

### 3.2 Luthfi (`mollfpr`)

- **Dashboard volume:** JW Player env/service/videos pages, roles, user CRUD, InstantSearch tables, featured vid-news, login, stats widgets.  
- **Public web volume:** vid-news SSR, comments, related news, share/SEO, gtag, static newsroom pages, search UI, last 2022 mWeb menu fix.  
- **Functions:** smaller — user HTTP, vidNews fields, sanitize options. Do **not** say he did not touch the API; do **not** say the API was 50/50.

### 3.3 Outcomes you can defend

- A live news brand with **three** clients (CMS, Next, Flutter) on one Firestore, shipped in ~three months.  
- Same pairing as Wakafyuk: **you = Flutter + heavier Functions**; **Luthfi = dashboard + public web**.  
- Pre-null-safety Flutter 1.x + Provider — earlier than Wakafyuk mobile (Nov 2020). Brainplus (2019) still first Flutter; this is an early **reader/content** app.

---

## 4. Suggested resume copy

### 4.1 Header

**Full-Stack (Flutter + Functions), Journaltime.co / Lamacca Media Online** — Jul 2020 – Oct 2020  
News publishing: Flutter Android, Next.js reader site, React CMS, Firebase.

### 4.2 Short blurb

Built Journaltime.co with Mohammad Luthfi Fathur Rahman: I owned the Flutter news/video app and most Cloud Functions (Algolia, FCM, slugs, view stats); he owned the Next.js reader site and most of the Bootstrap CMS.

### 4.3 Medium blurb

Journaltime is Lamacca’s Media Online product — a news CMS plus reader web and Android. In summer 2020 I pair-built it with Luthfi (same split as Wakafyuk). I wrote the Flutter 1.x client (Provider, Firestore, Algolia, FCM, JW video, guest comments) and the bulk of Functions (sanitize, slugs, search sync, push, Firestore export, top-view rollups). Luthfi shipped JW Player in the CMS and most of the Next 10 public site. GitHub Selected Projects does not list this; Lamacca does. No MAU in the repos.

### 4.4 Bullets

- Pair-built Journaltime.co (Lamacca Media Online): Flutter Android + Firebase Functions (me, heavier) and Next.js + CRA CMS (Luthfi, heavier).  
- Flutter 1.x (pre-null-safety, Provider): categories, article HTML, JW video, Algolia search, guest comments, FCM `news-post`.  
- Functions: slug + sanitize-html, Algolia recorders, FCM “Berita Baru!”, sharded views, daily GCS export.  
- Dual reader surfaces share the same Firestore and `journaltime.co` URLs.

### 4.5 Skills

Flutter, Dart, Provider, Firebase (Firestore, Cloud Functions, FCM, Hosting, Auth on CMS), Next.js, React, Redux, Bootstrap, Algolia, JW Player, TypeScript

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I built Journaltime alone” | **Two people.** Flutter is sole; CMS and Next are Luthfi-heavy; Functions mostly you. |
| “Luthfi only did dashboard” | He also did **most of the public Next site** (and the 2022 mWeb fix). |
| “We split the API 50/50” | Together, **you ~20 function-path commits vs his ~7.** Triggers (FCM/Algolia/slug) are yours to claim. |
| “I owned JW Player” | **Luthfi** brought JW into the dashboard; you played JW URLs on Flutter/web. |
| “Tag taxonomy / ads / subscriptions” | **Categories**, not tags. **No ads SDK, no paywall.** |
| “Reader analytics platform” | View shards + topViews + gtag — not a BI product. |
| Play Store / MAU / unique visitors | **Unknown.** Cite Journaltime.co + package id, not numbers. |
| “Still my product in 2026” | Last **your** commit Oct 2020; last **any** human Apr 2022. Lamacca still lists it live. |
| Algolia keys in `lib/credentials.dart`, Firebase web config, `android/key.properties` | **Do not paste.** Treat as leaked client/keystore material, not a resume line. |

---

## 5. Suggested 3-bullet block

**Journaltime.co (Lamacca Media Online)** — Full-Stack with one mid *(Jul 2020 – Oct 2020)*  
News publishing for web and Android.

- Sole Flutter 1.x reader (Provider, Firestore, Algolia, FCM, JW video).  
- Most Cloud Functions: slugs, HTML sanitize, search sync, push, view stats, daily backup.  
- Pair with Luthfi, who owned the Next.js site and most of the Bootstrap/JW CMS.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Media Online** | Lamacca product name; this deployment is Journaltime.co |
| **Vid News** | JW-backed video articles (`vidNews` / `vidChannels`) |
| **Sofbox** | Purchased React admin kit; leftover unused screens |
| **mWeb** | Luthfi’s 2022 public-site menu fix |

---

*Generated from `~/code/journaltime` and `~/code/journaltime_mobile` plus lamacca.com/products as of 16 August 2026. Pairing attributed from git shortlog and path-touch counts; Luthfi = mollfpr. No store or traffic numbers in-repo.*
