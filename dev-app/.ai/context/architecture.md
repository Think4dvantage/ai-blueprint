# Architecture Reference

## Screen Map

| Feature | Screen | Route | State |
|---|---|---|---|
| Auth | Login | `/login` | `AuthCubit` |
| Auth | Register | `/register` | `AuthCubit` |
| [Feature] | [Screen] | `/[path]` | `[Cubit]` |

[Document every screen here. This is the source of truth for the screen inventory.]

---

## State Classes

| Cubit | States | Responsibility |
|---|---|---|
| `AuthCubit` | `AuthInitial`, `AuthLoading`, `AuthSuccess`, `AuthError` | Login, logout, session check |
| `[FeatureCubit]` | `[...]` | [What it manages] |

---

## API Contracts Consumed

### Auth

- `POST /auth/login` — `{email, password}` → `{access_token, refresh_token}`
- `POST /auth/register` — `{email, password}` → `{user_id, token}`
- `POST /auth/refresh` — `{refresh_token}` → `{access_token}`

### [Feature]

- `GET /[resource]` — → `{data: [...], total: N}`
- `POST /[resource]` — `{...}` → `{id, ...}`

[Add all consumed endpoints here, grouped by feature.]

---

## Local Storage

| Key / Collection | Library | Type | Purpose |
|---|---|---|---|
| `access_token` | SharedPreferences | `String` | JWT access token |
| `refresh_token` | SharedPreferences | `String` | JWT refresh token |
| `[collection]` | Hive | `[Model]` | [What's cached] |

---

## Environment Configuration

| Variable | How injected | Default (dev) |
|---|---|---|
| `API_BASE_URL` | `--dart-define` | `http://localhost:8000` |

---

## Dependencies

| Package | Version | Purpose |
|---|---|---|
| `flutter_bloc` | `^8.x` | State management |
| `go_router` | `^14.x` | Navigation |
| `dio` | `^5.x` | HTTP client |
| `get_it` | `^7.x` | Dependency injection |
| `logger` | `^2.x` | Logging |
| `shared_preferences` | `^2.x` | Key-value local storage |
| `hive_flutter` | `^1.x` | Typed local database |
| `bloc_test` | `^9.x` | Cubit/BLoC testing |
| `mocktail` | `^1.x` | Mocking in tests |
