# Prompt: Implement a New Feature

Use this checklist when implementing any non-trivial feature end-to-end.

---

## Domain Layer

- [ ] Define the entity (pure Dart class, no framework dependencies) in `features/[feature]/domain/entities/`
- [ ] Define the repository interface (abstract class) in `features/[feature]/domain/repositories/`

## Data Layer

- [ ] Create the API model (`fromJson` / `toJson`) in `features/[feature]/data/models/`
- [ ] Implement the repository (calls Dio client, maps model → entity, wraps in `_safeRequest`) in `features/[feature]/data/repositories/`
- [ ] Register the repository in `lib/core/config/injection.dart`

## Presentation Layer

- [ ] Create Cubit + sealed State class in `features/[feature]/presentation/cubit/`
- [ ] Register the Cubit in `injection.dart`
- [ ] Create screen(s) in `features/[feature]/presentation/screens/`
  - [ ] Use `BlocBuilder` for state-driven UI, `BlocListener` for side effects (navigation, snackbars)
  - [ ] All colors/text styles from Theme — no hardcoded values
  - [ ] Navigation via `context.go()` / `context.push()` — no `Navigator.push`
- [ ] Add route(s) to the feature's router file and register in `lib/core/router/router.dart`

## Testing

- [ ] Unit test for the Cubit (all state transitions) using `bloc_test`
- [ ] Unit test for the repository (success + error paths) using `mocktail`
- [ ] Widget test for the screen (loading, success, error states)

## Observability

- [ ] Cubit logs state transitions at `debug` level
- [ ] Repository logs API calls at `debug` level and errors at `error` level

## Documentation

- [ ] Add new screens to `context/architecture.md` screen map
- [ ] Add new Cubit to the state classes table
- [ ] Add new API endpoints to the API contracts section
- [ ] Add new local storage entries if applicable

Refer to `.ai/instructions/02-app-conventions.md` for patterns.
Refer to `.ai/instructions/03-api-client-conventions.md` for API integration.
Refer to `.ai/context/architecture.md` for existing contracts.
