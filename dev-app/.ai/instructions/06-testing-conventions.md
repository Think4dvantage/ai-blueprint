# Testing Conventions

## Philosophy

Testing is mandatory. Every feature must have tests at the appropriate level. AI-generated code is especially prone to subtle logic errors — tests are the safety net.

---

## Test Pyramid

```
              [ Integration ]   — few, slow, high confidence
           [ Widget Tests   ]   — moderate, test UI behavior
        [ Unit Tests        ]   — many, fast, test logic
```

Write the most tests at the unit level (Cubits, repositories, utilities). Widget tests for non-trivial UI behavior. Integration tests for critical user flows only.

---

## Unit Tests — Cubits and Repositories

Location: `test/unit/`

Use `bloc_test` for Cubits and `mocktail` for mocking dependencies.

```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:mocktail/mocktail.dart';

class MockAuthRepository extends Mock implements AuthRepository {}

void main() {
  group('AuthCubit', () {
    late MockAuthRepository mockRepo;
    late AuthCubit cubit;

    setUp(() {
      mockRepo = MockAuthRepository();
      cubit = AuthCubit(mockRepo);
    });

    blocTest<AuthCubit, AuthState>(
      'emits [AuthLoading, AuthSuccess] on successful login',
      build: () {
        when(() => mockRepo.login(any(), any()))
            .thenAnswer((_) async => User(id: '1', email: 'a@b.com'));
        return cubit;
      },
      act: (c) => c.login('a@b.com', 'password'),
      expect: () => [AuthLoading(), isA<AuthSuccess>()],
    );
  });
}
```

---

## Widget Tests

Location: `test/widget/`

Use `flutter_test` with `pumpWidget`. Mock Cubits with `MockCubit` from `bloc_test`.

```dart
testWidgets('shows error message when auth fails', (tester) async {
  final mockCubit = MockAuthCubit();
  whenListen(mockCubit, Stream.value(AuthError('Invalid credentials')));

  await tester.pumpWidget(
    BlocProvider<AuthCubit>.value(
      value: mockCubit,
      child: const MaterialApp(home: LoginScreen()),
    ),
  );
  await tester.pump();

  expect(find.text('Invalid credentials'), findsOneWidget);
});
```

---

## Integration Tests

Location: `integration_test/`

Use `flutter_test` with `IntegrationTestWidgetsFlutterBinding`. Run against a real dev API instance.

```dart
void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();

  testWidgets('user can log in and see home screen', (tester) async {
    app.main();
    await tester.pumpAndSettle();

    await tester.enterText(find.byKey(Key('email_field')), 'test@example.com');
    await tester.enterText(find.byKey(Key('password_field')), 'password');
    await tester.tap(find.byKey(Key('login_button')));
    await tester.pumpAndSettle();

    expect(find.byKey(Key('home_screen')), findsOneWidget);
  });
}
```

---

## CI / Automation

- Unit and widget tests run on every pull request via GitHub Actions.
- Integration tests run on a schedule (nightly) or on release branch creation.
- **Coverage**: aim for ≥ 80% on Cubit/repository layer. UI widgets are lower priority.

```yaml
- name: Run unit and widget tests
  run: flutter test --coverage

- name: Run integration tests
  run: flutter test integration_test/ --dart-define=API_BASE_URL=http://localhost:8000
```
