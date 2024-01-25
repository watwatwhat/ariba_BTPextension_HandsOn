# 2. フロントエンドアプリの構築

## 本マニュアルのステップの全体像
1. ソースコードの入手
2. 環境変数の設定
3. プレビュー実行


### 1. ソースコードの入手
1. 今回は講師の方で用意したソースコードを活用しますので、「Clone from Git」をクリックしてください。

![BAS_top](../../00_Assets/01_setup/03_BAS_top.png)

2. 画面上部に表示される入力欄に、下記リポジトリURLをペーストしてEnterをクリックします。

|   項目   |         値     |
| -------------- |-------------------------- |
| リポジトリURL    | https://example...   |

![repositoryURL](../../00_Assets/02_backend/01_repositoryURL.png)

3. ポップアップが表示されたら「開く」をクリックしてクローンしたフォルダに移動します。

![envVar](../../00_Assets/02_backend/02_openTerminal.png)

4. プロジェクトがクローンされたことが確認できます。

![openFolder](../../00_Assets/02_backend/03_clonedDir.png)

このプロジェクトのディレクトリ構成は以下の通りです。

| ディレクトリ | | 内容 |
| ---------------------- | ---- | ------------------------ |
| `./app`               | | UIフロントエンドのソースコード(フロントエンド本体) |
| `./db`                | | アプリ内で利用されるドメインモデルとデータ |
|  | `/data-model.cds` | ドメインモデル |
| `./srv`               | | アプリ内で利用されるサービスモデルとカスタムハンドラ(バックエンド本体) |
|  | `/server.js` | CORS設定をコントロールするミドルウェアを実装 |
|  | `/reporting-service.cds` | サービスモデル |
|  | `/reporting-service.js` | カスタムハンドラ |
|  | `/external/aribaOpenAPI.js` | AribaのAPIを呼び出すカスタムモジュール |
|  | `/lib/designTimeTools.js` | コーディング時に利用するカスタムモジュール |
|  | `/lib/queryOptionParser.js` | フロントエンドからのODataクエリをパースするカスタムモジュール |
|  | `/lib/responseProcessor.js` | フロントエンドへのODataレスポンスを$selectや$orderbyにより調整するカスタムモジュール |
| `./xs-security.json`       | | アプリを利用するために必要な権限ロールの設定 |
| `./mta.yaml`           | | Multi Target Application (mta) : BTP上のマイクロサービスを横断・統合したデプロイ設定 |
| `./package.json`       | | プロジェクトのメタデータと設定 |
| `./README.md`          | | 本スタートガイド |


## 2. 環境変数の設定
SAP Ariba 環境のAPIキーやレルム等の情報については、セキュリティ上の理由でgitリポジトリの管理から排除しています。
この章では、皆さまそれぞれのAriba環境に接続するため、環境変数を設定していきます。

1. プロジェクトのルートディレクトリに`.cdsrc.json`を追加します。中身には、以下の通りに入力して保存してください。これはAriba環境に関する設定データであり、プロジェクトに環境変数として読み込まれます。

```json
{
    "realm": "<Aribaのレルム名>",
    "OAuth_client_id": "<取得したOauthクライアントID>",
    "OAuth_client_secret": "<取得したOauthクライアントシークレット>",
    "Base64Encoded_OAuth_client_id_and_secret": "<<取得したOauthクライアントID>:<取得したOauthクライアントパスワード>をBase64エンコーディングしたもの>",
    "ApplicationKey": "<取得したAPIキー>"
}
```

![envVar](../../00_Assets/02_backend/04_envVar.png)

## 3. プレビュー実行
それでは早速、アプリケーションを実行してみましょう。

1. 図の通りにターミナルを開きます。

![openTerminal](../../00_Assets/02_backend/05_openTerminal.png)

2. 以下のコマンドを実行して、依存関係のインストールを行った上でプレビューを実行します。すると、このDev Spaceコンテナ内でプレビューアプリケーションが立ち上がります。右下の「Open in a New Tab」からプレビューを開きます。

```bash
npm install
```
```bash
cds watch
```

![command](../../00_Assets/02_backend/06_command.png)

3. CAPサーバーの画面が立ち上がります。この画面では、ソースコード内でODataエンティティとして定義されているデータの一覧が表示されています。メインのエンティティである「C_Requisitions」をクリックしてください。

![serverStart](../../00_Assets/02_backend/07_serverStart.png)

4. SAP Ariba の Operational Regional API から取得したデータを OData (もどき) に変換してクライアントに返却しています。SAP Ariba の Operational Regional API 側の制約により、最大表示件数は40件です。

![dataReturned](../../00_Assets/02_backend/08_dataReturned.png)

## <span style="color: pink">次のステップ</span>

[3. フロントエンドアプリの構築](../03_フロントエンドアプリの構築/README.md)