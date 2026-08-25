---
published: true
layout: post
title:  "デプロイ — Deploy Editor とビルドタイプ"
author: ishimoto
date:   2026-08-25
categories: IntelliJ
tags: [IntelliJ, 日本語]
lang: ja
---

# デプロイ

エディタ巡りの最後は、アプリケーションをサーバーに載せる作業です。これには 2 つの
側面があります — **どこへ**デプロイするか（`.treasureboat` ファイルに支えられた
Deploy エディタ）と、**どのように**パッケージングするか（3 つのビルドタイプ）です。

## Deploy エディタと `.treasureboat` ファイル

すべてのプロジェクトには `.treasureboat` ファイルがあります（[プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %})
の投稿で触れたとおり、このファイルはプロジェクトが APPLICATION か FRAMEWORK かも
記録します）。アプリケーションの場合は、これに加えて **デプロイ設定** も保持しており、
Deploy エディタはそれを視覚的に編集します。

```
project.type=APPLICATION
deploy.server.app01.host=159.XXX.XXX.XXX
deploy.server.app01.user=centos
deploy.server.app01.base=/opt/TreasureBoat
deploy.server.app01.build=LEGACY
deploy.server.apache.host=159.XXX.XXX.XXX
deploy.server.apache.build=LEGACY
deploy.target.live.servers=app01\:app, apache\:wsr
```

* **Servers（サーバー）** — 各デプロイ先サーバーの定義です。`host`、SSH の `user`、インストール先の `base`、そしてどの **build**（ビルド）タイプを使うかを指定します。
* **Targets（ターゲット）** — サーバーを **ロール** に対応づける名前付きプロファイルです（ここでは `live`）。`app01:app` はアプリケーションを実行し、`apache:wsr` は静的な Web リソースを配信します。`live` ターゲットをデプロイすると、各サーバーに担当分が SSH 経由で送られます。

![DeployEditor](/assets/Deploy/DeployEditor.png) 

---

## 3 つのビルドタイプ

アプリのパッケージングには 3 通りの方法があり、デプロイ先サーバーに何が入っているか、
そしてバンドルをどれだけ自己完結させたいかで選びます。いずれの方法でも、転送用に
`.tar.gz` も生成されます。

| | **Legacy (NLB)** | **Maven Embedded (MEB)** | **Maven Simple (MSB)** |
|---|---|---|---|
| フォルダ | `App-version-timestamp.woa` | `App_embedded_YYYYMMDD_HHmm` | `App_YYYYMMDD_HHmm` |
| 内容 | `.woa` バンドル：JAR + すべての Resources + 展開済みの WebServerResources + 起動スクリプト | アプリ + **すべての依存 JAR** を `lib/` に、+ 起動スクリプト | アプリの **JAR + `pom.xml`** のみ、+ 起動スクリプト |
| 依存関係の解決 | 同梱 | 同梱 | **サーバー上で Maven が取得** |
| サーバーに必要なもの | Java | Java | Java **+ Maven + ネットワーク** |
| 向いている用途 | 従来型の本番サーバー | モダン・自己完結・エアギャップ環境 | 開発 / ステージング |

* **Legacy (NLB)** — 従来の WebObjects スタイルの `.woa` バンドルです。実行時に読み込まれる完全なリソースを含む JAR、ファイルシステムに展開される WebServerResources、MacOS / UNIX 用の起動スクリプトが含まれます。長期稼働する本番マシンが想定している形式です。
* **Maven Embedded (MEB)** — 自己完結型です。すべての依存 JAR が `lib/` に入るため、サーバーには Java 以外は何も要りません。ロックダウンされた環境や **エアギャップ**（Maven リポジトリへの外向き通信がない）サーバーに最適です。
* **Maven Simple (MSB)** — 軽量です。アプリの JAR + `pom.xml` だけを含みます。初回起動時にサーバー上の Maven が依存関係をダウンロードします。転送は最も小さくて済みますが、サーバーに Maven とネットワークアクセスが必要です。開発 / ステージングに便利です。

> ビルドタイプは `.treasureboat` 内でサーバーごとに指定します
> （`deploy.server.<name>.build`）。そのため、同じプロファイル内でも
> ターゲットごとに異なるパッケージングにできます。

---

## WebServerResources (WSR)

上記 3 つのいずれかと併せて、**WSR ビルド** は **静的リソース**（CSS・JS・画像・
フォント）だけをパッケージングします。これは、アプリケーションを介さずに前段の Web
サーバー — Apache または nginx — が直接配信するためのものです。前述のターゲットにおける
`apache:wsr` ロールがこれにあたります。

---

## Docker について

コンテナ向けには、TreasureBoat は `Dockerfile` と `docker-compose.yml` も生成でき、
ローカルでのビルド / 実行も可能です。これは別のトピックで、IDE 内の Docker ヘルプ
ページで扱っています。

---

## 関連

* [プロジェクト構成]({% post_url 2026-08-15-ij-project-layout-ja %}) — `.treasureboat` ファイルと `target/`
* そこにある **アプリケーション と フレームワーク** のセクション — デプロイ設定を持つのはアプリケーションだけです

---
