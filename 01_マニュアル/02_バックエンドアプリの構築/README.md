# 1. 開発環境のセットアップ

## 本マニュアルのステップの全体像
1. ソースコードの入手
2. 環境変数の設定


### 1. ソースコードの入手
1. 今回は講師の方で用意したソースコードを活用しますので、「Clone from Git」をクリックしてください。

![BAS_top](../../00_Assets/01_setup/03_BAS_top.png)

**本リポジトリのディレクトリ構成**

ハンズオンで使用する、Cloneされたアプリケーションの大まかなディレクトリの構成は以下の通りです。

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


### 1. 環境設定と確認
プロジェクトのルートディレクトリに`.cdsrc.json`を追加します。
中身には、以下の通りに入力して保存してください。これはAriba環境に関する設定データであり、プロジェクトに環境変数として読み込まれます。

```json
{
    "realm": "<Aribaのレルム名>",
    "OAuth_client_id": "<取得したOauthクライアントID>",
    "OAuth_client_secret": "<取得したOauthクライアントシークレット>",
    "Base64Encoded_OAuth_client_id_and_secret": "<<取得したOauthクライアントID>:<取得したOauthクライアントパスワード>をBase64エンコーディングしたもの>",
    "ApplicationKey": "<取得したAPIキー>"
}
```

### 2. アプリケーションの確認