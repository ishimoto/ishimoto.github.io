---
published: true
layout: post
title:  "Localization Editor（ローカライズエディタ）"
author: ishimoto
date:   2026-08-22
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# Localization Editor（ローカライズエディタ）

TreasureBoat では、Apple 形式の **`.strings`** ファイルを言語ごとに 1 つ用意し、
`.lproj` フォルダの中に置いてローカライズします（[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %})
の記事を参照）。

```
src/main/resources/
    English.lproj/Localizable.strings
    Japanese.lproj/Localizable.strings
    German.lproj/Localizable.strings
```

`.strings` ファイルは、単なるキーと値のペアです。

```
/* a comment */
"login.title"    = "Sign In";
"login.username" = "Username";
```

複数の言語を手作業で編集し、それぞれのキーを同期し続けるのは面倒です。そこで
Localization Editor では、**比較（compare）** と **同期（sync）** を組み込んだテーブルを使えます。

![Overview](/assets/LocalizationEditor/Overview.png)

---

## レイアウト

* **左 — キー一覧。** すべてのキーと値が並び、**フィルター** ボックスが付いています。未翻訳の値はハイライトされるので、抜けがひと目で分かります。
* **右 — 編集と比較。** 選択した値を編集でき（複数行にも対応）、**比較言語（Comparison Language）** を選ぶと、別の言語の値を横に並べて確認できます。

---

## 時間を節約してくれる 2 つの機能

### 比較（Compare）

比較言語を選ぶと、テーブルに現在の言語の値の隣にその言語の値を表示する列が追加されます。参照しながら翻訳でき、まだ足りていない箇所もひと目で分かります。

### Sync Keys

**Sync Keys** は、一方の言語には存在するがもう一方には無いキーを見つけ、足りないキーを（空の値で）**両方の** ファイルに追加し、いくつ追加したかを報告します。新しいキーを追加したら実行しておけば、どの言語ファイルも揃った状態を保てます。

![Compare](/assets/LocalizationEditor/Compare.png)

---

## 知っておくとよいこと

* **Delete と Delete from All** — *Delete* は現在の言語だけからキーを削除します。*Delete from All* は **すべての** 言語ファイルから削除します。後者は慎重に使ってください。
* **階層的なキー**（`login.title`、`login.button.submit`）を使いましょう。自然にグループ化され、コード上でも読みやすくなります。
* 値の中で改行を入れるには `\n` を、引用符をエスケープするには `\"` を使います。
* このエディタが編集するのは *文字列そのもの* です。コンポーネントや Java からは、**localizer** を通して *読み込みます*（例：`$localizer.login.title`）。多言語コンポーネントではなく localizer クラスを使う点については、[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事の補足を参照してください。

---

## 関連記事

* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `.lproj` フォルダの置き場所

---
