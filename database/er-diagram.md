# ER図

生成元: `db/migration/V*.sql`（全マイグレーション適用後）の外部キー制約。

```mermaid
erDiagram
    users {
        BIGINT_UNSIGNED id PK
        VARCHAR_255 email UK
        VARCHAR_100 display_name
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    households {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED owner_user_id FK
        VARCHAR_100 name
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    financial_institutions {
        BIGINT_UNSIGNED id PK
        VARCHAR_50 code UK
        VARCHAR_100 name
        ENUM_bank_credit_union_credit_card_e_money_securities_other kind
        TIMESTAMP created_at
    }
    accounts {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED institution_id FK
        VARCHAR_100 name
        ENUM_cash_bank_credit_card_e_money_securities_other kind
        TIMESTAMP created_at
        TIMESTAMP updated_at
        BIGINT balance_offset
    }
    categories {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED parent_id FK
        VARCHAR_100 name
        INT sort_order
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    transactions {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED account_id FK
        BIGINT_UNSIGNED to_account_id FK
        BIGINT_UNSIGNED category_id FK
        ENUM_income_expense_transfer_adjustment type
        BIGINT amount
        DATE transacted_on
        VARCHAR_500 memo
        TIMESTAMP deleted_at
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    budgets {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED category_id FK
        CHAR_7 target_month UK
        BIGINT amount
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    import_jobs {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED account_id FK
        BIGINT_UNSIGNED institution_id FK
        ENUM_generic_csv_institution_csv_manual source
        ENUM_pending_done_failed status
        INT imported_count
        VARCHAR_1000 error_message
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    institution_connections {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED household_id FK
        BIGINT_UNSIGNED institution_id FK
        VARCHAR_100 connector_type
        VARCHAR_255 external_ref
        ENUM_active_revoked status
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
    login_codes {
        BIGINT_UNSIGNED id PK
        VARCHAR_255 email
        CHAR_64 code_hash
        VARCHAR_100 display_name
        INT attempts
        TIMESTAMP expires_at
        TIMESTAMP consumed_at
        TIMESTAMP created_at
    }
    passkey_credentials {
        BIGINT_UNSIGNED id PK
        BIGINT_UNSIGNED user_id FK
        VARCHAR_255 credential_id UK
        TEXT public_key_cose
        BIGINT_UNSIGNED sign_count
        VARCHAR_100 name
        TIMESTAMP created_at
        TIMESTAMP last_used_at
    }
    webauthn_challenges {
        BIGINT_UNSIGNED id PK
        VARCHAR_255 challenge UK
        BIGINT_UNSIGNED user_id
        VARCHAR_20 kind
        TEXT request_json
        TIMESTAMP expires_at
        TIMESTAMP created_at
    }
    users ||--o{ households : "fk_households_owner: owner_user_id"
    households ||--o{ accounts : "fk_accounts_household: household_id"
    financial_institutions ||--o{ accounts : "fk_accounts_institution: institution_id"
    households ||--o{ categories : "fk_categories_household: household_id"
    categories ||--o{ categories : "fk_categories_parent: parent_id"
    households ||--o{ transactions : "fk_transactions_household: household_id"
    accounts ||--o{ transactions : "fk_transactions_account: account_id"
    accounts ||--o{ transactions : "fk_transactions_to_account: to_account_id"
    categories ||--o{ transactions : "fk_transactions_category: category_id"
    households ||--o{ budgets : "fk_budgets_household: household_id"
    categories ||--o{ budgets : "fk_budgets_category: category_id"
    households ||--o{ import_jobs : "fk_import_jobs_household: household_id"
    accounts ||--o{ import_jobs : "fk_import_jobs_account: account_id"
    financial_institutions ||--o{ import_jobs : "fk_import_jobs_institution: institution_id"
    households ||--o{ institution_connections : "fk_ic_household: household_id"
    financial_institutions ||--o{ institution_connections : "fk_ic_institution: institution_id"
    users ||--o{ passkey_credentials : "fk_passkey_user: user_id"
```

## リレーション一覧

| 制約名 | 参照元 | 参照先 |
|---|---|---|
| fk_households_owner | households.owner_user_id | users.id |
| fk_accounts_household | accounts.household_id | households.id |
| fk_accounts_institution | accounts.institution_id | financial_institutions.id |
| fk_categories_household | categories.household_id | households.id |
| fk_categories_parent | categories.parent_id | categories.id |
| fk_transactions_household | transactions.household_id | households.id |
| fk_transactions_account | transactions.account_id | accounts.id |
| fk_transactions_to_account | transactions.to_account_id | accounts.id |
| fk_transactions_category | transactions.category_id | categories.id |
| fk_budgets_household | budgets.household_id | households.id |
| fk_budgets_category | budgets.category_id | categories.id |
| fk_import_jobs_household | import_jobs.household_id | households.id |
| fk_import_jobs_account | import_jobs.account_id | accounts.id |
| fk_import_jobs_institution | import_jobs.institution_id | financial_institutions.id |
| fk_ic_household | institution_connections.household_id | households.id |
| fk_ic_institution | institution_connections.institution_id | financial_institutions.id |
| fk_passkey_user | passkey_credentials.user_id | users.id |
