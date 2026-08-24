---
published: true
layout: post
title:  "Face プロパティエディタ"
author: ishimoto
date:   2026-08-24
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# Face プロパティエディタ

**Face** とは、1 つの TreasureBoat アプリケーションのスキン／バリエーション／テナントのことです。デプロイされた 1 つのアプリが複数のフェイスを提供でき、それぞれが独自のブランディング・設定を持ち、多くの場合は独自のデータの一部も持ちます。（たとえば EdisonSystem は、`acotro`・`cscw`・`lake`・`metlakatla` といったフェイスを 1 つのビルドから動かしています。）

このシリーズでは FaceID がすでに 2 回登場しています — [ナビゲーションエディタ]({% post_url 2026-08-21-ij-navigation-editor-ja %}) では FaceID ごとに独自のメニューを持たせ、[EO マイグレーション]({% post_url 2026-08-17-ij-eo-migration-editor-ja %}) では特定のフェイスを対象にできました。このエディタはその 3 つ目のピース、各フェイスの **設定** を扱います。

## Face プロパティファイル

各フェイスは、`Property/` フォルダ内に `face_{FaceID}.properties` ファイルを持ちます。

```
src/main/resources/
    Property/
        face_default.properties     ← ベース／フォールバック
        face_acotro.properties
        face_lake.properties
```

これは、そのフェイス向けの単純なキー／値の設定です — ベース URL、SMTP アドレス、ロゴ、機能フラグ、テキストの上書きなど。

```
org.treasureboat.core.app.baseUrl             = https://…
org.treasureboat.security.login.logoResource  = face://LogoLogin.png
mu.app.mail.sending.address.from              = office@treasureboat.org
```

> `face://` はフェイスごとのリソース参照です — `face://LogoLogin.png` は *その* フェイスのロゴに解決されるため、各テナントはコードを一切変更せずに独自のロゴを用意できます。

---

## エディタ

![Overview](/assets/FacePropertyEditor/Overview.png)

* **Faces パネル（左）** — `Property/` 内で見つかったすべての FaceID。**+** で新しい `face_{name}.properties` を作成し、**−** で削除します。
* **プロパティテーブル（中央）** — 選択したフェイスのキーと値。その場で編集でき、プロパティの追加・削除もここで行います。
* **比較パネル（右）** — 1 つのプロパティを選ぶと、その値を **他のすべてのフェイスにわたって** 一度に確認できます。ここが便利なところで、あるフェイスにキーが欠けているのか、フェイス間で意図的に異なっているのかが一目で分かります。

**ツールバー:** Save All · Reload · Help。

---

## 知っておくと便利なこと

* **`default` がフォールバックです。** フェイスは `face_default.properties` を継承するので、`face_{X}.properties` にはそのフェイスで異なる分だけを列挙すれば十分です。
* **比較パネル** は一貫性チェックに使えます — SMTP アドレス、URL、機能フラグをフェイス間で並べて、抜けを見つけましょう。
* 各ファイルはたいてい **ロードチェック** 用のキー（`org.treasureboat.loadedFace.Properties = acotro`）から始まります。これにより実行時に、正しいフェイスのプロパティが実際に読み込まれたかを確認できます。
* フェイスは TreasureBoat の軽量なマルチテナンシーです。**1 つのアプリ、1 つのデプロイで、多数のブランド設定** を実現します — これらのファイルに加え、フェイスごとのナビゲーションとデータによって駆動されます。

---

## 関連記事

* [ナビゲーションエディタ]({% post_url 2026-08-21-ij-navigation-editor-ja %}) — FaceID ごとのメニュー
* [EO マイグレーションエディタ]({% post_url 2026-08-17-ij-eo-migration-editor-ja %}) — `Face` の対象指定フィールド
* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `Property/` が置かれる場所

---
