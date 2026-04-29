# App Conventions

## Project Structure

Follow clean architecture with three layers per feature:

```
features/[feature]/
  data/
    models/           # JSON serializable API models (fromJson/toJson)
    repositories/     # Implements domain interface; calls API
  domain/
    entities/         # Pure Dart classes — no framework dependencies
    repositories/     # Abstract interface
  presentation/
    screens/          # Full-screen widgets, one file per screen
    widgets/          # Feature-local reusable widgets
    cubit/            # Cubit + State classes
```

One folder per feature. Never put routes from multiple features in the same file.

---

## State Management — BLoC/Cubit

Use **Cubit** for simple state, **BLoC** for event-driven flows. Default to Cubit unless the state transitions are complex enough to warrant explicit events.

```dart
// Cubit pattern
class AuthCubit extends Cubit<AuthState> {
  AuthCubit(this._authRepository) : super(AuthInitial());

  final AuthRepository _authRepository;

  Future<void> login(String email, String password) async {
    emit(AuthLoading());
    try {
      final user = await _authRepository.login(email, password);
      emit(AuthSuccess(user));
    } catch (e) {
      emit(AuthError(e.toString()));
    }
  }
}
```

**Rules**:
- Never call API directly from a widget — always go through a Cubit/BLoC.
- Never use `setState` in a screen that has a Cubit — use `BlocBuilder` / `BlocListener`.
- Cubit states are sealed classes (Dart 3+): `AuthInitial`, `AuthLoading`, `AuthSuccess`, `AuthError`.

---

## Navigation — GoRouter

All routes are defined in `lib/core/router/router.dart`. Each feature registers its own routes in a separate file and they are composed in the main router.

```dart
// core/router/router.dart
final router = GoRouter(
  routes: [
    ...authRoutes,
    ...featureRoutes,
  ],
);
```

**Rules**:
- Never use `Navigator.push` directly — always navigate via `context.go()` or `context.push()`.
- Route paths are defined as constants, not inline strings.
- Protected routes check auth state via a `redirect` callback in the router.

---

## Dependency Injection — get_it

Register dependencies in `lib/core/config/injection.dart`:

```dart
void configureDependencies() {
  getIt.registerLazySingleton<DioClient>(() => DioClient());
  getIt.registerLazySingleton<AuthRepository>(() => AuthRepositoryImpl(getIt()));
}
```

Call `configureDependencies()` once in `main()` before `runApp()`.

---

## API Models

Use `fromJson` / `toJson` manually or with `json_serializable`. Keep API models in the `data/` layer — never expose them to the domain or presentation layer. Map to domain entities in the repository.

```dart
class UserModel {
  final String id;
  final String email;

  UserModel({required this.id, required this.email});

  factory UserModel.fromJson(Map<String, dynamic> json) =>
      UserModel(id: json['id'], email: json['email']);

  User toEntity() => User(id: id, email: email);
}
```

---

## Coding Standards

- **Always use typed parameters** — never `dynamic` unless forced by an external API.
- **Named parameters** for constructors with more than 2 arguments.
- **`const` constructors** wherever possible — improves rebuild performance.
- **No `print()` statements** — use the `logger` package (see `07-observability.md`).
- **Async/await** for all async operations — never raw `Future.then()`.
- **`late` keyword** only for values that are initialized before first use and provably non-null. If uncertain, use nullable + null check.

---

## Theming

Define all colors, text styles, and spacing in `lib/core/theme/`. Never hardcode color values inline.

```dart
// Good
color: Theme.of(context).colorScheme.primary

// Bad
color: Color(0xFF90CDF4)
```
