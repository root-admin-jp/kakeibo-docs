# テーブル定義書

生成元: `db/migration/V1__init.sql`（Flyway DDL）。金額は JPY の整数。金融機関の認証情報は保存しない。
テナント分離: 全業務テーブルは `household_id` で分離し、1テナント = 1オーナーユーザー（ADR-0007）。

テーブル数: 9

| テーブル | カラム数 | 主キー | 備考 |
|---|---|---|---|
| [users](#users) | 6 | id | ログインユーザー |
| [households](#households) | 5 | id | テナント（1オーナー専用） |
| [financial_institutions](#financial_institutions) | 5 | id | 金融機関マスタ（初期データあり） |
| [accounts](#accounts) | 7 | id | 口座（残高は取引から算出） |
| [categories](#categories) | 7 | id | カテゴリ（2階層: parent_id で親子） |
| [transactions](#transactions) | 12 | id | 取引（収入/支出/振替、論理削除） |
| [budgets](#budgets) | 7 | id | 予算（カテゴリ×月） |
| [import_jobs](#import_jobs) | 10 | id | CSV取込ジョブ履歴 |
| [institution_connections](#institution_connections) | 8 | id | 金融機関連携（外部参照のみ保持） |

## users

ログインユーザー

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | email | VARCHAR(255) | 不可 | - | UNIQUE(uq_users_email) |
| 3 | password_hash | VARCHAR(255) | 不可 | - |  |
| 4 | display_name | VARCHAR(100) | 不可 | - |  |
| 5 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 6 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## households

テナント（1オーナー専用）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | owner_user_id | BIGINT UNSIGNED | 不可 | - | FK → users.id, UNIQUE(uq_households_owner) |
| 3 | name | VARCHAR(100) | 不可 | - |  |
| 4 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 5 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## financial_institutions

金融機関マスタ（初期データあり）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | code | VARCHAR(50) | 不可 | - | UNIQUE(uq_institutions_code) |
| 3 | name | VARCHAR(100) | 不可 | - |  |
| 4 | kind | ENUM('bank','credit_union','credit_card','e_money','securities','other') | 不可 | - |  |
| 5 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |

## accounts

口座（残高は取引から算出）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id |
| 3 | institution_id | BIGINT UNSIGNED | 可 | - | FK → financial_institutions.id |
| 4 | name | VARCHAR(100) | 不可 | - |  |
| 5 | kind | ENUM('cash','bank','credit_card','e_money','securities','other') | 不可 | - |  |
| 6 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 7 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## categories

カテゴリ（2階層: parent_id で親子）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id |
| 3 | parent_id | BIGINT UNSIGNED | 可 | - | FK → categories.id |
| 4 | name | VARCHAR(100) | 不可 | - |  |
| 5 | sort_order | INT | 不可 | 0 |  |
| 6 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 7 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## transactions

取引（収入/支出/振替、論理削除）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id |
| 3 | account_id | BIGINT UNSIGNED | 不可 | - | FK → accounts.id |
| 4 | to_account_id | BIGINT UNSIGNED | 可 | - | FK → accounts.id |
| 5 | category_id | BIGINT UNSIGNED | 可 | - | FK → categories.id |
| 6 | type | ENUM('income','expense','transfer') | 不可 | - |  |
| 7 | amount | BIGINT | 不可 | - |  |
| 8 | transacted_on | DATE | 不可 | - |  |
| 9 | memo | VARCHAR(500) | 可 | - |  |
| 10 | deleted_at | TIMESTAMP | 可 | - |  |
| 11 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 12 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

インデックス: idx_transactions_household_date(household_id, transacted_on), idx_transactions_account(account_id)

## budgets

予算（カテゴリ×月）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id, UNIQUE(uq_budgets) |
| 3 | category_id | BIGINT UNSIGNED | 不可 | - | FK → categories.id, UNIQUE(uq_budgets) |
| 4 | year_month | CHAR(7) | 不可 | - | UNIQUE(uq_budgets) |
| 5 | amount | BIGINT | 不可 | - |  |
| 6 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 7 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## import_jobs

CSV取込ジョブ履歴

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id |
| 3 | account_id | BIGINT UNSIGNED | 可 | - | FK → accounts.id |
| 4 | institution_id | BIGINT UNSIGNED | 可 | - | FK → financial_institutions.id |
| 5 | source | ENUM('generic_csv','institution_csv','manual') | 不可 | 'generic_csv' |  |
| 6 | status | ENUM('pending','done','failed') | 不可 | 'pending' |  |
| 7 | imported_count | INT | 不可 | 0 |  |
| 8 | error_message | VARCHAR(1000) | 可 | - |  |
| 9 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 10 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## institution_connections

金融機関連携（外部参照のみ保持）

| # | カラム | 型 | NULL | デフォルト | その他 |
|---|---|---|---|---|---|
| 1 | id | BIGINT UNSIGNED | 不可 | - | PK, AUTO_INCREMENT |
| 2 | household_id | BIGINT UNSIGNED | 不可 | - | FK → households.id |
| 3 | institution_id | BIGINT UNSIGNED | 不可 | - | FK → financial_institutions.id |
| 4 | connector_type | VARCHAR(100) | 不可 | - |  |
| 5 | external_ref | VARCHAR(255) | 可 | - |  |
| 6 | status | ENUM('active','revoked') | 不可 | 'active' |  |
| 7 | created_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP |  |
| 8 | updated_at | TIMESTAMP | 不可 | CURRENT_TIMESTAMP (更新時も) |  |

## 初期データ: financial_institutions

| code | 名称 | 種別 |
|---|---|---|
| aeon_bank | イオン銀行 | bank |
| mizuho_bank | みずほ銀行 | bank |
| paypay_bank | PayPay銀行 | bank |
| shizuoka_bank | 静岡銀行 | bank |
| hamamatsu_iwata_shinkin | 浜松磐田信用金庫 | credit_union |
| sbi_securities | SBI証券 | securities |
| mobile_waon | モバイルWAON | e_money |
| mobile_suica | モバイルSuica | e_money |
