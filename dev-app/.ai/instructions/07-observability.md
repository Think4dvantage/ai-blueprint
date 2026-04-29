# Observability Conventions

## Philosophy

An engineer debugging a production issue on a user's device must be able to reconstruct what happened from logs alone. Log everything that matters.

---

## Logging — `logger` Package

Never use `print()`. Use the `logger` package throughout.

### Setup

Configure a singleton logger in `lib/core/config/logger.dart`:

```dart
import 'package:logger/logger.dart';

final logger = Logger(
  printer: PrettyPrinter(
    methodCount: 0,
    errorMethodCount: 8,
    lineLength: 120,
    colors: true,
    printEmojis: false,
    printTime: true,
  ),
  level: kReleaseMode ? Level.warning : Level.trace,
);
```

In release builds, only `warning` and above are logged. In debug builds, all levels are logged.

### Log Levels

| Level | When |
|---|---|
| `logger.t()` (trace) | Per-frame detail, polling, high-frequency events |
| `logger.d()` (debug) | State transitions, API request/response details |
| `logger.i()` (info) | App startup steps, navigation events, user actions |
| `logger.w()` (warning) | Recoverable errors, empty API responses, retries |
| `logger.e()` (error) | Exceptions, failed API calls, unexpected states |

### What to Log

| Event | Level | What to include |
|---|---|---|
| App startup | `info` | App version, environment, API base URL |
| Navigation | `info` | Route name, parameters |
| API request | `debug` | Method, URL, request body (sanitize credentials) |
| API response | `debug` | Status code, response size, elapsed ms |
| Cubit state change | `debug` | Old state type → new state type |
| User action | `info` | Action name, relevant parameters |
| API error | `error` | Status code, error code, message, URL |
| Unexpected exception | `error` | Full stack trace + context |

### Never Log

- Passwords, tokens, or any credential — even in debug builds.
- Full request/response bodies for auth endpoints.

---

## Error Reporting

For unhandled exceptions, catch at the top level in `main()`:

```dart
void main() {
  FlutterError.onError = (details) {
    logger.e('Flutter error', error: details.exception, stackTrace: details.stack);
  };

  PlatformDispatcher.instance.onError = (error, stack) {
    logger.e('Platform error', error: error, stackTrace: stack);
    return true;
  };

  runApp(const MyApp());
}
```

In production, consider a self-hosted error tracking solution consistent with the no-public-cloud philosophy.

---

## Debug vs. Release Behavior

| Behavior | Debug | Release |
|---|---|---|
| Log level | All (trace+) | Warning+ only |
| API base URL | `localhost` (via `--dart-define`) | Production URL |
| Assert statements | Active | Stripped |
| Error overlay | Visible | Hidden |

Use `kDebugMode` / `kReleaseMode` constants (from `flutter/foundation.dart`) to gate debug-only behavior — never use environment strings for this.
