# 画面定義書

生成元: `apps/web/pages/*.vue`。共通レイアウト（ヘッダーナビ）は `apps/web/layouts/default.vue`。未ログイン時はログイン画面へ誘導する。

## 画面一覧

| ルート | 画面名 | 画面ファイル | 主なセクション | 利用API |
|---|---|---|---|---|
| `/accounts` | 口座 | `apps/web/pages/accounts.vue` | - | `/api/accounts`, `/api/institutions`, `/api/accounts/{param}` |
| `/categories` | カテゴリ | `apps/web/pages/categories.vue` | - | `/api/categories`, `/api/categories/{param}` |
| `/` | ダッシュボード | `apps/web/pages/index.vue` | カテゴリ別支出, 口座残高 | `/api/summary/monthly?month={param}`, `/api/accounts` |
| `/login` | Kakeibo | `apps/web/pages/login.vue` | - | `/api/auth/login`, `/api/auth/register` |
| `/transactions` | 取引 | `apps/web/pages/transactions.vue` | - | `/api/transactions?month={param}`, `/api/accounts`, `/api/categories`, `/api/transactions/{param}`, `/api/transactions`, `/api/transactions/import?accountId={param}`, `/api/transactions/export.csv?month={param}` |

## 画面遷移図

```mermaid
flowchart LR
    login["/login ログイン/登録"] -->|ログイン成功| index["/ ダッシュボード"]
    index -->|ヘッダーナビ| accounts["/accounts 口座"]
    index -->|ヘッダーナビ| categories["/categories カテゴリ"]
    index -->|ヘッダーナビ| transactions["/transactions 取引"]
```

## 画面詳細

### /accounts 口座

- 画面ファイル: `apps/web/pages/accounts.vue`
- 入力要素: input type = text / select 2件
- 利用API: `/api/accounts`, `/api/institutions`, `/api/accounts/{param}`

### /categories カテゴリ

- 画面ファイル: `apps/web/pages/categories.vue`
- 入力要素: input type = text / select 1件
- 利用API: `/api/categories`, `/api/categories/{param}`

### / ダッシュボード

- 画面ファイル: `apps/web/pages/index.vue`
- 入力要素: input type = month
- 利用API: `/api/summary/monthly?month={param}`, `/api/accounts`

### /login Kakeibo

- 画面ファイル: `apps/web/pages/login.vue`
- 入力要素: input type = email, password, text
- 利用API: `/api/auth/login`, `/api/auth/register`

### /transactions 取引

- 画面ファイル: `apps/web/pages/transactions.vue`
- 入力要素: input type = date, file, month, number, text / select 5件
- 利用API: `/api/transactions?month={param}`, `/api/accounts`, `/api/categories`, `/api/transactions/{param}`, `/api/transactions`, `/api/transactions/import?accountId={param}`, `/api/transactions/export.csv?month={param}`
