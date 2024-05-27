# 本ハンズオンで登場する単語集

## SAP BTP 用語

| 用語                                 | 説明                                             | 追加資料 |
|--------------------------------------|--------------------------------------------------|----------|
| SAP Business Technology Platform (BTP) | 統合的なエンタープライズ開発と実行プラットフォーム       | [本編マニュアル](./01_マニュアル/00_環境管理者セットアップ/.md)      |
| SAP BTP Cockpit         | SAP BTPの管理とモニタリングを行うためのWebベースユーザーインターフェース                   | なし   |
| SAP CAP (Cloud Application Programming model)  | フルスタックアプリケーション開発フレームワーク       | [ドキュメント](https://cap.cloud.sap/docs/get-started/hello-world)      |
| Multi Target Application (MTA)       | 複数のマイクロコンポーネントから構成されるアプリを束ねたパッケージの形式 | なし      |
| Managed / Standalone approuter       | アプリケーションルーティングと認証機能を提供するミドルウェア | [コラム](./03_コラム/04_managedApprouter.md)      |
| XSUAA Service                                | セキュリティサービスで、認証と権限付与を管理      | [コラム](./03_コラム/06_XSUAA_Destination.md) <br> [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/authorization-and-trust-management-service?region=all) |
| Destination                    | ネットワーク接続先の設定情報を管理                | [コラム](./03_コラム/06_XSUAA_Destination.md) <br> [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/authorization-and-trust-management-service?region=all)      |
| SAP BTP Cloud Foundry Runtime              | SAP BTP上でCloud Foundryベースのアプリケーションを実行するためのランタイム環境               |  [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/cloud-foundry-runtime?region=all)  |
| SAP Business Application Studio            | Webベースの統合開発環境 <br> 多様な開発ツールを提供する   | [コラム](./03_コラム/01_DevSpace.md) <br> [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/business-application-studio?region=all)    |
| SAP HTML5 Application Repository Service for SAP BTP | SAP BTP上でHTML5アプリケーションを管理、配布するためのサービス  | [コラム](./03_コラム/04_managedApprouter.md) <br> [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/html5-application-repository-service?region=all) |
| SAP Build Work Zone, standard edition             | 企業向けのデジタルワークスペースを提供するサービス | [コラム](./03_コラム/04_managedApprouter.md) <br> [SAP Discovery Center](https://discovery-center.cloud.sap/serviceCatalog/sap-build-work-zone-standard-edition?region=all)    |
| Fiori | SAPのソフトウェアに対するユーザーエクスペリエンス(UX)のアプローチ <br> ロールベース」、「アダプティブ」、「シンプル」、「一貫性があり」、「独立している」という五つの基本原則に基づく | [デザインガイドライン](https://experience.sap.com/fiori-design-web/)|
| SAPUI5 (SAP User Interface for HTML5) | MVCアーキテクチャに基づいたフロントエンド開発フレームワーク <br> HTML5、CSS3、JavaScriptを基に構築 | [リファレンス](https://sapui5.hana.ondemand.com/)|
| Fiori Elements            | Fioriアプリの雛形を用いて開発工数を削減するアプローチ | [デザインガイドライン](https://experience.sap.com/fiori-design-web/smart-templates/)   |


## Web開発一般 用語

| 用語                                 | 説明                                             | 追加資料 |
|--------------------------------------|--------------------------------------------------|----------|
| API  | 外部プログラムがシステムやライブラリの機能を利用するためのインターフェース                   | なし                                               |
| OData API   | Web APIの開発規格で、SAPなどのシステム間でのデータ交換を行う                               | なし |
| バックエンドアプリケーション                   | サーバーサイドで動作するビジネスロジックを処理する部分 <br> 今回はSAP Aribaと直接対話する   | なし      |
| フロントエンドアプリケーション                   | ユーザーと直接対話するインターフェース部分 <br> Fioriなど呼ばれる部分で、UIを担う       | なし      |
| デプロイ                             | アプリケーションを運用環境へ配置すること                  | なし      |
| ビルド                               | ソースコードから直接コンピュータが実行可能なアプリケーションを生成すること  | なし      |
| npmコマンド                          | Node.jsのパッケージ管理と配布を行うツールとその操作を行うコマンド   | なし      |
| cdsコマンド                          | CAPプロジェクトの操作に使用するツールとその操作を行うコマンド       | なし      |
