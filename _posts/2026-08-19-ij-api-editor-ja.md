---
published: true
layout: post
title:  "API エディター"
author: ishimoto
date:   2026-08-19
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# API エディター

[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事で、コンポーネントが
`.html`・`.wod`・任意の `.api`・任意の `.md` を持つ `.wo` フォルダであることを見ました。この記事では
その **`.api`** ファイルを取り上げます。`.api` はコンポーネントの一部なので、これは Component Editor
の予習にもなります。

`.api` ファイルは、コンポーネントの **公開バインディング**（そのインターフェース）を宣言します。つまり
*「これが受け付けるバインディングで、これは必須、これは真偽値を期待する」* ということを表します。必ずしも
必要ではありませんが、あると各種ツールがそれを活用します。WOD エディターはコンポーネントのバインディングに
対して **オートコンプリートとバリデーション** を提供し、Component Editor はそのコンポーネントが何を期待して
いるかを把握できます。

## `.api` ファイルの中身

小さな XML ファイルです。API エディターを使えばフォームとして編集できるので、XML を手で触る必要はありません。
実際に書き出されるのは次のような内容です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tbdefinitions>
    <tb class="MyComponent.java" tbcomponentcontent="false">
        <binding name="editingContext"/>
        <binding defaults="Boolean" name="allowHiding"/>
        <validation message="'editingContext' is a required binding.">
            <unbound name="editingContext"/>
        </validation>
    </tb>
</tbdefinitions>
```

* `<tb class="…">` – この API が属するコンポーネント
* `tbcomponentcontent` – コンポーネントが他のコンテンツを **ラップ** できるかどうか（HTML のコンテナのように）
* `<binding name="…">` – 公開バインディングごとに 1 つ。`defaults="…"` はその *Value Set* を記録します
* `<validation>` / `<unbound>` – **必須** バインディングの表し方

---

## エディターの構成

### Component Content

上部のチェックボックスで **`tbcomponentcontent`** を設定します。コンポーネントが親から渡されたコンテンツを
ラップする場合にオンにします。

### バインディング一覧

コンポーネントの公開バインディングの一覧です。**必須** のバインディングは **太字** で表示されます。
**Add** / **Remove** で追加・削除します。

### バインディングの詳細

バインディングを選択すると、右側のパネルに次の項目が表示されます。

| 項目 | 説明 |
|---|---|
| **Name** | バインディング名 — 値を渡すために `.wod` で使うのと同じ名前です |
| **Value Set** | バインディングが期待する値の種類（`.wod` でのオートコンプリートに使われます） |
| **Required** | 親が必ず指定しなければならないかどうか（`<validation>`／`<unbound>` ブロックを書き出します） |
| **Will Set** | コンポーネントが親へ値を **戻す** かどうか（双方向バインディング） |

![Layout](/assets/ApiEditor/Layout.png)

---

## Value Set の選択肢

**Value Set** は、そのバインディングが何を期待するかをエディターに伝えるもので、`.wod` エディターが
オートコンプリートできるようにします。

| Value Set | 期待する値 |
|---|---|
| **Undefined** | 何でも |
| **Boolean Set** | `true` / `false` |
| **Actions** | ページやアクション結果を返すメソッド |
| **Pages** | TBComponent のページ名 |
| **Frameworks** | フレームワークのバンドル名 |
| **Date Format Strings** | 日付フォーマットのパターン |
| **Number Format Strings** | 数値フォーマットのパターン |
| **MIME Types** | MIME タイプの値 |

---

## 知っておくと便利なこと

* `.api` は **任意** です — なくてもコンポーネントは動作します。宣言された、オートコンプリートと
  バリデーションの効くインターフェースをコンポーネントに与えるために追加します。他のコンポーネントから
  再利用されるものには付けることをおすすめします。
* **Required** はバインディングの属性ではなく、`<unbound>` エントリを含む独立した `<validation>` ブロック
  です。これが一覧でバインディングを太字にし、指定が漏れているときに `.wod` で警告する仕組みです。
* バインディング名はコンポーネントの Java アクセサメソッドと揃えましょう — `.html`・`.wod`・`.api`・`.java`
  がすべて同じ名前で会話できるようになります。

---

## 関連記事

* **Component Editor** — `.api` が属する `.wo` バンドル *(次の記事)*
* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `.wo` の構造

---
