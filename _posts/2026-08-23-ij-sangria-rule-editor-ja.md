---
published: true
layout: post
title:  "Sangria ルールエディター"
author: ishimoto
date:   2026-08-23
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# Sangria ルールエディター

Sangria（TreasureBoat の D2W — Direct-to-Web）は **ルールベースの UI** です。エンティティごとにページを手作りする代わりに **ルール** を書き、実行時にエンジンがそれらを評価して、表示する要素それぞれに *どのコンポーネント・レイアウト・ラベル・書式を使うか* を決定します。ルールは `.sangria`（または `.d2wmodel`）ファイルに格納され、Sangria ルールエディターは、生の plist ではなくテーブル形式でそれらを編集します。

![Overview](/assets/SangriaEditor/Overview.png)

---

## ルールとは

すべてのルールは **`LHS ⇒ RHS`、そして優先度** から成ります。

* **LHS**（左辺）— ルールが発火する *条件*
* **RHS**（右辺）— *設定するプロパティ* とその値
* **優先度** — 複数のルールがマッチした場合、最も優先度の高いものが採用されます

例えば、*「`TBPolicy` エンティティの edit task で、プロパティの `d2wType` が `policy` のとき、このコンポーネントを使う」* というルールは次のようになります。

```
LHS:  EN = "TBPolicy"  AND  task = edit  AND  d2wType = "policy"
RHS:  componentName = "TBSangria_Edit_policy"
```

### LHS の略語

D2W のキーはファイル内で略語になっています。知っておくと便利です。

| Key | 意味 |
|---|---|
| **EN** | Entity Name（エンティティ名） |
| **PC** | Page Configuration（ページ構成） |
| **PK** | Property Key（プロパティキー） |
| **task** | display / edit / query / list … |
| **d2wType** | プロパティの表示 *タイプ*（string, date, popup, `policy` …） |

条件は `AND` で結合され、それぞれが `key <selector> value` の形です（`isEqualTo`, `isLike` …）。

### RHS

RHS は、値の *計算方法* を決める **assignment クラス** を通じて、1 つのプロパティ（`keyPath`）に値を設定します。

| Assignment | 役割 |
|---|---|
| **Assignment** | そのままの値 |
| **BooleanAssignment** | `true` / `false` |
| **DelayedLocalizedAssignment** | 表示時に解決されるローカライズ文字列 |
| **DelayedObjectCreationAssignment** | クラスをインスタンス化する（例：ページのデリゲート） |

よく使う RHS キー：`componentName`, `d2wType`, `displayNameForPageConfiguration`, `nextPageDelegate`, `DPK`（表示するプロパティの順序付きリスト）。

---

## エディター

![Table](/assets/SangriaEditor/Table.png)

* **ルールテーブル（上部）** — すべてのルールが 1 行ずつ表示されます：`#`, **Priority**, **LHS**, **KeyPath**, **Value**, **Class**。ヘッダーをクリックするとソートでき、**Filter** ボックスで LHS / KeyPath / Value を横断検索できます。
* **ルールプロパティ（左下）** — 選択したルールを編集します：優先度、**LHS qualifier**、**RHS のキー** と **値**、そして **assignment クラス**。
* **エンティティ情報（右下）** — ルールが参照するエンティティのコンテキスト。どのモデルに対して動作しているかが分かります。

**ツールバー：** Add Rule · Remove Rule · Duplicate · Add from Template · Help。

---

## 知っておくと便利なこと

* **優先度はファイル内では `author` として格納されます**（D2W の慣習です）。値が大きいほど先に評価されます。おおまかな慣習として、フレームワークのデフォルトには小さい番号を、アプリ側のオーバーライドには大きい番号を使います。優先度の高いアプリのルールは、フレームワークに手を入れることなく、そのデフォルトを静かに上書きします。
* ルールは **宣言的** です。見た目や振る舞いを変えるには、ページを編集するのではなくルールを追加します。これこそが D2W の狙いです。
* ルールは **無効化**（フラグ）でき、**documentation** のメモを持たせることもできます。どちらもエディターに表示されます。
* **`d2wType`** は、プロパティを *どの* 表示／編集コンポーネントで描画するかを選ぶキーです。組み込みの d2wType（string, integer, date, toOne, toMany, popup …）の完全なカタログは、IDE 内の **Sangria Help** ツールウィンドウにドキュメントがあります。そちらで参照できるため、ここでは再掲しません。

---

## 関連記事

* [Entity Editor]({% post_url 2026-08-16-ij-entity-editor-ja %}) — これらのルールが表示するエンティティ
* [Component Editor]({% post_url 2026-08-20-ij-component-editor-ja %}) — ルールの `componentName` が指すコンポーネント
* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %})

---
