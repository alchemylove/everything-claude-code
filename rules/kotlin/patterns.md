---
paths:
  - "**/*.kt"
  - "**/*.kts"
---
# Kotlin パターン (Kotlin Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Kotlin および Android/KMP 固有の内容を追加する。

## 依存性注入 (Dependency Injection)

コンストラクタ注入を優先。Koin（KMP）または Hilt（Android のみ）を使用:

```kotlin
// Koin — declare modules
val dataModule = module {
    single<ItemRepository> { ItemRepositoryImpl(get(), get()) }
    factory { GetItemsUseCase(get()) }
    viewModelOf(::ItemListViewModel)
}

// Hilt — annotations
@HiltViewModel
class ItemListViewModel @Inject constructor(
    private val getItems: GetItemsUseCase
) : ViewModel()
```

## ViewModel パターン (ViewModel Pattern)

単一の状態オブジェクト、イベントシンク、一方向データフロー:

```kotlin
data class ScreenState(
    val items: List<Item> = emptyList(),
    val isLoading: Boolean = false
)

class ScreenViewModel(private val useCase: GetItemsUseCase) : ViewModel() {
    private val _state = MutableStateFlow(ScreenState())
    val state = _state.asStateFlow()

    fun onEvent(event: ScreenEvent) {
        when (event) {
            is ScreenEvent.Load -> load()
            is ScreenEvent.Delete -> delete(event.id)
        }
    }
}
```

## Repository パターン (Repository Pattern)

- `suspend` 関数は `Result<T>` またはカスタムエラー型を返す
- リアクティブストリームには `Flow`
- ローカル + リモートのデータソースを調整

```kotlin
interface ItemRepository {
    suspend fun getById(id: String): Result<Item>
    suspend fun getAll(): Result<List<Item>>
    fun observeAll(): Flow<List<Item>>
}
```

## UseCase パターン (UseCase Pattern)

単一責任、`operator fun invoke`:

```kotlin
class GetItemUseCase(private val repository: ItemRepository) {
    suspend operator fun invoke(id: String): Result<Item> {
        return repository.getById(id)
    }
}

class GetItemsUseCase(private val repository: ItemRepository) {
    suspend operator fun invoke(): Result<List<Item>> {
        return repository.getAll()
    }
}
```

## expect/actual (KMP)

プラットフォーム固有の実装に使用:

```kotlin
// commonMain
expect fun platformName(): String
expect class SecureStorage {
    fun save(key: String, value: String)
    fun get(key: String): String?
}

// androidMain
actual fun platformName(): String = "Android"
actual class SecureStorage {
    actual fun save(key: String, value: String) { /* EncryptedSharedPreferences */ }
    actual fun get(key: String): String? = null /* ... */
}

// iosMain
actual fun platformName(): String = "iOS"
actual class SecureStorage {
    actual fun save(key: String, value: String) { /* Keychain */ }
    actual fun get(key: String): String? = null /* ... */
}
```

## Coroutine パターン (Coroutine Patterns)

- ViewModel では `viewModelScope`、構造化された子タスクには `coroutineScope`
- コールド Flow からの StateFlow には `stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), initialValue)` を使用
- 子の失敗を独立させる場合は `supervisorScope` を使用

## DSL による Builder パターン (Builder Pattern with DSL)

```kotlin
class HttpClientConfig {
    var baseUrl: String = ""
    var timeout: Long = 30_000
    private val interceptors = mutableListOf<Interceptor>()

    fun interceptor(block: () -> Interceptor) {
        interceptors.add(block())
    }
}

fun httpClient(block: HttpClientConfig.() -> Unit): HttpClient {
    val config = HttpClientConfig().apply(block)
    return HttpClient(config)
}

// Usage
val client = httpClient {
    baseUrl = "https://api.example.com"
    timeout = 15_000
    interceptor { AuthInterceptor(tokenProvider) }
}
```

## 参照 (References)

詳細な coroutine パターンは skill: `kotlin-coroutines-flows` を参照。
モジュールとレイヤーのパターンは skill: `android-clean-architecture` を参照。
