# API Client Conventions

## Philosophy

The app is a pure REST client. All API interactions go through a typed repository layer. No widget or Cubit talks directly to Dio.

---

## Dio Client Setup

Configure the Dio client in `lib/core/api/dio_client.dart`:

```dart
class DioClient {
  late final Dio _dio;

  DioClient() {
    _dio = Dio(BaseOptions(
      baseUrl: AppConfig.apiBaseUrl,
      connectTimeout: const Duration(seconds: 10),
      receiveTimeout: const Duration(seconds: 30),
      headers: {'Content-Type': 'application/json'},
    ));
    _dio.interceptors.addAll([
      AuthInterceptor(),
      LoggingInterceptor(),
    ]);
  }
}
```

---

## Auth Interceptor

Attach the JWT to every request. Refresh on 401:

```dart
class AuthInterceptor extends Interceptor {
  @override
  void onRequest(RequestOptions options, RequestInterceptorHandler handler) {
    final token = TokenStorage.accessToken;
    if (token != null) {
      options.headers['Authorization'] = 'Bearer $token';
    }
    handler.next(options);
  }

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) async {
    if (err.response?.statusCode == 401) {
      // Attempt token refresh
      final refreshed = await _attemptRefresh();
      if (refreshed) {
        // Retry original request
        handler.resolve(await _retry(err.requestOptions));
        return;
      }
      // Redirect to login
      getIt<AppRouter>().go('/login');
    }
    handler.next(err);
  }
}
```

---

## Repository Pattern

Every feature's repository implements a typed interface:

```dart
// domain/repositories/auth_repository.dart
abstract class AuthRepository {
  Future<User> login(String email, String password);
  Future<void> logout();
}

// data/repositories/auth_repository_impl.dart
class AuthRepositoryImpl implements AuthRepository {
  final DioClient _client;

  @override
  Future<User> login(String email, String password) async {
    final response = await _client.post('/auth/login', data: {
      'email': email,
      'password': password,
    });
    return UserModel.fromJson(response.data).toEntity();
  }
}
```

---

## Error Handling

Map `DioException` to domain errors at the repository boundary. Cubits receive typed domain errors, not raw Dio exceptions.

```dart
Future<T> _safeRequest<T>(Future<T> Function() request) async {
  try {
    return await request();
  } on DioException catch (e) {
    switch (e.response?.statusCode) {
      case 401: throw UnauthorizedException();
      case 403: throw ForbiddenException();
      case 404: throw NotFoundException();
      case 409: throw ConflictException();
      default: throw ApiException(e.message ?? 'Unknown error');
    }
  }
}
```

---

## API Response Format

The backend follows the standard response format (see `dev-web` blueprint `07-api-conventions.md`):

```json
// Single entity
{ "id": "123", "name": "..." }

// Collection
{ "data": [...], "total": N }

// Error
{ "error": { "code": "ERROR_CODE", "message": "...", "details": {} } }
```

Parse accordingly in model `fromJson` methods. For error responses, check for `error.code` and throw a typed exception.

---

## Environment Configuration

The API base URL is injected at build time via `--dart-define`:

```dart
class AppConfig {
  static const String apiBaseUrl = String.fromEnvironment(
    'API_BASE_URL',
    defaultValue: 'http://localhost:8000',
  );
}
```

Never hardcode API URLs. Never commit actual URLs for sensitive environments.
