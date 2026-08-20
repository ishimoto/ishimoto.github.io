---
published: true
layout: post
title:  "コンポーネントエディタ"
author: ishimoto
date:   2026-08-20
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# コンポーネントエディタ

**コンポーネント**（TBComponent）は、TreasureBoat の再利用可能な UI の構成要素です。
[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) の記事で見たとおり、
コンポーネントは小さなファイルをまとめた `.wo` フォルダと、その隣にある Java クラスで構成されます。

```
MyComponent.wo/
    MyComponent.html    (テンプレート)
    MyComponent.wod     (バインディング)      – 任意
    MyComponent.api     (インターフェース)    – 任意
    MyComponent.md      (ドキュメント)        – 任意
src/main/java/…/component/MyComponent.java  (ロジック)
```

これらを Project View にばらばらに散らばった別々のファイルとして編集するのは面倒です。そこで
**Component Editor** は、それらすべてを **1 つのタブ付きエディタ** にまとめます。`.wo` フォルダ内の
どれかのファイルを開くと、ファイルごとにタブが並んだエディタが開きます。

![Overview](/assets/ComponentEditor/Overview.png)

---

## 4 つのファイルはどう連携するのか

ここが理解しておく価値のあるポイントです。ファイルは **名前によって** 結びついています。

1. **HTML** が `<treasureboat name="LoginForm">` で動的な箇所を指定します。
2. **WOD** がその名前を、ダイナミックエレメントとその値に結びつけます：
   `LoginForm : TBForm { … }`。
3. **API** *(任意)* はコンポーネント自身の公開バインディング、つまりインターフェースを宣言します（[API エディタ]({% post_url 2026-08-19-ij-api-editor-ja %}) を参照）。
4. **Java** が、WOD が参照する値やアクションを KVC 経由で提供します。

つまり `<treasureboat name="X">`（HTML）↔ `X : Type { value = something; }`（WOD）↔
`something()`（Java）。同じ名前が 3 つのファイルにまたがっています。

> すべての動的タグに WOD のエントリが必要なわけではありません。インラインの **`<tb:…>`** タグ
> （例：`<tb:TBString value="$title"/>`）はバインディングをインラインで持ちます。WOD のエントリ `X`
> と対になるのは、名前付きの `<treasureboat name="X">` 形式です。

---

## タブ

### HTML

テンプレートです。標準的な HTML に TreasureBoat の動的タグを加えたものです。

```html
<treasureboat name="LoginForm">
    <treasureboat name="UserName"></treasureboat>
    <treasureboat name="SubmitButton" />
</treasureboat>
```

### WOD

名前付きの各要素を、ダイナミックエレメントの型とその値に結びつけます。

```
LoginForm : TBForm {
    name = "LoginForm";
}
UserName : TBTextField {
    value = user.name;
    placeholder = "Enter username";
}
SubmitButton : TBSubmitButton {
    action = loginAction;
    value = "Sign In";
}
```

各エントリは `Name : ElementType { binding = value; … }` の形式です。値には、KVC のキーパス
（`user.name`）、定数（`"Sign In"`、`2`、`true`）、またはアクションメソッド（`loginAction`）を
指定できます。

### API

コンポーネントの公開インターフェースです。[API エディタ]({% post_url 2026-08-19-ij-api-editor-ja %}) で解説しています。

### Java

サーバーサイドのロジックです。`TBComponent` を継承し、WOD が結びつけるメソッドを公開します。

```java
public class MyComponent extends TBComponent {
    public MyComponent(final TBContext context) { super(context); }

    public String userName() { return session().currentUser().name(); }

    public TBComponent loginAction() { /* … */ return pageWithName(HomePage.class); }
}
```

### Help

`.wo` フォルダに `.md` ファイルがあれば、**Help** タブとして表示されます。コンポーネントごとの
ドキュメントです。

---

## KVC — バインディングがどのようにコードへ届くのか

WOD のバインディングは **KVC のキーパス** です。`value = session.currentUser.firstName;` は
`session().currentUser().firstName()` として解決されます。各セグメントがメソッド呼び出しになります。
KVC では次のことができます。

* **単純なキー** — `userName` → `userName()`
* **キーパス** — `user.address.city` は複数の呼び出しを連鎖させます
* **演算子** — コレクションに対する `@count`、`@sum`、`@avg`
* **`$` プレフィックス** — `$item` はコンポーネント *自身* のバインディング（親から渡されたもの）を指します

---

## エディタまわりのツール

* **ツールバー** — *Select in Project View*、*Go to Java Class*、*Help*。
* **キーボードショートカット** — タブ間を移動：`Ctrl+Alt+H`（HTML）、`Ctrl+Alt+W`（WOD）、`Ctrl+Alt+A`（API）、`Ctrl+Alt+J`（Java）。

![Tabs](/assets/ComponentEditor/Tabs.png)

---

## よく使うダイナミックエレメントの一部

| エレメント | 用途 |
|---|---|
| `TBForm` | HTML フォーム |
| `TBTextField` | テキスト入力（`value`、`placeholder`） |
| `TBSubmitButton` | 送信ボタン（`action`、`value`） |
| `TBHyperlink` | リンク（`action`、`string`） |
| `TBConditional` | 表示／非表示（`condition`、`negate`） |
| `TBRepetition` | 繰り返し（`list`、`item`、`index`） |
| `TBString` | テキスト出力（`value`、`escapeHTML`） |
| `TBImage` | 画像（`src` / `filename` + `framework`） |

---

## コンポーネントの作成

パッケージまたはフォルダを右クリック → **TreasureBoat → Create TBComponent** で名前を付け、生成する
ファイル（HTML / WOD / API / Java）を選びます。互いに結びついたスターターテンプレートが生成されます。
後から不足しているファイルを追加したい場合は、**Create WOD / API File** で行えます。

---

## 関連

* [API エディタ]({% post_url 2026-08-19-ij-api-editor-ja %}) — `.api` インターフェースのタブ
* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `.wo` の構造

---
