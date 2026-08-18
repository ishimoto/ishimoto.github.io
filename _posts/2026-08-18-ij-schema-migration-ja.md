---
published: true
layout: post
title:  "スキーママイグレーション"
author: ishimoto
date:   2026-08-18
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# スキーママイグレーション

これは **もう一方の** マイグレーション — *データベースの構造* をモデルに合わせて保つためのものです。[EO マイグレーション]({% post_url 2026-08-17-ij-eo-migration-editor-ja %})（データ）エディタと混同しやすいので、まずは 1 行で違いを整理します。

| | **スキーママイグレーション** *(この記事)* | EO マイグレーション |
|---|---|---|
| 作成方法 | [Entity Editor]({% post_url 2026-08-16-ij-entity-editor-ja %}) の **Generate Migration** | Migration Editor |
| 生成物 | **`.java`** クラス | `.xml` ファイル |
| 目的 | テーブル・カラム・外部キー・インデックスを作成 — DB の **形** | 起動時にデータを作成／更新／削除 |

エンティティを変更して [ハッシュがオレンジ色になったら]({% post_url 2026-08-16-ij-entity-editor-ja %})、スキーママイグレーションでデータベースを追従させます。

## Generate Migration

Entity Editor のツールバーで **Generate Migration** を押すと、次のダイアログが表示されます。

![GenerateMigration](/assets/SchemaMigration/GenerateMigration.png)

* **Package** – 生成されるクラスの配置先（デフォルトはモデルの `…migrations` パッケージ）
* **Migration Number** – このマイグレーションの連番（後述の *採番* を参照）
* **Entities to include** – このマイグレーションが対象とするエンティティにチェックを入れます（デフォルトは *Select all*）
* **Preview** – 実際に書き出される Java。オプションを変更するとその場で更新されます

### オプション

* **Model Dependencies** – このマイグレーションが他のモデルのマイグレーションに依存することを宣言し、モデルをまたいだ順序を正しく保ちます
* **New Automatic** – すべてのカラムを書き下す代わりに、現在のモデルバージョンからスキーマを導出する *自動* マイグレーションを生成します
* **Transformation** – 変換フックを含めます（構造変更に伴ってデータを整形するため）
* **Run Migration** – SQL データを読み込むエリア（各データベースのコールバック有り）

---

## 生成される内容

スキーママイグレーションは実際の **Java クラス** です。名前はモデルのプレフィックス + マイグレーション番号（例：`TBTag0`）で、`TBEnterpriseMigrationDatabase.Migration` を継承します。1 つのマイグレーションクラスが、チェックしたすべてのエンティティをカバーします。処理は **3 つのパス** に分かれています。

```java
public class TBTag0 extends TBEnterpriseMigrationDatabase.Migration {

    @Override
    public void upgrade(TBEnterpriseEditingContext ec, TBEnterpriseMigrationDatabase database) {
        var table = database.newTableByEntity(TBTagStore.clazz.entity());
        table.newStringColumn("name_en", 255, NOT_NULL);
        table.newBooleanColumn("locked", NOT_NULL);
        // … one table per entity, each with its columns
    }

    @Override
    public void foreignKeyUpgrade(TBEnterpriseEditingContext ec, TBEnterpriseMigrationDatabase database) {
        var table = database.existingTableByEntity(TBTagConnection.clazz.entity());
        table.addForeignKey("idTag", TBTagStore.clazz.entity(), "id");
    }

    @Override
    public void indexUpgrade(TBEnterpriseEditingContext ec, TBEnterpriseMigrationDatabase database) {
        safeAddIndex(database, TBTagStore.clazz.entity(), _TBTagStore.NAME_EN_KEY);
    }
}
```

* **`upgrade`** – テーブルとそのカラムを作成します（`newStringColumn`・`newIntegerColumn`・`newBooleanColumn` … に `NOT_NULL` / `ALLOWS_NULL` を指定）
* **`foreignKeyUpgrade`** – 外部キーを追加します
* **`indexUpgrade`** – インデックスを追加します（およびデータベース固有のチューニング）

> **なぜ 3 パスなのか？** 外部キーは、すべてのテーブルが存在した **後** に追加されます — まだ作成されていないテーブルを外部キーで参照することはできないからです。そこで、まずすべてのテーブルとカラムを作成し（`upgrade`）、次にそれらを結ぶキーを追加し（`foreignKeyUpgrade`）、最後にインデックスを追加します。

---

## 採番

EO マイグレーションと同様に、**番号は恒久的で順序を持つ履歴** です。`TBTag0` はマイグレーション `0` で、そのモデルへの次の構造変更は **新しい** クラス `TBTag1`、その次は `TBTag2`、というように続きます。すでにどこかで実行済みのマイグレーションは決して編集せず、次の番号を追加します。フレームワークは各データベースがどの番号まで進んでいるかを記録し、まだ適用していないものだけを実行します。

EO マイグレーション（Migration Editor で編集する `.xml` ファイル）とは異なり、スキーママイグレーションは **素の Java** です。生成後は通常の Java エディタで開いて手動で調整できます。

---

## 関連記事

* [Entity Editor]({% post_url 2026-08-16-ij-entity-editor-ja %}) — **Generate Migration** がある場所
* [EO Migration Editor]({% post_url 2026-08-17-ij-eo-migration-editor-ja %}) — *データ* のマイグレーション、もう一方の種類
* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %})

---
