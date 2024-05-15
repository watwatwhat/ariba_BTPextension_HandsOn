# 3. フロントエンドアプリの構築

> 本ステップでは、下図赤枠の部分について実装を行います。
> ![frontendArchitecture](../../00_Assets/03_frontend/00_architecture.png)


## 本マニュアルのステップの全体像
1. Application Generator を起動
2. Fiori Elements を利用したフロントエンドアプリ生成


### 1. Application Generator を起動

1. 画面上部の入力欄に「 >application generator 」と入力すると、「 Fiori: Open Application Generator 」が表示されます。これをクリックしてアプリケーションジェネレーターを起動してください。

![openGenerator](../../00_Assets/03_frontend/01_openGenerator.png)

### 2. Fiori Elements を利用したフロントエンドアプリ生成

1. Fiori Elements のテンプレート一覧が表示されます。今回は「List Report Page」を選択し「Next」をクリックしてください。

![ListReport](../../00_Assets/03_frontend/02_ListReport.png)

2. データソースについて入力するページが開きます。表の通りに入力し「Next」をクリックします。

|   項目   |         値                             |
| -------------- |--------------------------       |
| Data source    | Use a Local CAP Project         |
| Choose your CAP project   | ariba_BTPextension_HnadsOn_src   |
| OData service    | Reporting Service (Node.js)   |

![dataSource](../../00_Assets/03_frontend/03_dataSource.png)

3. エンティティを表の通りに入力し「Next」をクリックします。

|   項目   |         値                             |
| -------------- |--------------------------       |
| Main Entity    | C_Requisitions         |
| Navigation Entity   | None   |
| Automatically ...   | Yes   |

![entitySelection](../../00_Assets/03_frontend/04_entitySelection.png)

4. プロジェクトの設定を表の通りに入力し「Next」をクリックします。

|   項目   |         値                             |
| -------------- |--------------------------       |
| Module name    | ariba-report-fe-<ユーザーID>         |
| Application title   | ariba-report-fe-<ユーザーID>   |
| Application namespace   | aribareportfe<ユーザーID>   |
| Description   | AribaレポートFioriアプリ   |
| Minimum SAPUI5 version   | 1.120.4   |
| Add deployment...   | Yes   |
| Add FLP configration   | No   |

![attributes](../../00_Assets/03_frontend/05_attributes.png)

5. デプロイ設定を表の通りに入力し「Finish」をクリックします。

|   項目                       |         値               |
| --------------------------- |-----------------------   |
| Please choose the target    | Cloud Foundry            |
| Destination name            | None                     |

![deployConf](../../00_Assets/03_frontend/06_deployConf_none.png)

6. `./app` 配下にフロントエンドのアプリケーションが生成されます。

![fioriDashboard](../../00_Assets/03_frontend/07_fioriDashboard.png)

7. この状態で先ほどのプレビューを開くと、「Web Applications」の部分に `/ariba-report-fe-<ユーザー名>/webapp/index.html` が表示されているはずです。こちらをクリックしてください。

![webApp](../../00_Assets/03_frontend/08_webApp.png)

8. 半自動で生成された Fiori アプリケーションが確認できます。「開始」ボタンをクリックすると、バックエンドアプリがSAP Aribaからのデータを収集し、Fioriアプリが扱える OData (もどき) の形に整形してフロントエンドに送信します。これにより、Fiori の画面で SAP Ariba のデータを確認することが可能です。

![aribaReport](../../00_Assets/03_frontend/09_aribaReport.png)



## 次のステップ

[4. デプロイと結果の確認](../04_デプロイと結果の確認/README.md)

### 各ステップ リンク一覧
[1. 開発環境のセットアップ](../../01_開発環境のセットアップ/README.md) <br>
[2. バックエンドアプリの構築](../../02_バックエンドアプリの構築/README.md) <br>
[3. フロントエンドアプリの構築](../../03_フロントエンドアプリの構築/README.md) <br>
[4. デプロイと結果の確認](../../04_デプロイと結果の確認/README.md) <br>