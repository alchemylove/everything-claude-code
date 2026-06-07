---
name: nestjs-patterns
description: NestJS architecture patterns for modules, controllers, providers, DTO validation, guards, interceptors, config, and production-grade TypeScript backends.
origin: ECC
---

# NestJS 開発パターン (NestJS Development Patterns)

modular TypeScript backend 向けの本番グレード NestJS pattern。

## 有効化タイミング (When to Activate)

- NestJS API または service の構築
- module、controller、provider の構成
- DTO validation、guard、interceptor、exception filter の追加
- 環境対応 setting と database 統合の設定
- NestJS unit または HTTP endpoint のテスト

## プロジェクト構成 (Project Structure)

```text
src/
├── app.module.ts
├── main.ts
├── common/
│   ├── filters/
│   ├── guards/
│   ├── interceptors/
│   └── pipes/
├── config/
│   ├── configuration.ts
│   └── validation.ts
├── modules/
│   ├── auth/
│   │   ├── auth.controller.ts
│   │   ├── auth.module.ts
│   │   ├── auth.service.ts
│   │   ├── dto/
│   │   ├── guards/
│   │   └── strategies/
│   └── users/
│       ├── dto/
│       ├── entities/
│       ├── users.controller.ts
│       ├── users.module.ts
│       └── users.service.ts
└── prisma/ or database/
```

- domain code は feature module 内に保持
- cross-cutting filter、decorator、guard、interceptor は `common/` に
- DTO は所有 module の近くに

## Bootstrap とグローバル Validation (Bootstrap and Global Validation)

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule, { bufferLogs: true });

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      transformOptions: { enableImplicitConversion: true },
    }),
  );

  app.useGlobalInterceptors(new ClassSerializerInterceptor(app.get(Reflector)));
  app.useGlobalFilters(new HttpExceptionFilter());

  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

- public API では常に `whitelist` と `forbidNonWhitelisted` を有効化
- route ごとに validation config を繰り返すより 1 つの global validation pipe を優先

## Module、Controller、Provider (Modules, Controllers, and Providers)

```ts
@Module({
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get(':id')
  getById(@Param('id', ParseUUIDPipe) id: string) {
    return this.usersService.getById(id);
  }

  @Post()
  create(@Body() dto: CreateUserDto) {
    return this.usersService.create(dto);
  }
}

@Injectable()
export class UsersService {
  constructor(private readonly usersRepo: UsersRepository) {}

  async create(dto: CreateUserDto) {
    return this.usersRepo.create(dto);
  }
}
```

- controller は薄く: HTTP input を parse、provider を呼び、response DTO を返す
- business logic は injectable service に。controller に置かない
- 他 module が本当に必要とする provider のみ export

## DTO と Validation (DTOs and Validation)

```ts
export class CreateUserDto {
  @IsEmail()
  email!: string;

  @IsString()
  @Length(2, 80)
  name!: string;

  @IsOptional()
  @IsEnum(UserRole)
  role?: UserRole;
}
```

- すべての request DTO を `class-validator` で検証
- ORM entity を直接返さず dedicated response DTO または serializer を使用
- password hash、token、audit column など内部 field を漏らさない

## Auth、Guard、Request Context (Auth, Guards, and Request Context)

```ts
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin')
@Get('admin/report')
getAdminReport(@Req() req: AuthenticatedRequest) {
  return this.reportService.getForUser(req.user.id);
}
```

- auth strategy と guard は真に shared でない限り module-local に
- 粗い access rule は guard に、resource 固有 authorization は service に
- authenticated request には明示的 request type を優先

## Exception Filter と Error Shape (Exception Filters and Error Shape)

```ts
@Catch()
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const response = host.switchToHttp().getResponse<Response>();
    const request = host.switchToHttp().getRequest<Request>();

    if (exception instanceof HttpException) {
      return response.status(exception.getStatus()).json({
        path: request.url,
        error: exception.getResponse(),
      });
    }

    return response.status(500).json({
      path: request.url,
      error: 'Internal server error',
    });
  }
}
```

- API 全体で一貫した error envelope を維持
- 想定 client error は framework exception。予期しない failure は central に log と wrap

## Config と環境 Validation (Config and Environment Validation)

```ts
ConfigModule.forRoot({
  isGlobal: true,
  load: [configuration],
  validate: validateEnv,
});
```

- env は boot 時に検証。最初の request まで遅延しない
- typed helper または config service の背後で config にアクセス
- dev/staging/prod は feature code 全体で分岐せず config factory で分離

## 永続化とトランザクション (Persistence and Transactions)

- repository / ORM code は domain 言語を話す provider の背後に
- Prisma または TypeORM では unit of work を所有する service に transactional workflow を隔離
- controller が multi-step write を直接調整しない

## テスト (Testing)

```ts
describe('UsersController', () => {
  let app: INestApplication;

  beforeAll(async () => {
    const moduleRef = await Test.createTestingModule({
      imports: [UsersModule],
    }).compile();

    app = moduleRef.createNestApplication();
    app.useGlobalPipes(new ValidationPipe({ whitelist: true, transform: true }));
    await app.init();
  });
});
```

- provider を mock dependency で unit test
- guard、validation pipe、exception filter の request-level test を追加
- test でも production と同じ global pipe/filter を再利用

## 本番デフォルト (Production Defaults)

- structured logging と request correlation id を有効化
- 無効 env/config では部分 boot せず terminate
- DB/cache client の async provider 初期化と明示 health check を優先
- background job と event consumer は HTTP controller 内ではなく専用 module に
- public endpoint では rate limiting、auth、audit logging を明示
