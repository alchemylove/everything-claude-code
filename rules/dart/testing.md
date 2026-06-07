---
paths:
  - "**/*.dart"
  - "**/pubspec.yaml"
  - "**/analysis_options.yaml"
---
# Dart/Flutter テスト (Dart/Flutter Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Dart および Flutter 固有の内容を追加する。

## テストフレームワーク (Test Framework)

- **flutter_test** / **dart:test** — 組み込み test runner
- モックには **mockito**（`@GenerateMocks` 付き）または **mocktail**（codegen なし）
- BLoC/Cubit unit test には **bloc_test**
- unit test で時間を制御するには **fake_async**
- 実デバイス E2E には **integration_test**

## テスト種別 (Test Types)

| Type | Tool | Location | When to Write |
|------|------|----------|---------------|
| Unit | `dart:test` | `test/unit/` | すべての domain logic、state manager、repository |
| Widget | `flutter_test` | `test/widget/` | 意味のある挙動を持つすべての widget |
| Golden | `flutter_test` | `test/golden/` | デザイン上重要な UI component |
| Integration | `integration_test` | `integration_test/` | 実デバイス/emulator 上の重要 user flow |

## Unit Test: State Manager

### `bloc_test` による BLoC

```dart
group('CartBloc', () {
  late CartBloc bloc;
  late MockCartRepository repository;

  setUp(() {
    repository = MockCartRepository();
    bloc = CartBloc(repository);
  });

  tearDown(() => bloc.close());

  blocTest<CartBloc, CartState>(
    'emits updated items when CartItemAdded',
    build: () => bloc,
    act: (b) => b.add(CartItemAdded(testItem)),
    expect: () => [CartState(items: [testItem])],
  );

  blocTest<CartBloc, CartState>(
    'emits empty cart when CartCleared',
    seed: () => CartState(items: [testItem]),
    build: () => bloc,
    act: (b) => b.add(CartCleared()),
    expect: () => [const CartState()],
  );
});
```

### `ProviderContainer` による Riverpod

```dart
test('usersProvider loads users from repository', () async {
  final container = ProviderContainer(
    overrides: [userRepositoryProvider.overrideWithValue(FakeUserRepository())],
  );
  addTearDown(container.dispose);

  final result = await container.read(usersProvider.future);
  expect(result, isNotEmpty);
});
```

## Widget Test

```dart
testWidgets('CartPage shows item count badge', (tester) async {
  await tester.pumpWidget(
    ProviderScope(
      overrides: [
        cartNotifierProvider.overrideWith(() => FakeCartNotifier([testItem])),
      ],
      child: const MaterialApp(home: CartPage()),
    ),
  );

  await tester.pump();
  expect(find.text('1'), findsOneWidget);
  expect(find.byType(CartItemTile), findsOneWidget);
});

testWidgets('shows empty state when cart is empty', (tester) async {
  await tester.pumpWidget(
    ProviderScope(
      overrides: [cartNotifierProvider.overrideWith(() => FakeCartNotifier([]))],
      child: const MaterialApp(home: CartPage()),
    ),
  );

  await tester.pump();
  expect(find.text('Your cart is empty'), findsOneWidget);
});
```

## Mock より Fake (Fakes Over Mocks)

複雑な dependency には手書き fake を優先する:

```dart
class FakeUserRepository implements UserRepository {
  final _users = <String, User>{};
  Object? fetchError;

  @override
  Future<User?> getById(String id) async {
    if (fetchError != null) throw fetchError!;
    return _users[id];
  }

  @override
  Future<List<User>> getAll() async {
    if (fetchError != null) throw fetchError!;
    return _users.values.toList();
  }

  @override
  Stream<List<User>> watchAll() => Stream.value(_users.values.toList());

  @override
  Future<void> save(User user) async {
    _users[user.id] = user;
  }

  @override
  Future<void> delete(String id) async {
    _users.remove(id);
  }

  void addUser(User user) => _users[user.id] = user;
}
```

## 非同期テスト (Async Testing)

```dart
// Use fake_async for controlling timers and Futures
test('debounce triggers after 300ms', () {
  fakeAsync((async) {
    final debouncer = Debouncer(delay: const Duration(milliseconds: 300));
    var callCount = 0;
    debouncer.run(() => callCount++);
    expect(callCount, 0);
    async.elapse(const Duration(milliseconds: 200));
    expect(callCount, 0);
    async.elapse(const Duration(milliseconds: 200));
    expect(callCount, 1);
  });
});
```

## Golden Test

```dart
testWidgets('UserCard golden test', (tester) async {
  await tester.pumpWidget(
    MaterialApp(home: UserCard(user: testUser)),
  );

  await expectLater(
    find.byType(UserCard),
    matchesGoldenFile('goldens/user_card.png'),
  );
});
```

意図的な visual 変更時は `flutter test --update-goldens` を実行する。

## テスト命名 (Test Naming)

挙動に焦点を当てた説明的な名前を使う:

```dart
test('returns null when user does not exist', () { ... });
test('throws NotFoundException when id is empty string', () { ... });
testWidgets('disables submit button while form is invalid', (tester) async { ... });
```

## テスト構成 (Test Organization)

```
test/
├── unit/
│   ├── domain/
│   │   └── usecases/
│   └── data/
│       └── repositories/
├── widget/
│   └── presentation/
│       └── pages/
└── golden/
    └── widgets/

integration_test/
└── flows/
    ├── login_flow_test.dart
    └── checkout_flow_test.dart
```

## カバレッジ (Coverage)

- business logic（domain + state manager）の line coverage は 80%+ を目標
- すべての state transition に test が必要: loading → success、loading → error、retry
- `flutter test --coverage` を実行し、coverage reporter で `lcov.info` を確認する
- 閾値未満の coverage は CI でブロックする
