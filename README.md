# Kakeibo 設計書

このリポジトリは **自動生成** です。`kakeibo` 本体リポジトリの main が更新されるたびに、
GitHub Actions がソース（DDL・コード・本体文書）から再生成して上書きします。直接編集しないでください。

生成元コミット: `b226536c0910f46f462d92306278c5b7c885af91`（kakeibo main）

## 設計書一覧

| ドキュメント | 内容 | 生成元 |
|---|---|---|
| [テーブル定義書](database/table-definition.md) | 全テーブル・全カラム・キー・FK・初期データ | DDL (Flyway) |
| [ER図](database/er-diagram.md) | Mermaid ER図 + リレーション一覧 | DDL の FK 制約 |
| [API定義書](api/api-definition.md) | エンドポイント一覧 + DTO | API Controller |
| [画面定義書](screens/screen-definition.md) | 画面一覧・項目・利用API・画面遷移図 | Nuxt pages |
| [要件定義](requirements/requirements.md) | 要件定義書（本体から複写） | docs/requirements |
| [決定事項ログ](decisions.md) | 確定した決定の一覧（本体から複写） | docs/decisions.md |

## システム概要

- API: Kotlin / Spring Boot 3 / MyBatis (ADR-0009) / REST + OpenAPI、DB: MySQL 8 (Flyway)
- UI: Nuxt 3 + TypeScript + SSR / Tailwind、UIとAPIは分離しモノレポ構成
- 認証: メール + パスワード (JWT)。1ユーザー = 1テナントでデータ分離、共有・招待なし
- 金融機関連携: Phase 1 は CSV 取り込み（MF形式互換）。銀行パスワードを預かる方式は取らない

## ADR 一覧

- [ADR-0001 モノレポ構成にする](adr/0001-monorepo.md)
- [ADR-0002 APIは Kotlin + Spring Boot 3](adr/0002-api-kotlin-spring-boot.md)
- [ADR-0003 DBは MySQL 8 + Flyway](adr/0003-mysql-flyway.md)
- [ADR-0004 UI/API分離と OpenAPI 契約](adr/0004-openapi-contract.md)
- [ADR-0005 シークレット管理 — クレデンシャルはコミットしない](adr/0005-secrets.md)
- [ADR-0006 金融機関連携 — 差し替え可能な InstitutionConnector と Phase 1 CSV](adr/0006-institution-connector.md)
- [ADR-0007 テナント分離 (将来公開前提) と共有なし](adr/0007-tenancy-no-sharing.md)
- [ADR-0008 DBアクセスは当面 JdbcTemplate とし、jOOQコード生成は保留する](adr/0008-jdbc-template.md)
- [ADR-0009 DBアクセスは MyBatis を使う（jOOQ / JdbcTemplate から変更）](adr/0009-mybatis.md)
- [ADR-0010 設計書は専用リポジトリへ自動生成・自動同期する](adr/0010-design-docs-repo.md)
- [ADR-0011 Spring Boot を 3.5.x へアップグレードする](adr/0011-spring-boot-35-upgrade.md)
- [ADR-0012 Nuxt を 4.x へ移行する](adr/0012-nuxt4-migration.md)
- [ADR-0013 Spring Boot を 4.1.x へアップグレードする](adr/0013-spring-boot-4-upgrade.md)
- [ADR-0014 認証をパスワードレス（メール OTP ＋ パスキー）に移行する](adr/0014-passwordless-auth.md)
- [ADR-0015 口座の「現在の残高」設定（残高調整）を追加する](adr/0015-account-balance-adjustment.md)
