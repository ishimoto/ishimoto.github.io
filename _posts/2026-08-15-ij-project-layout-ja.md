---
published: false
layout: post
title:  "IntelliJ IDEA のプロジェクト構成"
author: ishimoto
date:   2026-08-15
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# IntelliJ プロジェクト構成

まずはフレームワークプロジェクトの構成を見ていきましょう。  
この例では **tb-pro-rule-engine** を使います。  

## 概要

![TopLayout](/assets/ProjectLayout/TopLayout.png)

* .idea – IntelliJ がセットアップや設定を管理するためのフォルダです
* docs – .sangria ルールのバックアップファイルが保存される、任意のフォルダです
* Documents – こちらもドキュメント用の、任意のフォルダです
* src – 実際のプロジェクトフォルダです（開発・コンパイル）
* target – **src** フォルダをコンパイルした結果が入るフォルダです（デプロイ用）。ビルド時にはバンドルの **Info.plist** もここに生成されます（実行ファイル名・プリンシパルクラス・バージョンといった、バンドルメタデータの唯一の正となる情報源です）。そのため Info.plist は **src** には見当たりません。
* .treasureboat – プロジェクトの情報（アプリケーションかフレームワークか）を持つファイルです。旧来の（変換された）プロジェクトがバンドル識別に使っていた Eclipse の `build.properties` を置き換えるものです。
* pom.xml – ビルドファイル（Maven）です。依存関係、TreasureBoat フレームワークのバージョン、ビルドプロファイルを記述します。
* pom.xml.versionsBackup – `versions:set`（リリースバージョンの更新など）を実行したときに Maven が作成するバックアップです。削除して構いません。
* {プロジェクト名}.iml – IntelliJ がプロジェクト情報を管理するために使うファイルです

---

## プロジェクト構成

**src** フォルダの中には、**test** と **main** の 2 つがあります。

![src](/assets/ProjectLayout/src.png)

---

### test

**test** フォルダにはプロジェクトのユニットテストが入ります。  
TreasureBoat は **JUnit 5** によるユニットテストに対応しています。

* java – ユニットテストの Java ソースコードが入ります
* resources – プロジェクトのリソース（画像や設定ファイルなど）が入ります

![test](/assets/ProjectLayout/test.png)

---

### main

**main** フォルダにはプロジェクトのソースコードが入ります。

* generated – 自動生成されたコード（生成されたクラスなど）が入ります
* java – プロジェクトの Java ソースコードが入ります
* resources – プロジェクトのリソース（画像や設定ファイルなど）が入ります
* webapp – プロジェクトのコンポーネント（HTML / XML / JS など）が入ります
* webserver-resources – Web サーバー用のリソース（静的ファイルなど）が入ります

![main](/assets/ProjectLayout/main.png)

---

### generated

ここには自動生成されたコード（生成されたクラスなど）が入ります。  
TreasureBoat では、生成コードはフレームワークによって自動生成されるものであり、ユーザーが手で編集するものではありません。  
これらのファイルは Entity Editor の EOGenerator によって生成されます。

各エンティティごとに、KVC のアクセサやリレーションシップを持つ **アンダースコア始まりのベースクラス**（例：`_MyEntity.java`）がここに生成されます。これらは絶対に編集しないでください — 再生成のたびに上書きされてしまいます。あなたのビジネスロジックは、**java** フォルダにある、対応する **アンダースコアなしのサブクラス**（`MyEntity.java`）に書きます。このクラスは生成されたベースクラスを継承しています。この分割のおかげで、自分のコードを失うことなくモデルを再生成できます。

![generated](/assets/ProjectLayout/generated.png)

---

### java

ここにはプロジェクトのすべてのソースコードが入ります。

![java](/assets/ProjectLayout/java.png)

---

### resources

アプリケーションが必要とするすべてのリソースを置くフォルダです。

##### ローカライズファイル

* Chinese_Taiwan.lproj – 中国語（台湾）のローカライズファイル
* Dutch.lproj – オランダ語のローカライズファイル
* English.lproj – 英語のローカライズファイル
* French.lproj – フランス語のローカライズファイル
* German.lproj – ドイツ語のローカライズファイル
* Italian.lproj – イタリア語のローカライズファイル
* Japanese.lproj – 日本語のローカライズファイル
* Portuguese_Brazil.lproj – ポルトガル語（ブラジル）のローカライズファイル
* Spanish.lproj – スペイン語のローカライズファイル

##### EO マイグレーションファイル

* EOMigration – 起動時にデータを読み込むための EO マイグレーションファイル
* EOTestMigration – マイグレーションファイルを開発するための、EO マイグレーションのテスト用ファイル

##### ナビゲーション

* Navigationbar – ナビゲーションバーの設定

##### モデル

* tb_pro_rule_engine_model.eomodeld – EO モデル

##### Sangria

* d2w.sangria – Sangria のルールファイル（NeXTSTEP plist 形式）です。ルールの条件（LHS）`=>` プロパティ値（RHS）を記述し、ルールベースの UI を制御します。概要で触れた **docs** フォルダには、このファイルのタイムスタンプ付きバックアップが保存されます。

##### プロパティ

* Properties.properties – プロパティファイル

![resources](/assets/ProjectLayout/resources.png)

---

### webapp

ここにはプロジェクトのすべてのコンポーネント（HTML / XML / JS など）が入ります。

各コンポーネントは **`.wo` フォルダ** です。これは、1 つのコンポーネントを構成する各ファイルをまとめた小さなバンドルです。

* `.html` – テンプレート。動的なプレースホルダとして `<treasureboat>`（または `<tb:...>`）タグを使います
* `.wod` – *(任意)* それらのタグを KVC キーパス経由で Java コードに結びつけるバインディング
* `.api` – *(任意)* Component Editor 向けに、コンポーネントのバインディングを宣言します
* `.md` – *(任意)* コンポーネントのドキュメント

このフォルダ内の構成は自由です。好きなようにコンポーネントをグループ分けできます。

古い実装の名残で NonLocalized.lproj フォルダが残っている場合がありますが、現在はもう必要ありません。  
TreasureBoat は多言語コンポーネントには対応していません。  
ローカライズには localizer クラスを使ってください。

![webapp](/assets/ProjectLayout/webapp.png)

---

### webserver-resources

ここには Web サーバー用のリソース、つまりアプリケーションを介さずに Web サーバーが直接配信する静的ファイル（CSS・JS・画像・フォントなど）が入ります。

これらを参照するには、所有するフレームワーク名と `webserver-resources` 内のパスを指定した **`static://`** URL を使います。例：

```
static://tb-core-skin-keen:assets/css/style.bundle.css
```

フレームワーク名が URL の一部になっているため、1 つのバンドルが、それに依存するすべてのアプリにアセットを配信できます。これが、スキンが CDN を使わずに CSS / JS をすべてのアプリへ届ける仕組みです。

![webserver-resources](/assets/ProjectLayout/webserver-resources.png)

---

## アプリケーション と フレームワーク

概要に出てきた `.treasureboat` ファイルには、プロジェクトが **アプリケーション** なのか **フレームワーク** なのかが記録されています。両者は、いま見てきた構成 — `generated`・`java`・`resources`・`webapp`・`webserver-resources` を含む `src/main` — を **まったく同じように** 共有しています。違うのは、バンドルが **何であるか**、そしてどう動くかです。

| | フレームワーク | アプリケーション |
|---|---|---|
| 目的 | 他のプロジェクトが依存するライブラリ — モデル・コンポーネント・スキン・機能 | フレームワークを束ねて動作する、実行可能なアプリ |
| バンドル種別 | `FMWK` — `.framework`。Maven の依存関係として取り込まれます | `APPL` — JAR と起動スクリプトを含む、実行可能な `.woa` バンドル |
| プリンシパルクラス | `TBEnterpriseFrameworkPrincipal` を継承 — バンドル読み込み時に実行されるセットアップ用フック | アプリの `Application` クラス（`TBApplication` を継承）— エントリーポイント |
| Info.plist（**target** 内） | `target/classes/Resources/Info.plist` | `target/classes/Info.plist` |
| 典型的な `resources` | 利用側が使う、共有の EO モデル・コンポーネント・マイグレーション | 上記に **加えて**、実際のデプロイ用 Properties（DB 接続）、起動時 EO マイグレーション、アプリ設定 |

この例の **tb-pro-rule-engine** はフレームワークで、アプリケーションが依存するルールエンジンのモデルとコンポーネントを提供します。アプリケーションはこれ（と他の多くのフレームワーク）に依存し、その上に独自の画面・データ・デプロイ設定を追加します。

---
