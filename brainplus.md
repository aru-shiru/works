# Brainplus — Resume Project Brief

**Role:** Sole Full-Stack Engineer  
**Product:** **Brainplus** — “Identify, Explore and Unleash Your Potential.” Lamacca product #03: edtech for Indonesian high-school (and college) students — psychometric / aptitude tests, major & campus catalog, “brainpath,” community, IAP.  
**Period:** August 2019 – June 2020. Mobile first commit **30 Aug 2019**; dashboard **20 Nov 2019**. Last mobile **26 May 2020** (release candidate); last dashboard **25 Jun 2020** (aptitude recommendation reference).  
**Team:** Sole author on both trees (149 mobile + 115–116 dashboard/functions commits, all Muhammad Dzul Arsyil).  
**Repos:** `~/code/brainplus_mobile` (`lamaccatech/brainplus_mobile`); `~/code/brainplus` (`lamaccatech/brainplus` — React admin **and** Cloud Functions)

This is the project **you learned Flutter on**, and where you first applied GoF-style **Factory + Strategy** to a family of tests. Later Flutter work (Wakafyuk 2020, Pantau Relawan, e-Watch, WBN) sits on top of this.

Public listing: Lamacca [products](https://lamacca.com/products) **Brainplus**. **Not** on GitHub Selected Projects. **No MAU** — do not invent. Product copy mentions “predictive modelling” — **these repos are rule-based scorers** (MBTI pairs, Holland/RIASEC, aptitude keys), not a trained ML model.

---

## 1. Project overview

### 1.1 One-sentence pitch

Brainplus is a student career-exploration app: take MBTI / RIASEC / Big Five / Raven / aptitude batteries in Flutter, get a **brainpath** and major/campus suggestions from Firestore + Functions, while admins edit questions, Holland codes, SMA/SMK/PT, scholarships, and quotes in a Bootstrap React console.

### 1.2 Why it exists

High-schoolers needed a phone flow for interest/aptitude tests plus a university/major encyclopedia (Brainpedia), not a PDF booklet. Scoring had to live **off the device** (Cloud Functions + Jest) because every test method has a different algorithm. The dashboard is the CMS for that catalog.

### 1.3 Who uses it

| Actor | Surface |
|---|---|
| **Students** | Flutter: Google/Facebook login, profile builder (SMA/SMK/PT), tests, results, brainpath, scholarships/colleges/majors search (**Algolia**), community, lifeplan, mentor placeholder, **in-app purchase** |
| **Admins** | React dashboard: tests + question banks, high schools, colleges, majors, quotes, events, Holland-code / RIASEC / aptitude reference |

### 1.4 Test battery (wired in factories)

MBTI, Learning Styles (`ls`), RIASEC, Big Five, Raven, and aptitude modules: penalaran visual/numerik/urutan, analisa verbal, pengenalan spasial, tiga dimensi, sistemasi, kosakata, figural angka. (DISC / RMIB widgets exist on disk; **not** in `TestProviderFactory` — don’t list them as shipped.)

---

## 2. Stack

| Surface | Stack |
|---|---|
| **Mobile** | **First Flutter app** — Dart `>=2.5 <3.0` (**pre-null-safety**), **Provider 3**, Firestore, Auth, Crashlytics, Functions, Storage, Algolia client, `in_app_purchase`, Flare, fl_chart. Version **1.0.0+43**, 219 Dart files |
| **Dashboard** | React 16 CRA, **Reactstrap / Bootstrap**, Redux + **redux-saga**, Formik, Algolia InstantSearch, Chart.js |
| **API / scoring** | Firebase **Cloud Functions** (TS, Node 10), **Jest** per-test validator/scorer/result, Algolia, **Google Play Developer API** (`googleapis` androidpublisher) for IAP |

Firebase Auth + Firestore throughout. No Nest yet (that’s Bantu Sesama 2022).

---

## 3. Contribution — patterns you actually shipped

Sole engineer, both sides. Interview: **this is where Flutter and test-engine design started**, not a junior ticket.

### 3.1 Factory + Strategy (you remembered both)

You were right that it could be named either. In this tree they **compose**:

1. **Strategy** — `TestScorer` / `TestValidator` / `Transformer` interfaces in `functions/src/types.ts`. Each method has its own scorer (`MbtiScorer.evaluate()`, RIASEC, Raven, …). Scoring is swapped, not a giant `if`.  
2. **Factory** — `TestScorerFactory implements Factory` maps `testMethod` → scorer class (plus answer-transformer factory, question-transformer factory, validator factory).  
3. **Mobile factories** (explicit folder `lib/factory/`): `TestProviderFactory`, `QuestionWidgetFactory`, `TestPageFactory`, `TestResultFactory` — `switch (method)` that returns the right Provider, question widget, page, or result screen.  
4. **Abstract `Test` / `Question` / `Answer`** (`lib/model/test.dart`) share fetch/timer; subclasses differ.

Say in interviews: **“Factory to pick a Strategy per psychometric method.”** Don’t claim Abstract Factory or Visitor. Don’t say Strategy *instead of* Factory — the code uses **both**.

Play Billing verification is in `functions/src/http/google_play.ts`. Dart unit tests exist for MBTI + profile builder.

### 3.2 Outcomes

- First production Flutter app (2019), before Wakafyuk.  
- ~dozen test methods without a single 2,000-line widget.  
- Scoring on Functions with a real Jest suite (per-method scorer/validator/result tests).  
- Admin CMS for questions + campus/major graph.  
- IAP hooked to Play Developer API.

---

## 4. Suggested resume copy

### 4.1 Header

**Sole Full-Stack Engineer, Brainplus** — Aug 2019 – Jun 2020  
First Flutter app: psychometric/aptitude tests, campus catalog, IAP. Factory + Strategy for a family of test methods. React Bootstrap admin + Cloud Functions.

### 4.2 Short blurb

Sole developer of Brainplus, an Indonesian student career app. Flutter (my first) + Firebase; Cloud Functions score MBTI, RIASEC, Raven, and aptitude batteries via Factory-selected Strategy classes; React dashboard for the question and campus CMS.

### 4.3 Medium blurb

Brainplus is where I learned Flutter (Aug 2019, Provider, pre-null-safety) and applied design patterns under real product pressure: each psychometric method has its own scorer/validator/UI, selected by factories (`TestScorerFactory`, `QuestionWidgetFactory`, …). I also built the Bootstrap React admin and the Cloud Functions that score tests and verify Play purchases. Sole author of both repos. Lamacca still lists the product; it is not on GitHub Selected Projects.

### 4.4 Bullets

- Sole engineer, first Flutter production app (2019): tests, Brainpedia (Algolia), brainpath, community, IAP.  
- **Factory + Strategy** for 14 test methods (MBTI, RIASEC, Big Five, Raven, aptitudes) — Functions scorers + Flutter widgets/pages.  
- Jest suite per scorer/validator/result; scoring **not** trusted to the client.  
- React 16 + Reactstrap CMS: questions, SMA/PT, majors, Holland-code reference.  
- Google Play purchase verification via androidpublisher API.

### 4.5 Skills

Flutter (first production), Dart 2, Provider, Firebase (Auth, Firestore, Functions, Storage, Crashlytics), Cloud Functions / TypeScript, Jest, React, Redux-Saga, Bootstrap / Reactstrap, Algolia, Google Play Billing, Factory pattern, Strategy pattern

### 4.6 Say carefully

| Claim | Safer |
|---|---|
| “I invented Flutter at Lamacca” | **Your first Flutter app.** Later products reuse it. |
| Strategy XOR Factory | **Both:** factory **selects** the strategy. |
| “ML / predictive modelling” | **Rule-based psychometric scoring**, not a fitted model. Lamacca marketing overreaches. |
| Licensed MBTI™ | You implemented **an MBTI-style instrument**. Don’t claim official Myers-Briggs licensing unless you have it. |
| DISC / RMIB live | Widgets exist; **not in the factories**. |
| User/download counts | **None in repo.** |
| Null-safety / Riverpod | This tree is **Provider 3, SDK &lt; 3.0**. Riverpod is Bantu/WBN. |
| GitHub Selected | **Not listed.** |

---

## 5. Suggested 3-bullet block

**Brainplus** — Sole Full-Stack Engineer *(Aug 2019 – Jun 2020)*  
Student career app (psychometrics, campus catalog). First production Flutter project.

- Flutter + Firebase for MBTI, RIASEC, Raven, and aptitude tests, with Algolia Brainpedia and Play IAP.  
- Factory + Strategy on Cloud Functions and in `lib/factory/*` so each test method has its own scorer, validator, and UI.  
- React Bootstrap dashboard to author questions, Holland codes, and school/major data.

---

## 6. Glossary

| Term | Meaning |
|---|---|
| **Brainpath** | Ordered test plan / recommendation path for a student type |
| **Brainpedia** | Catalog: scholarships, colleges, majors |
| **RIASEC / Holland** | Interest codes mapped to majors/careers |
| **LS** | Learning-styles instrument in this app |
| **Factory / Strategy** | `make(method)` → object that **evaluates that method** |

---

*Generated from `brainplus_mobile` and `brainplus` (including `functions/`) as of 16 August 2026. Sole author. Pattern names confirmed from `lib/factory` and `functions/src/use_cases/*/factory.ts`.*
