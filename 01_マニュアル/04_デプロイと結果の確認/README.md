# 4. デプロイと結果の確認

## 本マニュアルのステップの全体像
1. UI デプロイ設定の追加
2. プロジェクトのビルド
3. プロジェクトのデプロイ
4. 結果の確認

### 1. UI デプロイ設定の追加

1. `mta.yaml` ファイル上で右クリックをしてください。表示されるメニューのうち「 Create MTA Module from Template 」をクリックします。

![mtaModule_fromTemplate](../../00_Assets/04_deploy/01_mtaModule_fromTemplate.png)

2. 「Approuter Configration」をクリックします。

![approuterConfig](../../00_Assets/04_deploy/02_approuterConfig.png)

3. 表の通りに記入し「Next」をクリックします。

|   項目   |         値                             |
| -------------- |--------------------------       |
| Select fyour HTML5 application runtime    | Managed Approuter         |
| Enter a unique business solution ...   | aribaOpenAPI_proxy_<ユーザーID>   |
| Do you plan to add a UI ?    | Yes  |

![approuterConfig](../../00_Assets/04_deploy/03_approuterConfig.png)

4. デプロイに関する設定ファイルである `mta.yaml` に UI デプロイに関わる情報が付加されます。

![mtaUpdated](../../00_Assets/04_deploy/04_mtaUpdated.png)

### 2. プロジェクトのビルド

1. `mta.yaml` ファイル上で右クリックをしてください。表示されるメニューのうち「 Build MTA Project 」をクリックします。

![buildMTA](../../00_Assets/04_deploy/05_buildMTA.png)

2. `mta_archives/aribaOpenAPI_proxy_1.0.0.mtar` が生成されます。

![mtaArchive](../../00_Assets/04_deploy/06_mtaArchive.png)

### 3. プロジェクトのデプロイ

1. `mta_archives/aribaOpenAPI_proxy_1.0.0.mtar` ファイル上で右クリックをしてください。表示されるメニューのうち「 Deploy MTA Archive 」をクリックします。

![deployMTA](../../00_Assets/04_deploy/07_deployMTA.png)

2. 講師の提示した ID/Password を用いて、Cloud Foundry 環境にログインしてください。

![cfLogin](../../00_Assets/04_deploy/08_cfLogin.png)

3. 講師の提示した Cloud Foundry スペース (アプリケーションの実行環境) を選択してください。

![selectSpace](../../00_Assets/04_deploy/09_selectSpace.png)

4. デプロイが無事完了しました。

![deployCompleted](../../00_Assets/04_deploy/10_deployCompleted.png)

### 4. 結果の確認

1. 講師の提示したURLより、HTML5 Application Repository にアクセスし、「aribareportfe<ユーザーID>」にアクセスします。

![HTML5AppRepo](../../00_Assets/04_deploy/11_HTML5AppRepo.png)

2. パブリックにアクセス可能な UIアプリケーション の完成です。

![app](../../00_Assets/04_deploy/12_app.png)

## <span style="color: pink">最初に戻る</span>

[SAP Ariba x SAP BTP による Side-by-Side拡張 ハンズオン](../../README.md)