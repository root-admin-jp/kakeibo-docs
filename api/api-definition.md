# API定義書

生成元: `apps/api` の Controller。認証は JWT (Bearer)。OpenAPI の正は API 起動時の `/swagger-ui.html`、本書は一覧用の生成ドキュメント。
エンドポイント数: 23

## Account (`AccountController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/accounts` | list |  |
| POST | `/api/accounts` | create |  |
| PUT | `/api/accounts/{id}` | update |  |
| DELETE | `/api/accounts/{id}` | delete |  |

DTO `AccountRequest`: `val institutionId: Long?`, `@field:NotBlank val name: String`, `@field:NotBlank val kind: String`

DTO `AccountResponse`: `val id: Long`, `val institutionId: Long?`, `val name: String`, `val kind: String`, `val balance: Long`

## Auth (`AuthController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| POST | `/api/auth/request-code` | requestCode |  |
| POST | `/api/auth/verify-code` | verifyCode |  |

DTO `RequestCodeRequest`: `@field:Email @field:NotBlank val email: String`, `// 新規登録用（任意）。検証成功時に未登録メールならこの名前でアカウントを作成する。
    @field:Size(max = 100) val displayName: String? = null`

DTO `VerifyCodeRequest`: `@field:Email @field:NotBlank val email: String`, `@field:NotBlank @field:Pattern(regexp = "\\d{6}") val code: String`

DTO `RequestCodeResponse`: `val sent: Boolean = true`

DTO `AuthResponse`: `val token: String`, `val userId: Long`, `val householdId: Long`, `val email: String`, `val displayName: String`

## Passkey (`PasskeyController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| POST | `/api/passkeys/register/options` | registerOptions | application/json |
| POST | `/api/passkeys/register/verify` | registerVerify |  |
| GET | `/api/passkeys` | list |  |
| DELETE | `/api/passkeys/{id}` | delete |  |
| POST | `/api/auth/passkey/login/options` | loginOptions | application/json |
| POST | `/api/auth/passkey/login/verify` | loginVerify |  |

DTO `PasskeyResponse`: `val id: Long`, `val name: String`, `val createdAt: LocalDateTime?`, `val lastUsedAt: LocalDateTime?`

DTO `RegisterVerifyRequest`: `@field:Size(max = 100) val name: String? = null`, `@field:NotBlank val challenge: String`, `val response: JsonNode`

DTO `LoginVerifyRequest`: `@field:NotBlank val challenge: String`, `val response: JsonNode`

## Category (`CategoryController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/categories` | list |  |
| POST | `/api/categories` | create |  |
| DELETE | `/api/categories/{id}` | delete |  |

DTO `CategoryRequest`: `val parentId: Long?`, `@field:NotBlank val name: String`, `val sortOrder: Int = 0`

DTO `CategoryResponse`: `val id: Long`, `val parentId: Long?`, `val name: String`, `val sortOrder: Int`

## Csv (`CsvController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/transactions/export.csv` | export | text/csv |
| POST | `/api/transactions/import` | import | multipart/form-data |

DTO `ImportResult`: `val imported: Int`, `val skipped: Int`, `val errors: List<String>`

## Institution (`InstitutionController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/institutions` | list |  |

DTO `InstitutionResponse`: `val id: Long`, `val code: String`, `val name: String`, `val kind: String`

## Summary (`SummaryController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/summary/monthly` | monthly |  |

DTO `CategoryTotal`: `val categoryName: String`, `val total: Long`

DTO `MonthlySummary`: `val month: String`, `val totalIncome: Long`, `val totalExpense: Long`, `val balance: Long`, `val expenseByCategory: List<CategoryTotal>`, `val incomeByCategory: List<CategoryTotal>`

## Transaction (`TransactionController.kt`)

| メソッド | パス | ハンドラ | 備考 |
|---|---|---|---|
| GET | `/api/transactions` | list |  |
| POST | `/api/transactions` | create |  |
| PUT | `/api/transactions/{id}` | update |  |
| DELETE | `/api/transactions/{id}` | delete |  |

DTO `TransactionRequest`: `val accountId: Long`, `val toAccountId: Long? = null`, `val categoryId: Long? = null`, `@field:NotBlank val type: String`, `@field:Positive val amount: Long`, `val transactedOn: LocalDate`, `val memo: String? = null`

DTO `TransactionResponse`: `val id: Long`, `val accountId: Long`, `val toAccountId: Long?`, `val categoryId: Long?`, `val type: String`, `val amount: Long`, `val transactedOn: LocalDate`, `val memo: String?`
