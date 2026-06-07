---
paths:
  - "**/*.kt"
  - "**/*.kts"
---
# Kotlin テスト (Kotlin Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Kotlin および Android/KMP 固有の内容を追加する。

## テストフレームワーク (Test Framework)

- マルチプラットフォーム（KMP）には **kotlin.test** — `@Test`、`assertEquals`、`assertTrue`
- Android 固有のテストには **JUnit 4/5**
- Flow と StateFlow のテストには **Turbine**
- coroutine テストには **kotlinx-coroutines-test**（`runTest`、`TestDispatcher`）

## Turbine による ViewModel テスト (ViewModel Testing with Turbine)

```kotlin
@Test
fun `loading state emitted then data`() = runTest {
    val repo = FakeItemRepository()
    repo.addItem(testItem)
    val viewModel = ItemListViewModel(GetItemsUseCase(repo))

    viewModel.state.test {
        assertEquals(ItemListState(), awaitItem())     // initial state
        viewModel.onEvent(ItemListEvent.Load)
        assertTrue(awaitItem().isLoading)               // loading
        assertEquals(listOf(testItem), awaitItem().items) // loaded
    }
}
```

## Mock より Fake (Fakes Over Mocks)

モックフレームワークより手書きの fake を優先:

```kotlin
class FakeItemRepository : ItemRepository {
    private val items = mutableListOf<Item>()
    var fetchError: Throwable? = null

    override suspend fun getAll(): Result<List<Item>> {
        fetchError?.let { return Result.failure(it) }
        return Result.success(items.toList())
    }

    override fun observeAll(): Flow<List<Item>> = flowOf(items.toList())

    fun addItem(item: Item) { items.add(item) }
}
```

## Coroutine テスト (Coroutine Testing)

```kotlin
@Test
fun `parallel operations complete`() = runTest {
    val repo = FakeRepository()
    val result = loadDashboard(repo)
    advanceUntilIdle()
    assertNotNull(result.items)
    assertNotNull(result.stats)
}
```

`runTest` を使用 — 仮想時間を自動進行し `TestScope` を提供する。

## Ktor MockEngine

```kotlin
val mockEngine = MockEngine { request ->
    when (request.url.encodedPath) {
        "/api/items" -> respond(
            content = Json.encodeToString(testItems),
            headers = headersOf(HttpHeaders.ContentType, ContentType.Application.Json.toString())
        )
        else -> respondError(HttpStatusCode.NotFound)
    }
}

val client = HttpClient(mockEngine) {
    install(ContentNegotiation) { json() }
}
```

## Room/SQLDelight テスト (Room/SQLDelight Testing)

- Room: インメモリテストに `Room.inMemoryDatabaseBuilder()` を使用
- SQLDelight: JVM テストに `JdbcSqliteDriver(JdbcSqliteDriver.IN_MEMORY)` を使用

```kotlin
@Test
fun `insert and query items`() = runTest {
    val driver = JdbcSqliteDriver(JdbcSqliteDriver.IN_MEMORY)
    Database.Schema.create(driver)
    val db = Database(driver)

    db.itemQueries.insert("1", "Sample Item", "description")
    val items = db.itemQueries.getAll().executeAsList()
    assertEquals(1, items.size)
}
```

## テスト命名 (Test Naming)

バッククォート付きの説明的な名前を使用:

```kotlin
@Test
fun `search with empty query returns all items`() = runTest { }

@Test
fun `delete item emits updated list without deleted item`() = runTest { }
```

## テスト構成 (Test Organization)

```
src/
├── commonTest/kotlin/     # Shared tests (ViewModel, UseCase, Repository)
├── androidUnitTest/kotlin/ # Android unit tests (JUnit)
├── androidInstrumentedTest/kotlin/  # Instrumented tests (Room, UI)
└── iosTest/kotlin/        # iOS-specific tests
```

最小テストカバレッジ: すべての feature で ViewModel + UseCase。
