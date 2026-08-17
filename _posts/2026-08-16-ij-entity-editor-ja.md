---
published: true
layout: post
title:  "エンティティエディタ"
author: ishimoto
date:   2026-08-16
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# エンティティエディタ

[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事では、`resources`
フォルダに `.eomodeld`（**EO モデル**）が入っていることを見ました。この記事では、それを
編集するためのエディタを取り上げます。

**EOModel** は TreasureBoat のオブジェクト・リレーショナルマッピングです。エンティティ
（テーブル）、その属性（カラム）、そしてそれらの間のリレーションシップ（結合）を、Java
コード内のアノテーションではなく、宣言的にデータとして記述します。`.eomodeld` を開くと、
プラグインは生の plist ファイルではなく、ビジュアルな **エンティティエディタ** を提供します。

## EOModel とは

`.eomodeld` は単一のファイルではなく、NeXTSTEP 形式の plist ファイルをまとめた
**バンドル**（フォルダ）です。

* `index.eomodeld` – モデルのメタデータ（接続情報、命名規則）
* エンティティごとの `.plist` – そのエンティティの属性・リレーションシップ・フェッチ仕様

これらの plist を手で編集する必要はありません。そのためのエディタです。

![Overview](/assets/EntityEditor/Overview.png)

---

## エディタの構成

エディタには 2 つのパネルがあります。左側の **ツリー** と、右側の **プロパティパネル** です。

### エンティティツリー（左）

ツリーにはモデル全体の構造が表示されます。

* **Model** – `.eomodeld` バンドル（ルート）
  * **Entity** – モデル内の各エンティティ
    * **Attributes** – カラム
    * **Relationships** – 他のエンティティへの結合
    * **Fetch Specifications** – あらかじめ定義された名前付きクエリ
    * **Indexes** – データベースのインデックス

ノードを右クリックすると、エンティティ・属性・リレーションシップ・フェッチ仕様・インデックスの
追加や削除、エンティティの **サブクラス** 作成ができます。

![EntityTreeRightClick](/assets/EntityEditor/EntityTreeRightClick.png)

また、必要に応じて次のファイルも作成できます。  

* Create REST Controller...
* Create Sangria Rules...
* Create Sangria Delegate...

### プロパティパネル（右）

任意のノードを選択すると、右側のパネルにそのプロパティが表示され、編集できます — 属性の
カラムや型、リレーションシップの参照先や削除ルールなどです。

![Layout](/assets/EntityEditor/Layout.png)

---

## ツールバー

| ボタン | 機能 |
|---|---|
| **Save** | すべての変更を `.eomodeld` の plist に書き戻します |
| **Add Attribute** | 選択中のエンティティにカラムを追加します |
| **Add Relationship** | 選択中のエンティティから結合を追加します |
| **Generate Migration** | モデルの変更に対するマイグレーションファイルを作成します（後述） |
| **Generate EO** | モデルから Java クラスを生成します |
| **Verify** | 保存前にモデルの整合性エラーをチェックします |
| **Select in Project View** | プロジェクトビューで `.eomodeld` フォルダにジャンプします |
| **Template Path** | *Generate EO* で使う Velocity テンプレートを選択します |
| **Help** | このエディタの組み込みヘルプを開きます |

![Toolbar](/assets/EntityEditor/Toolbar.png)

---

## 押さえておきたいプロパティ

### エンティティ

| プロパティ | 説明 |
|---|---|
| **name** | エンティティ名（Java クラスに対応します） |
| **tableName** | データベースのテーブル |
| **className** | 完全修飾の Java クラス名 |
| **parentName** | 継承のための親エンティティ |
| **isAbstractEntity** | 抽象エンティティ — 自身のテーブルを持ちません |

#### ベーシック・パネル

![Basic](/assets/EntityEditor/EntBasic.png)

#### アドバンスト・パネル

![Advanced](/assets/EntityEditor/EntAdvanced.png)

#### ローカライズ・パネル

![Localization](/assets/EntityEditor/EntLocalization.png)

### 属性

| プロパティ | 説明 |
|---|---|
| **name** | 属性名（Java のアクセサに対応します） |
| **columnName** | データベースのカラム |
| **isPrimaryKey** | 主キーの一部かどうか |
| **isClassProperty** | Java のプロパティとして公開されるかどうか |
| **allowsNull** | カラムが `NULL` を許可するかどうか |
| **prototype** | データベースのプロトタイプ |

> **知っておくと便利:** **isClassProperty** が付いた属性だけが Java のアクセサを持ちます。
> テーブルには存在するがクラスプロパティではないカラム（たとえば生の外部キーカラム）は、
> モデルには残りますが、EO のメソッドとしては現れません。

> **プロトタイプとは?** プロトタイプは、カラムのデータベース型・Java の値型・幅をまとめた
> 名前付きテンプレートです。それぞれを手で設定する代わりに、プロトタイプ（`uuid`、
> `varchar255`、`date` など）を選ぶと、属性がそれらをすべて引き継ぎます。覚えておきたい
> 落とし穴が 1 つあります。**`uuid`** プロトタイプはカラム名を既定で `uuid` にするため、
> `uuid` の主キーや外部キーでは **`columnName = id`** を明示的に設定しなければなりません。
> さもないと、生成される SQL が *"column uuid does not exist"* で失敗します。

#### ベーシック・パネル

![Basic](/assets/EntityEditor/AttBasic.png)  

#### アドバンスト・パネル

![Advanced](/assets/EntityEditor/AttAdvanced.png)  

#### ドキュメント・パネル

![Documentation](/assets/EntityEditor/AttDocumentation.png)  

### リレーションシップ

| プロパティ | 説明 |
|---|---|
| **name** | リレーションシップ名 |
| **destination** | 参照先のエンティティ |
| **isToMany** | to-one（単一オブジェクト）か to-many（配列）か |
| **deleteRule** | 削除時に相手側をどうするか: `Nullify`、`Cascade`、`Deny`、`No Action` |

#### ベーシック・パネル

![Basic](/assets/EntityEditor/RelBasic.png)

#### アドバンスト・パネル

![Advanced](/assets/EntityEditor/RelAdvanced.png)

---

## エンティティハッシュ

ツールバーには、選択中のエンティティの小さな **ハッシュ** が表示されます。これはエンティティの
構造 — 属性・リレーションシップ・型 — の指紋です。その色は、最後に保存したマイグレーション
以降に構造が変わったかどうかを示します。

* **グレー** – 保存済みのハッシュと一致（構造変更なし）
* **オレンジ** – 保存済みのハッシュと相違（構造が変わった — 新しいマイグレーションがおそらく必要）
* **ブルー** – 新規、保存済みのハッシュがまだない

ハッシュをクリックするとクリップボードにコピーされます。

![Hash](/assets/EntityEditor/Hash.png)

> **知っておくと便利:** ハッシュは、データベースをモデルと同期させておくためのリマインダー
> です。**オレンジ** になったら、それが **Generate Migration** を押す合図です。

---

## モデルからコードへ — Generate EO

**Generate EO** は、**Template Path** で設定した Velocity テンプレートを使って、モデルを Java
に変換します。これが [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事で
触れた `generated/` フォルダを埋めるものです。各エンティティごとに、KVC のアクセサと
リレーションシップを持つ **アンダースコア始まりのベースクラス**（`_MyEntity.java`）が
書き出されます。これらは編集しないでください — 再生成すると上書きされます。あなたの
ビジネスロジックは、`java` フォルダにある、対応する **`MyEntity.java`** サブクラスに書きます。
このクラスは生成されたベースクラスを継承します。

つまり、日々の流れはこうです。

1. ツリーでエンティティを編集する。
2. ハッシュが **オレンジ** になるのを確認する。
3. **Generate Migration** でデータベースを追従させる。
4. **Generate EO** で Java クラスを追従させる。
5. **Save**。

---

## 関連

* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `.eomodeld` が置かれている場所
* **Migration Editor** — このエディタが生成するマイグレーションファイルの編集 *(次の記事)*

---
