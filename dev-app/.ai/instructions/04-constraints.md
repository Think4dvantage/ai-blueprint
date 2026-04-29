# Constraints — What NOT to Do

## AI Files

**All AI-related content lives exclusively in `.ai/`.** Never create tool-specific instruction files such as `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`, `.windsurfrules`, or any equivalent — not even as thin pointers.

---

## Secrets

**Never commit secrets.** API keys, tokens, and credentials are never hardcoded. Use `--dart-define` for build-time injection. Never commit `.env` files with real values.

---

## Direct API Calls from UI

**Never call APIs directly from widgets or screens.** All API calls go through a repository, which is called by a Cubit/BLoC. Widgets observe state — they do not trigger network requests directly.

---

## Navigation

**Never use `Navigator.push` directly.** All navigation goes through GoRouter (`context.go()`, `context.push()`). Route paths are constants, not inline strings.

---

## State

**Never use `setState` in a screen that has a Cubit.** Mix of `setState` and BLoC/Cubit in the same widget causes split state and unpredictable rebuilds.

---

## Logging

**No `print()` statements.** Use the `logger` package with the appropriate log level. `print()` output is stripped in release builds but `logger` can be configured to do the same cleanly.

---

## Platform Code

**Never write platform-specific (Swift/Kotlin) code unless there is no Flutter plugin for it.** If a plugin exists on pub.dev that is maintained and widely used, use it. Only write platform channels as a last resort.

---

## Dependencies

**Vet pub.dev packages before adding them.** Check: pub score ≥ 120, Dart 3 compatible, maintained within the last 6 months. Do not add packages for functionality that Flutter or Dart stdlib already provides.

---

## Code Quality

- Don't add features, refactor code, or make "improvements" beyond what was asked.
- Don't add error handling for scenarios that can't happen.
- Don't create helpers or abstractions for one-time operations.
- Don't design for hypothetical future requirements.
- Don't add docstrings or comments to code you didn't change.
