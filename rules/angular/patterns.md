---
paths:
  - "**/*.component.ts"
  - "**/*.component.html"
  - "**/*.service.ts"
  - "**/*.store.ts"
  - "**/*.routes.ts"
---
# Angular パターン (Angular Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Angular 固有の内容を追加する。

## Smart / Dumb コンポーネント分離 (Smart / Dumb Component Split)

Smart（コンテナ）コンポーネントはデータ取得と状態を所有します。Dumb（プレゼンテーション）コンポーネントは入力の受け取りと出力の発行のみを行い、サービスの注入は行いません。

```typescript
// Smart — データを所有
@Component({ standalone: true, changeDetection: ChangeDetectionStrategy.OnPush })
export class UserPageComponent {
  private userService = inject(UserService);
  user = toSignal(this.userService.getUser(this.userId));
}
```

```html
<!-- Dumb — 純粋なプレゼンテーション -->
<app-user-card [user]="user()" (select)="onSelect($event)" />
```

## サービスレイヤー (Service Layer)

サービスがすべてのデータアクセスとビジネスロジックを所有します。コンポーネントは委譲のみ — コンポーネント内に `HttpClient` を配置しないでください。

```typescript
@Injectable({ providedIn: 'root' })
export class UserService {
  private http = inject(HttpClient);

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}
```

## `resource` を使用した非同期データ (Async Data with `resource`)

リアクティブな非同期フェッチには `resource()` を使用してください。単純なデータ読み込みには手動の RxJS パイプラインよりも優先してください:

```typescript
export class UserDetailComponent {
  userId = input.required<string>();

  userResource = resource({
    request: () => ({ id: this.userId() }),
    loader: ({ request }) =>
      firstValueFrom(inject(UserService).getUser(request.id)),
  });
}
```

状態へのアクセス: `userResource.value()`、`userResource.isLoading()`、`userResource.error()`、`userResource.reload()`。

## シグナル状態パターン (Signal State Patterns)

```typescript
// ローカルの可変状態
count = signal(0);

// 派生（複製しない）
doubled = computed(() => this.count() * 2);

// ソースとともにリセットされる書き込み可能な派生状態
selectedItem = linkedSignal(() => this.items()[0]);

// Observable からシグナルへのブリッジ
users = toSignal(this.userService.getUsers(), { initialValue: [] });
```

派生値を別のシグナルに格納しないでください — `computed` を使用してください。シグナルの同期に `effect` を使用しないでください — `computed` または `linkedSignal` を使用してください。

## サブスクリプションのクリーンアップ (Subscription Cleanup)

すべての手動サブスクリプションには `takeUntilDestroyed()` を使用してください。新しいコードでは手動の `ngOnDestroy` + `Subject` + `takeUntil` を使用しないでください。

```typescript
export class UserComponent {
  private destroyRef = inject(DestroyRef);

  ngOnInit() {
    this.userService.updates$
      .pipe(takeUntilDestroyed(this.destroyRef))
      .subscribe(update => this.handleUpdate(update));
  }
}
```

## ルーティング (Routing)

### ルート定義 (Route Definition)

```typescript
// app.routes.ts
export const routes: Routes = [
  { path: '', component: HomeComponent },
  {
    path: 'admin',
    canMatch: [authGuard],           // CanMatch はチャンクの読み込み自体を防止
    loadChildren: () => import('./admin/admin.routes').then(m => m.ADMIN_ROUTES),
  },
  {
    path: 'users/:id',
    resolve: { user: userResolver },
    component: UserDetailComponent,
  },
];
```

- 未認証ユーザーに対してルートモジュールを読み込まないようにする場合は `canMatch` を `canActivate` より優先
- すべての機能モジュールを `loadChildren` で遅延読み込み
- コンポーネント内のローディング状態を回避するため `resolve` でデータをプリフェッチ

### 関数型ガード (Functional Guards)

```typescript
export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  return auth.isAuthenticated()
    ? true
    : inject(Router).createUrlTree(['/login']);
};
```

### データリゾルバ (Data Resolvers)

```typescript
export const userResolver: ResolveFn<User> = (route) => {
  return inject(UserService).getUser(route.paramMap.get('id')!);
};
```

### ビュートランジション (View Transitions)

View Transitions API でスムーズなルート遷移を有効化:

```typescript
// app.config.ts
provideRouter(routes, withViewTransitions())
```

## 依存性注入パターン (Dependency Injection Patterns)

### スコープ付きプロバイダ (Scoped Providers)

サービスがシングルトンであるべきでない場合、コンポーネントまたはルートレベルで提供してください:

```typescript
@Component({
  providers: [UserEditService],   // このコンポーネントサブツリーにスコープ
})
export class UserEditComponent {}
```

### `InjectionToken` (`InjectionToken`)

```typescript
export const CONFIG = new InjectionToken<AppConfig>('APP_CONFIG');

// プロバイダ内:
{ provide: CONFIG, useValue: appConfig }
{ provide: CONFIG, useFactory: () => loadConfig(), deps: [] }

// 使用:
private config = inject(CONFIG);
```

### `viewProviders` と `providers` (`viewProviders` vs `providers`)

- `providers`: コンポーネントとそのすべてのコンテンツ子要素で利用可能
- `viewProviders`: コンポーネント自身のビューでのみ利用可能（投影されたコンテンツでは不可）

## HTTP インターセプター (HTTP Interceptors)

認証、エラーハンドリング、リトライには関数型インターセプター（v15+）を使用してください:

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  if (!token) return next(req);
  return next(req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }));
};
```

`app.config.ts` で登録:

```typescript
provideHttpClient(withInterceptors([authInterceptor, errorInterceptor]))
```

## RxJS オペレータ (RxJS Operators)

- `switchMap` — 検索、ナビゲーション（前のリクエストをキャンセル）
- `mergeMap` — 独立した並列リクエスト
- `exhaustMap` — フォーム送信（完了するまで無視）
- 常に `catchError` でエラーを処理 — ストリームを暗黙的に死なせない

```typescript
search$ = this.query$.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(q => this.service.search(q).pipe(catchError(() => of([])))),
);
```

## フォーム (Forms)

プロジェクトの既存のフォーム戦略に合わせてください。v21+ の新規アプリにはシグナルフォームを優先してください。

```typescript
// Reactive Forms — 複雑なフォームの標準
export class UserFormComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    name: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]],
  });
}
```

## レンダリング戦略 (Rendering Strategies)

- **CSR**（デフォルト）: 標準 SPA
- **SSR + ハイドレーション**: `ng add @angular/ssr` — FCP と SEO を改善
- **SSG（プリレンダリング）**: コンテンツの多いルート向けにビルド時に静的ページを生成

SSR を使用する場合、`window`、`document`、`localStorage` を直接使用しないでください — `isPlatformBrowser` または `DOCUMENT` トークンを使用してください。

## アクセシビリティ (Accessibility)

ヘッドレスでアクセシブルなコンポーネント（Accordion、Listbox、Combobox、Menu、Tabs、Toolbar、Tree、Grid）には Angular CDK を使用してください。ARIA 属性を手動で管理するのではなく、スタイルを適用してください:

```css
[aria-selected="true"] { background: var(--color-selected); }
```

## スキルリファレンス (Skill Reference)

シグナル、フォーム、ルーティング、DI、SSR、アクセシビリティパターンの詳細なガイダンスについては、スキル: `angular-developer` を参照してください。
