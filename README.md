<div align="center">

<img src="./assets/progtest-feature-graphic-tr.png" alt="ProgTest — Learn, test yourself and improve" width="100%" />

# ProgTest

### AI-powered quiz and learning platform for software developers

ProgTest is a production Android application that combines programming quizzes,
short-form discovery content, detailed performance analysis and generative AI in
one learning experience.

[![Google Play](https://img.shields.io/badge/Google_Play-Download-2ea44f?logo=googleplay&logoColor=white)](https://play.google.com/store/apps/details?id=com.yusufUguz.progtest)
![Release](https://img.shields.io/badge/release-v2.0.0-6D5BD0)
![Flutter](https://img.shields.io/badge/Flutter-Dart-02569B?logo=flutter&logoColor=white)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-8.0-512BD4?logo=dotnet&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL_Server-EF_Core-CC2927?logo=microsoftsqlserver&logoColor=white)
![Gemini](https://img.shields.io/badge/Gemini-3.1_Flash_Lite-8E75B2?logo=googlegemini&logoColor=white)

[Download on Google Play](https://play.google.com/store/apps/details?id=com.yusufUguz.progtest)
&nbsp;•&nbsp;
[Developer portfolio](https://yusufuguz.com)

</div>

> Designed, developed, deployed and published end-to-end by
> [Yusuf Uğuz](https://github.com/YusufUguz): Flutter mobile client,
> ASP.NET Core Web API, SQL Server data layer, AI integration and the Google
> Play release.

---

## Contents

- [Product overview](#product-overview)
- [What's new in v2](#whats-new-in-v2)
- [Core experiences](#core-experiences)
- [AI capabilities](#ai-capabilities)
- [Architecture](#architecture)
- [Current project structure](#current-project-structure)
- [Engineering highlights](#engineering-highlights)
- [Technology stack](#technology-stack)
- [Screenshots](#screenshots)
- [Quality and delivery](#quality-and-delivery)
- [Author](#author)

---

## Product overview

ProgTest helps students and developers strengthen their programming knowledge
through guided tests and short, repeatable learning activities. A user can start
immediately without creating an account, choose a software category, solve a
level-appropriate test, review every answer and inspect strengths and knowledge
gaps over time.

The mobile app communicates with a custom ASP.NET Core API. The API owns
authentication, guest sessions, quiz content, results, analysis data, AI quotas
and Gemini access. Data is persisted in SQL Server through Entity Framework Core.
No AI credential is shipped inside the Flutter application.

The application interface and current Play Store listing are in Turkish.

## What's new in v2

- **Explore hub** — flashcards, output-prediction exercises, a programming
  dictionary and a daily question add short learning sessions outside quizzes.
- **Account-free experience** — an installation-scoped guest session receives a
  renewable JWT, allowing users to solve tests, use AI and retain statistics
  without first registering.
- **Modern quiz flow** — refreshed question cards, progress tracking, previous /
  next navigation and a clearer answer-selection experience.
- **Deeper analysis** — overall score metrics, answer distribution, category
  performance and topic-level gap analysis expose strong and weak areas.
- **Better result review** — every completed test includes question-by-question
  review and technical explanations.
- **Safer AI architecture** — Gemini calls moved behind the API with server-side
  validation, daily per-user quotas, IP rate limits and structured response
  handling.
- **Production observability** — release errors are reported through Firebase
  Crashlytics while the API uses structured Serilog request and application logs.

## Core experiences

| Area | What it provides |
|---|---|
| **Tests** | Programming categories, level-based tests, guided question flow and persisted results. |
| **Explore** | Flashcards, code-output challenges, programming terms and a daily question. |
| **AI** | Eight focused learning and career tools backed by Gemini through the ProgTest API. |
| **Analysis** | Score overview, correct / incorrect / blank distribution, category comparison and topic gaps. |
| **Profile** | Lifetime statistics, feedback, password management, account deletion and sign out. |
| **Guest mode** | Immediate use without e-mail or password, backed by a renewable anonymous server session. |

### Quiz and result flow

1. Select a programming category such as C#, Flutter, OOP, front-end,
   back-end or database management.
2. Choose a test aligned with the desired topic and level.
3. Move between questions while the interface tracks progress and selected
   answers.
4. Finish the test to see score, duration, correct, incorrect and blank totals.
5. Review individual answers with concise technical explanations.
6. Use the Analysis tab to inspect long-term category and topic performance.

### Explore hub

Four learning modes are currently available:

- **Flashcards** for fast recall and spaced repetition-style review.
- **Predict the Output** for reading short snippets before mentally executing
  the code.
- **Programming Dictionary** for concise explanations of technical terms.
- **Daily Question** for a small, rotating daily challenge.

The content endpoints support deterministic decks, category or language filters
and server-side validation. Interview preparation, community discussions,
complete-the-code and bug-hunt experiences are represented in the product
roadmap and clearly marked as upcoming in the app.

## AI capabilities

The AI hub exposes eight active, purpose-built experiences. Each mode sends a
constrained feature identifier and validated input to the back-end instead of
calling Gemini directly from the device.

| # | Experience | Purpose |
|---:|---|---|
| 1 | **Ask AI** | Get a focused answer to a programming question. |
| 2 | **Chat with AI** | Continue a contextual, multi-turn learning conversation. |
| 3 | **Create a Test with AI** | Generate a structured ten-question test and save it as a solvable private test. |
| 4 | **Career Field Recommendation** | Turn guided answers into suitable software-career suggestions. |
| 5 | **Project Roadmap** | Break a platform and project idea into an actionable development plan. |
| 6 | **Learning Guide** | Identify the concepts and sequence needed to learn a chosen field. |
| 7 | **Technical Interview Simulation** | Practice realistic technical interview questions and feedback. |
| 8 | **Knowledge-Level Assessment** | Generate a level-aware assessment and evaluate current understanding. |

AI-generated tests are created transactionally on the server: the model response
is validated and converted to domain entities before the test and its questions
are committed. A failed generation does not leave a partial test in the database.

## Architecture

```text
┌─────────────────────────────────────────────────────────────────────┐
│ Flutter mobile client                                               │
│                                                                     │
│  Views + widgets  ⇄  Cubit/ViewModel  ⇄  services + typed models    │
│         │                                  │                        │
│         └─ secure storage / outbox / session handling ──────────────┤
└───────────────────────────────────┬─────────────────────────────────┘
                                    │ HTTPS + JSON + Bearer JWT
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│ ASP.NET Core 8 Web API                                              │
│                                                                     │
│ Controllers → validation → application services → EF Core           │
│      │              │                    │                          │
│      │              ├─ user + IP limits  ├─ Gemini API              │
│      │              └─ auth / guest JWT  └─ structured AI parsing   │
└───────────────────────────────────┬─────────────────────────────────┘
                                    │
                                    ▼
                         Microsoft SQL Server
```

The Flutter codebase uses a **feature-first MVVM-style organization**. Screens
are composed from small widgets; Cubit-based ViewModels emit explicit loading,
loaded and error states; shared networking, session, design-system and storage
concerns live in `core/`.

## Current project structure

```text
progtest/                                  Flutter mobile application
├─ lib/
│  ├─ main.dart                            Startup and app composition
│  ├─ core/
│  │  ├─ constants/                        API routes, assets and design tokens
│  │  ├─ models/                           Typed API/domain models
│  │  ├─ network/                          Unauthorized/session handling
│  │  ├─ secure_storage/                   JWT, user and guest persistence
│  │  ├─ services/
│  │  │  ├─ ai/                            Back-end AI client + typed failures
│  │  │  ├─ guest_session_service.dart     Anonymous bootstrap and refresh
│  │  │  ├─ statistics_outbox_service.dart Offline result queue
│  │  │  └─ crash_reporting_service.dart   Firebase Crashlytics bridge
│  │  └─ general_widgets/                  Shared loading/error/feedback UI
│  └─ features/
│     ├─ splash/ login/ register/ home/
│     ├─ tests/ questions/ results/
│     ├─ discover/                         Four active micro-learning modes
│     ├─ AI_page/                          Eight active AI experiences
│     ├─ analysis/                         Category and topic performance
│     ├─ profile/ change_password/ delete_account/
│     └─ bottom_nav_bar/
├─ assets/                                 Images, icons, fonts and local data
├─ test/                                   Unit, model and widget tests
├─ android/ ios/ web/ linux/ macos/         Flutter platform projects
└─ store_assets/                           Play Store images and promo video

ProgTestAPI/                               ASP.NET Core 8 back-end
├─ Controllers/
│  ├─ SessionsController.cs                Guest create/refresh
│  ├─ DiscoverController.cs                Explore content
│  ├─ AiController.cs                      Protected AI gateway
│  ├─ Categories/Tests/Questions           Quiz domain endpoints
│  └─ Users/UserStatistics/UserFeedbacks   Account and analytics endpoints
├─ Services/                               Gemini, quota, discovery, auth,
│                                          explanations and test generation
├─ Models/                                 Identity + EF Core entities/context
├─ DTO/                                    Explicit request/response contracts
├─ Data/                                   Seed and official explanation data
├─ Migrations/                             EF Core schema history
├─ Tests/                                  API service/controller tests
├─ Tools/                                  Content-generation utilities
└─ Program.cs                              DI, auth, rate limiting and pipeline
```

## Engineering highlights

### Session and data resilience

- Registered users authenticate with ASP.NET Core Identity and JWT Bearer
  tokens.
- Guest identities are generated once per installation; only a SHA-256 hash of
  the installation secret is stored by the API, and refresh comparison is
  constant-time.
- Concurrent guest bootstrap calls share one request, preventing duplicate
  anonymous accounts.
- Results produced during a network interruption enter a bounded secure-storage
  outbox and are flushed to the correct user when connectivity returns.
- Expired or malformed sessions are recovered without trapping the user on the
  splash screen.

### AI safety and reliability

- Gemini credentials stay on the server and are never included in the APK.
- Inputs have length, format, feature and file-size validation.
- Daily per-user usage limits are combined with IP-based fixed-window rate
  limiting.
- Chat history is bounded, AI errors map to typed client states and unsuccessful
  requests can release their reserved quota.
- AI test and knowledge-assessment responses use schema-aware parsing and domain
  validation before reaching the database or UI.
- Official question explanations are served from persisted content; missing
  explanations can be generated, validated for technical depth and stored for
  reuse.

### UI and state management

- Feature-local Cubits isolate business logic and make loading, success and
  failure branches predictable.
- Small widgets under each feature keep rebuild boundaries focused and maximize
  reusable, constant UI.
- Centralized colors, typography, decorations, buttons and asset paths maintain
  a consistent visual language.
- Reusable empty, error, configuration and loading states provide consistent
  recovery paths across the application.

## Technology stack

| Layer | Technologies |
|---|---|
| **Mobile** | Flutter, Dart, `flutter_bloc`, `equatable`, `http` |
| **Local security** | `flutter_secure_storage`, JWT expiry handling |
| **UX** | Material, Google Nav Bar, Flutter Markdown, Toastification, cached images |
| **Observability** | Firebase Core, Firebase Crashlytics |
| **API** | ASP.NET Core 8, REST, Swagger / OpenAPI, Serilog |
| **Identity & security** | ASP.NET Core Identity, JWT Bearer, HTTPS, rate limiting |
| **Persistence** | Entity Framework Core Code First, SQL Server, migrations |
| **AI** | Google Gemini 3.1 Flash Lite through a server-side HTTP client |
| **Testing** | Flutter unit/widget tests and xUnit API tests |
| **Distribution** | Signed Android App Bundle, Google Play Console |

## Screenshots

Every image below was captured directly from the current `2.0.0+9` build running
on an Android emulator. The gallery follows the real product flows instead of
using older mockups or archived UI screens.

<div align="center">

### Main experience

| Home | Explore |
|:---:|:---:|
| ![Current home screen](./assets/screenshots/current/01-home.png) | ![Current Explore hub](./assets/screenshots/current/02-discover.png) |

| AI hub | Guest profile |
|:---:|:---:|
| ![Current AI feature hub](./assets/screenshots/current/05-ai-hub.png) | ![Current guest profile and settings](./assets/screenshots/current/10-profile.png) |

| Performance overview | Topic-gap analysis |
|:---:|:---:|
| ![Current analysis overview](./assets/screenshots/current/08-analysis.png) | ![Current topic-level analysis](./assets/screenshots/current/09-topic-analysis.png) |

### Explore and micro-learning

| Flashcards | Revealed flashcard |
|:---:|:---:|
| ![Current flashcard experience](./assets/screenshots/current/03-flashcards.png) | ![Current flashcard answer](./assets/screenshots/current/04-flashcard-answer.png) |

| Predict the output | Programming dictionary |
|:---:|:---:|
| ![Current output-prediction challenge](./assets/screenshots/current/20-output-prediction.png) | ![Current programming dictionary](./assets/screenshots/current/22-programming-dictionary.png) |

| Daily question | Explore roadmap |
|:---:|:---:|
| ![Current daily question](./assets/screenshots/current/23-daily-question.png) | ![Current and upcoming Explore modules](./assets/screenshots/current/21-discover-roadmap.png) |

### Complete quiz journey

| Available tests | Start confirmation |
|:---:|:---:|
| ![Current Flutter test list](./assets/screenshots/current/11-tests.png) | ![Current test-start dialog](./assets/screenshots/current/12-test-start-dialog.png) |

| Question | Selected answer |
|:---:|:---:|
| ![Current question screen](./assets/screenshots/current/13-question.png) | ![Current selected-answer state](./assets/screenshots/current/14-question-selected.png) |

| Final question | Detailed result |
|:---:|:---:|
| ![Current final question](./assets/screenshots/current/15-last-question.png) | ![Current detailed result](./assets/screenshots/current/16-result.png) |

| Question-by-question review |
|:---:|
| ![Current answer review and explanation](./assets/screenshots/current/17-answer-review.png) |

### AI learning tools

| Ask AI | Create a test with AI |
|:---:|:---:|
| ![Current Ask AI screen](./assets/screenshots/current/06-ask-ai.png) | ![Current AI test creator](./assets/screenshots/current/07-create-ai-test.png) |

| Career assessment | Project roadmap |
|:---:|:---:|
| ![Current AI career assessment](./assets/screenshots/current/24-career-assessment.png) | ![Current AI project roadmap](./assets/screenshots/current/25-project-roadmap.png) |

| AI feature catalog | Knowledge assessment |
|:---:|:---:|
| ![Current complete AI feature catalog](./assets/screenshots/current/26-ai-more.png) | ![Current AI knowledge assessment](./assets/screenshots/current/27-knowledge-assessment.png) |

### Account flow

| Login | Registration |
|:---:|:---:|
| ![Current login screen](./assets/screenshots/current/19-login.png) | ![Current registration screen](./assets/screenshots/current/18-register.png) |

</div>

## Quality and delivery

- Flutter tests cover session bootstrap, analysis models, discovery screens,
  AI flows, quiz interaction, result review, splash recovery and guest profiles.
- API tests cover guest identity behavior, AI quotas and builders, discovery
  content, official explanations and statistics endpoints.
- The production build enables Crashlytics collection while keeping development
  feedback local.
- The Android release uses a signed App Bundle and versioning sourced from
  `pubspec.yaml`; the current project version is `2.0.0+9`.
- Store screenshots, feature graphic and promotional video are maintained next
  to the mobile project so product visuals can evolve with the UI.

> This repository is the public product showcase. The production mobile and API
> codebases are maintained separately.

## Author

**Yusuf Uğuz** — Full-stack web and mobile developer

[![GitHub](https://img.shields.io/badge/GitHub-YusufUguz-181717?logo=github)](https://github.com/YusufUguz)
[![Portfolio](https://img.shields.io/badge/Portfolio-yusufuguz.com-6D5BD0)](https://yusufuguz.com)
[![Google Play](https://img.shields.io/badge/Google_Play-ProgTest-2ea44f?logo=googleplay&logoColor=white)](https://play.google.com/store/apps/details?id=com.yusufUguz.progtest)
