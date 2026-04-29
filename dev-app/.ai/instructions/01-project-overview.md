# Project Overview — [PROJECT NAME]

## What This Is

[One paragraph describing what the app does, who it's for, and its core differentiator.]

---

## Target Platforms

| Platform | Min version | Build target |
|---|---|---|
| Android | [API level] | `apk` / `appbundle` |
| iOS | [version] | `ipa` |

---

## Tech Stack

| Concern | Tool |
|---|---|
| Language | Dart 3.x |
| Framework | Flutter 3.x |
| State management | BLoC / Cubit (`flutter_bloc`) |
| Navigation | GoRouter |
| HTTP client | Dio + Interceptors |
| Local storage | Hive / SharedPreferences |
| Dependency injection | `get_it` |
| Logging | `logger` package |
| Testing | `flutter_test`, `bloc_test`, `mocktail` |
| CI/CD | GitHub Actions |

---

## Feature Areas

| Feature | Screens | BLoC/Cubit | API endpoints consumed |
|---|---|---|---|
| [Auth] | Login, Register | `AuthCubit` | `POST /auth/login`, `POST /auth/register` |
| [Feature 1] | [Screen A, Screen B] | [StateCubit] | [endpoints] |

---

## API Base URL

| Environment | Base URL |
|---|---|
| Development | `http://localhost:8000` |
| Production | `https://[api-host]` |

API base URL is injected via `--dart-define=API_BASE_URL=...` — never hardcoded.

---

## Repository Layout

```
lib/
├── core/
│   ├── api/              # Dio client, interceptors, base repository
│   ├── config/           # App config (API base URL, env values)
│   ├── router/           # GoRouter definition
│   └── theme/            # ThemeData, colors, text styles
├── features/
│   └── [feature]/
│       ├── data/         # Repository implementation, API models
│       ├── domain/       # Entities, repository interface
│       └── presentation/ # Screens, widgets, BLoC/Cubit
└── shared/
    ├── widgets/          # Reusable UI components
    └── utils/            # Shared utilities
test/
├── unit/                 # Unit tests for business logic
├── widget/               # Widget tests
integration_test/         # Integration (end-to-end) tests
```
