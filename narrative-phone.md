# Narrative Phone — Resume Project Brief

**Role:** Tech Lead (then de-facto **sole native** after both platform engineers left)  
**Client / product:** **ナラティブフォン** (Narrative Phone) for **xCareField Co., Ltd.** — video calling that sits on [ナラティブブック](https://www.narrativebook.jp); landing [phone-landing.narrativebook.jp](https://phone-landing.narrativebook.jp)  
**Period (your delivery):** August 2023 – September 2024. **First iteration** (full basic feature set) is the scope you own. The product **still runs** in 2026; you were not the long-term maintainer after that cut.  
**Team:** You as tech lead + **two native engineers** — **Adji** (iOS), **wiliarko** (Android). Git only; no surnames on file. Client **O'hashi Ryuzi** (`jp.co.xcf.narrativephone`) made the **initial commit** (3 Aug 2023) on both repos — scaffolding, not Lamacca engineering.  
**Repos:** `~/code/narrativephone-ios` (`lamaccatech/narrativephone-ios`); `~/code/narrativephone-android` (`lamaccatech/narrativephone-android`)

This document is a **source brief**, not a finished resume entry.

Public GitHub listing: **Narrative Phone — Healthcare Video Calling App** on [aru-shiru/aru-shiru](https://github.com/aru-shiru/aru-shiru). Lamacca `/work` card: **2023 — 2024**, xCareField. **No MAU** on either — do not invent. Landing (published 2 Mar 2026) is the live marketing page, **not** a changelog of *your* first iteration.

**Before AI assistants** (this work is 2023–2024). You learned CallKit, PushKit, Zoom MobileRTC custom meeting UI, and enough Kotlin/Swift to own both trees after the engineers resigned. Do not thread Claude/Cursor into this story.

---

## 1. Project overview

### 1.1 One-sentence pitch

Narrative Phone is a **phone-style Zoom video app** for care (family, clinicians, educators, government support): contacts come from ナラティブブック, incoming calls use **CallKit / FCM**, and in-call video is a **custom MobileRTC UI**, not Zoom’s default meeting chrome — against **xCareField’s Rails API**, not an API you wrote.

### 1.2 Why it exists

ナラティブブック is the care “story” / record platform. Field people still needed **face-to-face talk** (online consult, family conference, three-way school meeting) without leaving that identity system. The app: same account, imported address book, Zoom underneath, native accept/decline.

Requires a **ナラティブブック account** (and a **Zoom account**) per the 2026 FAQ. App itself is marketed as free.

### 1.3 What you shipped vs what continued

You were hired / scoped to **deliver the first full feature pass**: auth (webview + device), contacts + favorites + import from Narrative Book, start/receive call, in-call add participant, call history, switch account / role, Zoom reconnect, settings. After that the **client continued the product** (landing 2026, QR still “coming soon”). Your last landings:

| Tree | Last your commit | Notes |
|---|---|---|
| iOS | **26 Mar 2024** | Adji already gone (last **13 Nov 2023**) |
| Android | **30 Sep 2024** | Zoom SDK bump to **v5.17.11**; wiliarko last **24 Mar 2024** |

Do not claim store metrics, 8-way calls (FAQ 2026), or QR unless you later confirm they went in **after** these trees.

### 1.4 Who uses it (marketing surfaces)

| Actor | What they do |
|---|---|
| Care / family / admin / education users | Call from Narrative Book contacts; camera/mic off defaults |
| Facility admins | Config (landing); not in these clients |
| You / Lamacca | Native apps only in these two repos |

### 1.5 Features in **these** trees (first iteration)

- Login / device registration via **webview** to `phone.narrativebook.jp` (prod) / `phone-sub` (staging)  
- **Contact list**, groups, favorites, **import** from Narrative Book address book  
- **Outgoing + incoming** Zoom meetings; **add attendee** mid-call  
- **Call history**  
- **Switch account** (landing: 職種切替)  
- iOS: **CallKit + PushKit**  
- Android: FCM + incoming-call activities / notification services  
- Custom **Zoom meeting UI** (UIKit `CustomUiViewZoom` / `MyMeetingActivity` XML)  
- **No QR** in either repo (GitHub card overclaims; landing says coming soon)

---

## 2. Stack

Backend is **client Rails** (`phone.narrativebook.jp`, bundle `jp.co.xcf.narrativephone`). You did **not** own that API.

| Surface | Stack in these repos |
|---|---|
| **iOS** | **SwiftUI** shell (`NarrativePhoneApp`, lists, settings) + **UIKit** Zoom custom UI + **CallKit / PushKit**. Zoom **MobileRTC**. JWT helpers (Swift-JWT / CryptoSwift SPM). ~111 Swift files |
| **Android** | Kotlin, **XML Activities** (call/incoming/create) **and** some **Compose** list screens (`MainScreenView`, contacts). Zoom **`mobilertc`** module. Retrofit, FCM, coroutines. `minSdk 27`. Last bump Zoom **5.17.11** |
| **Auth / data** | App tokens + Zoom OAuth reconnect webview; contacts via REST |
| **Not in tree** | Ruby on Rails (xCareField). Do not put Rails on *your* skills line except “integrated with” |

GitHub’s “Kotlin/XML” understates **Compose** on home/contacts. GitHub’s “hands-on on iOS” is true **and insufficient**: you committed **both** platforms from week one, then **only you** after resignations.

**Secrets:** Android `app/build.gradle.kts` embeds Zoom client secret and keystore passwords. **Never** paste into GitHub/resume. Interview: first-iteration JWT/secrets on device is a **lesson**, not a brag.

---

## 3. Contribution

### 3.1 Git confirms the story (two engineers, then you)

| | iOS | Android |
|---|---|---|
| Junior / platform engineer | **Adji** — 24 Aug – **13 Nov 2023**, 54 commits | **wiliarko** — 25 Aug 2023 – **24 Mar 2024**, 114 commits |
| You (throughout) | 10 Aug 2023 – 26 Mar 2024, **230** commits | 14 Aug 2023 – 30 Sep 2024, **160** commits |
| Solo after they left | **Dec 2023 – Mar 2024** (iOS) | roughly **Feb/May–Sep 2024** (Android; wiliarko 1 commit Mar 2024) |

**Added lines** (excluding Zoom SDK `mobilertc`/`commonlib`): iOS you **~47%** / Adji **~53%**; Android you **~24%** / wiliarko **~76%**. **Do not** lead a resume with 24%. You were **lead** (reviews, both-platform glue, custom Zoom on iOS per GitHub, then **both phones after they quit**). Raw volume on Android XML/call UI is wiliarko’s; you still shipped the first iteration **without them**.

Aug–Oct 2023 both repos are busy with all three of you. Adji vanishes mid-November. You carry iOS through history, CallKit, custom Zoom, polish. wiliarko thins out then stops. You keep Android (including the Sep 2024 Zoom upgrade).

**No AI.** Interview color: you had to **learn** the second platform’s meeting SDK and CallKit/FCM the hard way instead of handing tickets back to a specialist.

### 3.2 Correct the GitHub card

| Card | Safer |
|---|---|
| Led two native devs; hands-on **iOS** | True for **phase 1**. Phase 2: **both apps, you alone**. Also hands-on Android from August 2023, not reviews-only |
| QR contact exchange | **Not in these repos.** Landing: coming soon |
| Custom Zoom UIKit | True (`CustomUiViewZoom`, later callers). Android equivalent is **`MyMeetingActivity`** (Zoom Video View managers) |
| Rails | **Client API**. Integrate, don’t claim you wrote it |
| SwiftUI + UIKit | True |
| Kotlin/XML | **XML + Compose** |

### 3.3 Outcomes you can defend

- First native pair of store-bound apps for xCareField’s Zoom + Narrative Book combo.  
- Custom in-call UI instead of stock Zoom (healthcare look).  
- CallKit/PushKit (iOS) and FCM incoming call (Android).  
- Finished the **v1 feature set after the team dissolved**, pre-AI, as tech lead who could write both Swift and Kotlin.

---

## 4. Suggested resume copy

### 4.1 Header

**Tech Lead, Narrative Phone (xCareField / ナラティブブック)** — Aug 2023 – 2024  
Native iOS + Android Zoom calling for Japanese care workflows. Led one iOS and one Android engineer; owned **both** codebases after they resigned. First full feature iteration; product still live.

### 4.2 Short blurb

Tech lead for Narrative Phone: SwiftUI + UIKit iOS and Kotlin Android apps with custom Zoom MobileRTC meeting UI, CallKit/FCM incoming calls, and Narrative Book contact sync against the client’s Rails API. When both native engineers left, I finished and maintained both platforms myself.

### 4.3 Medium blurb

xCareField needed a phone-like Zoom client bound to ナラティブブック identities (patients, family, clinicians). I led two native developers (Adji / iOS, wiliarko / Android) and stayed hands-on—especially custom Zoom UIKit and later the Android meeting activity. After they resigned (iOS Nov 2023, Android by Mar 2024) I was the only engineer on both repos through first-iteration delivery (iOS to Mar 2024, Android including a Zoom 5.17 upgrade Sep 2024). No AI tooling; Zoom Video SDK, CallKit, and Kotlin were learned in production. Rails stayed with the client.

### 4.4 Bullets

- Tech lead for **Narrative Phone** (xCareField): dual native apps, Zoom Video SDK, Narrative Book contacts — first complete iteration.  
- Led **Adji** (Swift) and **wiliarko** (Kotlin); code review **and** implementation on **both** platforms from day one.  
- When both resigned, **sole engineer** on iOS and Android to close v1 (CallKit/PushKit, FCM incoming call, in-call add participant, history, account switch).  
- Replaced default Zoom chrome with a **custom MobileRTC meeting UI** (UIKit on iOS; `MyMeetingActivity` on Android).  
- Integrated **client Rails** at `phone.narrativebook.jp` — webview auth, Zoom OAuth reconnect, address-book import. **Did not** write that backend.  
- Pre-LLM era: learned production iOS/Android calling rather than routing all tickets to specialists.

### 4.5 Skills

Swift, SwiftUI, UIKit, CallKit, PushKit, Kotlin, Android XML + Jetpack Compose, Zoom Video / MobileRTC SDK, Firebase Cloud Messaging, Retrofit, REST, JWT (Zoom), xCareField Rails API integration *(not the Rails app)*

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I built Narrative Book” | **Phone apps + Zoom.** Book is xCareField. |
| “I wrote the Rails API” | **Consumed it.** |
| QR sharing | **Not in these trees / landing: coming soon.** Strip from GitHub card when you re-rank. |
| “I still own the product” | **First iteration.** Last local git Sep 2024; landing still updated 2026. |
| “Kotlin/XML only” | **Compose + XML.** |
| 8-person calls | **2026 FAQ**, not proven in 2023–24 git. |
| User counts | None. Don’t invent. |
| Zoom/signing secrets in Gradle | **Do not publish.** Rotate if those files ever leaked. Interview: tokens belong on the **server**. |
| “AI-assisted” | **No.** Opposite talking point. |
| Line % as “I wrote 24% of Android” | Lead + takeover. Juniors wrote much of the first XML/call volume. |

---

## 5. Suggested 3-bullet block

**Narrative Phone (xCareField)** — Tech Lead *(Aug 2023 – 2024)*  
Native Zoom calling app for ナラティブブック (Japan care / family / clinical talk). First full iteration; product still live.

- Led two native engineers (iOS + Android); stayed hands-on on **both** repos, then **sole** native after they resigned.  
- Custom Zoom MobileRTC meeting UI (UIKit + Android meeting activity) instead of the default Zoom UI; CallKit/FCM incoming calls.  
- Wired client Rails (auth, contacts, history) — no backend ownership.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **ナラティブフォン** | This app |
| **ナラティブブック** | xCareField’s care record product; **required** account |
| **xCareField** | Japanese client (`jp.co.xcf…`) |
| **MobileRTC** | Zoom’s native meeting SDK |
| **First iteration** | Your Lamacca delivery; not all later landing claims |

---

*Generated from `narrativephone-ios` / `narrativephone-android` git (Aug 2023–Sep 2024), GitHub Selected Projects, lamacca.com/work, and phone-landing.narrativebook.jp as of 16 August 2026. QR absent from these trees. Do not copy secrets from Gradle.*
