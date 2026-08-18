---
published: true
layout: post
title:  "EO マイグレーションエディタ"
author: ishimoto
date:   2026-08-17
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# EO マイグレーションエディタ

TreasureBoat には名前に「マイグレーション」と付くものが **2 つ** あり、混同しがちです。
まずはこの 2 つを区別しておきましょう。この記事で扱うのは 2 つ目だけです。

| | スキーママイグレーション | **EO マイグレーション** *(この記事)* |
|---|---|---|
| 作成方法 | [Entity Editor]({% post_url 2026-08-16-ij-entity-editor-ja %}) の **Generate Migration** ボタン | **Migration Editor**（`.xml` ファイルを開きます） |
| 生成物 | **`.java`** クラス | **`.xml`** ファイル |
| 目的 | **データベースの構造** を同期する — テーブル作成、カラム追加、インデックス、外部キー | 起動時に **データ**（EO インスタンス）を作成・更新・削除する |

つまり、スキーママイグレーションはデータベースの *形* を変え、**EO マイグレーションはそこにデータを入れます**（あるいはデータを変更・削除します）。このエディタは後者、すなわち [Project Layout]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事で見た `EOMigration/` や `EOTestMigration/` の中にある `.xml` ファイルを扱うものです。

## EO マイグレーションとは

EO マイグレーションとは、**アプリケーションの起動時** に実行され、Enterprise Object を作成・更新・削除する小さな `.xml` ファイルです。アプリに存在していてほしいシードデータ — CMS ページ、ロール、ポリシー、ナビゲーションバー、スケジュールジョブなど — を用意します。

* ファイルは `EOMigration/`（全環境）または `EOTestMigration/`（開発・テスト専用）に置かれます。
* **番号付き**（`1001_…`、`0101_…`）で、**順番どおり** に実行されます。
* 一度リリースしたマイグレーションは **変更不可** です — 古いファイルは決して編集せず、変更は **新しい** 番号のファイルに入れます。（番号は恒久的で順序を持った履歴です。）

![Overview](/assets/EOMigrationEditor/Overview.png)

---

## File Settings

各ファイルは **1 つの操作ブロック** であり、エディタ上部で設定します。

* **Root Type** – ファイル全体の操作：**Create**、**Update**、**Delete**、または **SaveChanges**
* **Target** – 適用する環境。例：`dev,common,deploy`
* **Condition** – **冪等性のためのガード**：条件が真のときだけブロックが実行されます。典型的には `!hasEoForKey('TBPolicy.<uuid>')` — *「まだ存在しない場合だけ作成する」* という意味です。これにより、起動のたびにマイグレーションが実行されてもデータが重複しないようになっています。
* **Version** – *(任意)* マイグレーションが対象とするモデルバージョン
* **Face** – *(任意)* 特定のフェイスに限定します。例：`{FaceId1},{FaceId2}`
* **LockingEO** – *(任意)* マイグレーション実行中にロックするストアドプロパティ

![FileSettings](/assets/EOMigrationEditor/FileSettings.png)

> **知っておくと便利:** **1 ファイルにつき操作ブロックは 1 つ** にしてください。1 つのファイルを
> 複数のブロックに分けると確実には実行されません — 別の番号のファイルに分けましょう。

---

## EO リスト（左）

設定の下には、操作の対象となる Enterprise Object の一覧が表示されます。各行が 1 つのオブジェクトで、`Entity (identifier)` の形式で表示されます。例：`TBPolicy (show.navigation)`。リスト上部の小さなツールバーで行の追加・削除・並べ替えができます。

![Tree](/assets/EOMigrationEditor/Tree.png)

---

## Properties / XML Preview（右）

行を選択すると、右側に 2 つのタブが表示されます。

* **Properties** – オブジェクトを編集します：
  * **Entity** – EO のエンティティ種別（例：`TBKVDataStorage`）
  * **`_qualifier`** – このオブジェクトを **識別する** キー。`Create` では `Condition` のチェックに使われる識別子であり、`Update`/`Delete` では変更対象の既存オブジェクトを **見つける** ためのキーです。
  * **Attribute / Value** テーブル – 設定するフィールドの値
* **XML Preview** – ディスクに書き込まれる実際の XML。保存する前に一度目を通しておくとよいでしょう。

![Properties](/assets/EOMigrationEditor/Properties.png)

---

## 4 つの操作

| Root Type | 内容 |
|---|---|
| **Create** | 新しい EO インスタンスを挿入します（`Condition` によって重複しないようにガードされます） |
| **Update** | 既存インスタンスを変更します（`_qualifier` で検索） |
| **Delete** | 既存インスタンスを削除します（`_qualifier` で検索） |
| **SaveChanges** | 蓄積された変更をコミットします |

---

## 関連

* [Entity Editor]({% post_url 2026-08-16-ij-entity-editor-ja %}) — その **Generate Migration** ボタン（もう一方の *スキーマ* マイグレーション）
* [Project Layout]({% post_url 2026-08-15-ij-project-layout-ja %}) — `EOMigration/` と `EOTestMigration/` の場所
* **スキーママイグレーション** — スキーママイグレーションファイルの編集 *(次の記事)*

---
