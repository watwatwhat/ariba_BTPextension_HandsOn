# 本ハンズオンで利用しているアプリケーションコードの実装解説

## 概要

本ハンズオンは、CAP(Cloud Application Programming Model)というSAP独自のWebフレームワークに基づいたアプリケーションを扱います。
処理内容としては、SAP Ariba API により提供される REST API から取得した内容を、ODataもどきに変換し、フロントエンドアプリケーションであるSAPUI5アプリケーションへデータを整形して渡します。

## ディレクトリ構造

このアプリケーションに含まれるファイルと、それぞれの役割は以下のとおりです。

```tree
.
└── ariba_BTPextensionHandsOn_src/          ルートディレクトリ
    ├── app/                                UIフロントエンドのソースコード(フロントエンド本体)
    ├── db/                                 アプリ内で利用されるドメインモデルとデータ
    │   └── data-model.cds                      ドメインモデル
    ├── srv/                                アプリ内で利用されるサービスモデルとカスタムハンドラ(バックエンド本体)
    │   ├── server.js                           CORS設定をコントロールするミドルウェアを実装
    │   ├── reporting-service.cds               サービスモデル
    │   ├── reporting-service.js                カスタムハンドラ
    │   ├── external/
    │   │   └── aribaOpenAPI.js                     AribaのAPIを呼び出すカスタムモジュール
    │   └── lib/
    │       ├── designTimeTools.js                  コーディング時に利用するカスタムモジュール
    │       ├── ODataRequestHandler.js              フロントエンドからのODataクエリをパースするカスタムモジュール
    │       └── ODataResponseHandler.js             フロントエンドへのODataレスポンスを$selectや$orderbyにより調整するカスタムモジュール
    ├── xs-security.json                    アプリを利用するために必要な権限ロールの設定
    ├── mta.yaml                            Multi Target Application (mta) : BTP上のマイクロサービスを横断・統合したデプロイ設定
    ├── package.json                        プロジェクトのメタデータと設定
    └── README.md                           本スタートガイド

```

## 個別ファイルの役割の解説

ランタイム時の全体の挙動としては、下記のツリーの通りとなります。<br>
基本的にメインのロジックを担うのは `/srv/reporting-service.js` です。このメインカスタムロジックモジュールから、その他の便利ツールモジュールが適宜呼び出されています。

```tree

Fiori UI -> SAP Ariba -> Fiori UI

└── /srv/server.js                               エントリーポイント: CORS設定周りなどのミドルウェアはここで適用 / CAPサーバーインスタンスを作成 
    ├── /srv/reporting-service.cds               サービス定義: /db/data-model.cds にて定義されているデータモデルを元にODataサービスを定義
      ├── /srv/reporting-service.js              カスタムハンドラ定義 (Implementation) : CRUD以外のカスタムロジックを記述する
        ├── /srv/lib/ODataRequestHandler.js      適宜利用: Fiori UIから受信したODataリクエストをparseし、SAP Aribaへのリクエストを成形する
        ├── /srv/external/aribaOpenAPI.js        適宜利用: SAP Ariba Operational Reporting API へのリクエストを送信する トークンの取得等もここで行う
        ├── /srv/lib/ODataResponseHandler.js     適宜利用: Fiori UI へのレスポンスを$selectや$orderbyにより調整

```

<details>

<summary>./mta.yaml</summary>

MTA（Multi Target Application）の設定ファイルです。一部抜粋を記載しており、大枠は以下の通りになっています。
例えば `module -> aribaOpenAPI_proxy-srv` はNode.jsアプリケーションであり、`aribaOpenAPI_proxy-destination`や`aribaOpenAPI_proxy-auth`というマイクロサービスに依存しています。

```yaml
_schema-version: "3.1"                        # Schemaのバージョンを明記することで、mta.yamlのプロセッサとのバージョンを合わせる
ID: aribaOpenAPI_proxy                        # SAP BTP, Cloud Foundry Runtime 上で一意となるIDを付与
description: ariba OpenAPI proxy to OData     # このアプリケーションの説明
version: 1.0.0                                # このアプリケーションのバージョン
modules:                                      # このアプリケーションを構成するモジュール
- name: aribaOpenAPI_proxy-srv                # サービスモジュールの名前
  type: nodejs                                # モジュールのタイプ (Node.js)
  path: gen/srv                               # モジュールのファイルパス
  requires:                                   # このモジュールが依存するリソース
  - name: aribaOpenAPI_proxy-destination
  - name: aribaOpenAPI_proxy-auth
  provides:                                   # このモジュールが提供するリソース
  - name: srv-api
    properties:
      srv-url: ${default-url}                 # プロパティの定義
  parameters:                                 # モジュールに関するパラメータ
    buildpack: nodejs_buildpack               # 使用するビルドパック
  build-parameters:                           # ビルド時のパラメータ
    builder: npm                              # 使用するビルダー (npm)
resources:                                    # アプリケーションが依存するリソース
- name: aribaOpenAPI_proxy-destination        # リソースの名前
  type: org.cloudfoundry.managed-service      # リソースのタイプ (Managed Service)
  parameters:                                 # リソースに関するパラメータ
    config:                                   # リソースの設定
      HTML5Runtime_enabled: true
      init_data:
        instance:
          destinations:
          - Authentication: NoAuthentication
            HTML5.DynamicDestination: true
            HTML5.ForwardAuthToken: true
            Name: aribaOpenAPI_proxy-srv-api
            ProxyType: Internet
            Type: HTTP
            URL: ~{srv-api/srv-url}
          existing_destinations_policy: update
    service: destination                      # 使用するサービス (destination)
    service-plan: lite                        # サービスプラン (lite)
  requires:                                   # このリソースが依存するリソース
  - name: srv-api
- name: aribaOpenAPI_proxy-auth               # リソースの名前
  type: org.cloudfoundry.managed-service      # リソースのタイプ (Managed Service)
  parameters:                                 # リソースに関するパラメータ
    config:                                   # リソースの設定
      tenant-mode: dedicated
      xsappname: aribaOpenAPI_proxy-${org}-${space}
    path: ./xs-security.json
    service: xsuaa                            # 使用するサービス (xsuaa)
    service-plan: application                 # サービスプラン (application)
- name: aribaOpenAPI_proxy-repo-host          # リソースの名前
  type: org.cloudfoundry.managed-service      # リソースのタイプ (Managed Service)
  parameters:                                 # リソースに関するパラメータ
    service: html5-apps-repo                  # 使用するサービス (html5-apps-repo)
    service-name: aribaOpenAPI_proxy-html5-srv
    service-plan: app-host                    # サービスプラン (app-host)
parameters:                                   # アプリケーション全体に関するパラメータ
  deploy_mode: html5-repo                     # デプロイモード (html5-repo)
  enable-parallel-deployments: true           # 並行デプロイを有効にする
build-parameters:                             # ビルド時のパラメータ
  before-all:                                 # 全ビルド前に実行するパラメータ
  - builder: custom                           # 使用するビルダー (カスタム)
    commands:                                 # 実行するコマンド
    - npx cds build --production

```
</details>

<details>
<summary>./srv/server.js</summary>
このファイルでは、ミドルウェアを実装しています。
CORSの制限に引っかかることを簡易的に回避しています。本番環境では適切にCORS許容先のドメイン等を設定する必要があります。

```javascript
"use strict";

const cds = require("@sap/cds"); 
const cors = require("cors");
cds.on("bootstrap", app => app.use(cors()));

module.exports = cds.server;
```

</details>

<details>
<summary>./srv/reporting-service.cds</summary>
SAP Aribaから取得するデータのエンティティとその関連を定義しています。具体的には、購買依頼（C_Requisitions）、購買依頼の承認履歴（C_Requisitions__to_ApprovalRecords）、購買依頼の品目（C_Requisitions__to_LineItems）、請求書（C_Invoices）のエンティティが定義されています。これらのエンティティは読み取り専用であり、それぞれに対してUIアノテーションが付けられています。これにより、各エンティティの表示方法が定義されています。

```cds

// data-modelを他のファイルから読み込み
using my.ariba as ariba from '../db/data-model';

service ReportingService {
  // メインの購買依頼エンティティ
  @readonly
  entity C_Requisitions                     as projection on ariba.C_Requisitions;

  // 購買依頼エンティティに紐づいた、承認レコードエンティティ
  @readonly
  entity C_Requisitions__to_ApprovalRecords as projection on ariba.C_Requisitions__to_ApprovalRecords;

  // 購買依頼エンティティに紐づいた、品目エンティティ
  @readonly
  entity C_Requisitions__to_LineItems       as projection on ariba.C_Requisitions__to_LineItems;

  // 購買依頼エンティティに紐づいた、承認レコードエンティティ に対して、UIアノテーションを追加
  annotate C_Requisitions__to_ApprovalRecords with @(UI: {

    // SAP Fiori のヘッダ部分に表示される内容の設定
    HeaderInfo: {
      $Type         : 'UI.HeaderInfoType',
      TypeName      : '承認履歴',
      TypeNamePlural: '承認履歴'
    },

    // SAP Fiori の明細部分に表示される内容の設定
    LineItem  : [
      {
        $Type: 'UI.DataField',
        Value: RealUser,
        Label: '承認者',
      },
      // ...
      {
        $Type: 'UI.DataField',
        Value: ActivationDate,
        Label: '有効化日時',
      }
    ],

    // SAP Fiori のフィルター部分に表示される内容の設定
    SelectionFields   : [
      createdDateFrom,
      createdDateTo
    ],
  });
}


```


</details>

<details>
<summary>./srv/reporting-service.js</summary>
カスタムハンドラを定義するファイルです。 `reporting-service.cds` で提供されるエンティティに対する操作（特にREAD操作）が定義されています。このファイルでは、SAP Aribaからデータを取得し、一時的にキャッシュする機能が実装されています。また、クライアントからのリクエストに応じて、キャッシュからデータを取得し、必要に応じてフィルタリングやソートを行い、クライアントに返す機能も実装されています。このファイルでは、キャッシュの有効性を確認し、キャッシュが無効な場合や存在しない場合には新たにデータを取得するロジックが含まれています。

```js

// ライブラリやカスタムモジュールの読み込み
const cds = require('@sap/cds');
const ODataRequestHandlerClass = require('./lib/ODataRequestHandler');
const ODataRequestHandler = new ODataRequestHandlerClass();
const ODataResponseHandlerClass = require('./lib/ODataResponseHandler');
const ODataResponseHandler = new ODataResponseHandlerClass();
const { console_log_color, getCorrelationID } = require('./lib/designTimeTools');

module.exports = cds.service.impl(async function (srv) {

  // Aribaからのレスポンスを一時的にcacheとして保存する
  let cacheData = null;

  // DateRangeに関しては一時的に保存しておく
  // (DateRangeが変わるとキャッシュが意味をなさなくなるため、再度データをとってくる必要がある)
  let lastDateRange = null;

  console_log_color(`    [INFO] バックエンドキャッシュの状態を初期化しました`, "red");

  /*** SERVICE HANDLERS ***/
  // 全てのエンティティに対して、READ操作を行う際に実行される
  this.on('READ', '*', async (req) => {

    // エラーハンドリングを容易にするためには、操作とエラーの内容を紐づける必要がある。それを "correlationID" というもので紐づける。
    const corrID = getCorrelationID();
    console_log_color(`    [INFO: ${corrID}] サービス: ${req.target.name} を読み込んでいます`, "red");

    // Objectページの場合は、qにUniqueNameを入れる
    let q = null;
    if (req.query.SELECT.from.ref[0].where) {
      q = req.query.SELECT.from.ref[0].where[2].val;
    }
    console_log_color(`    [INFO: ${corrID}] Query Options: ${JSON.stringify(req._queryOptions)}`, "blue");

    // QueryOptionsを抽出する
    const dateRange = ODataRequestHandler.parseFilter_ariba(req.query.SELECT).filters;
    const qo_custom = ODataRequestHandler.parseFilter_custom(req.query.SELECT);
    let qo_orderby = null
      // req._queryOptionsが存在し、かつ$orderbyプロパティがあるかを確認
    if(req._queryOptions && req._queryOptions.$orderby) {
      console_log_color(`    [INFO: ${corrID}] orderbyクエリが検知されました: ${JSON.stringify(req._queryOptions.$orderby)}`, "blue");
      qo_orderby = ODataRequestHandler.parseOrderby(req._queryOptions.$orderby);
    }
    console_log_color(`    [INFO: ${corrID}] 指定された時間範囲: ${dateRange}`, "blue");
    console_log_color(`    [INFO: ${corrID}] 指定されたカスタムクエリ: ${JSON.stringify(qo_custom)}`, "blue");

    // キャッシュがある AND ( リストページでDateRangeが変わっていない OR オブジェクトページ)
    if (cacheData && ((lastDateRange == dateRange) || q)) {
      console_log_color(`    [INFO: ${corrID}] 有効なキャッシュが検知されました キャッシュからデータを出力します`, "red");
      console_log_color(`    [INFO: ${corrID}] キャッシュの先頭: ${cacheData[0].UniqueName}`, "red");

      let response_main = null;
      let response_to_ApprovalRecords = null;
      let response_to_LineItems = null;

      
      switch (req.target.name) {
        case "ReportingService.C_Requisitions":
          // C_Requisitionsが呼び出された場合
          if (q) {
            // C_Requisitions('q'): qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞ってヘッダデータを返す
            response_main = cacheData.filter(item => {
              return item.UniqueName == q
            })

            if (response_main) {
              response_main.$count = response_main.length;
            }
            return response_main;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // qo_customによるフィルタリングをした上で、ヘッダデータを返す
            // その他のクエリオプションもある場合は処理する
            let response_final = ODataResponseHandler.filterByCustomQuery(cacheData, qo_custom);
            if(qo_orderby) {
              response_final = ODataResponseHandler.orderby(response_final, qo_orderby);
            }
            ODataResponseHandler.set$count(response_final);
            return response_final;
          }
        case "ReportingService.C_Requisitions__to_ApprovalRecords":
          // C_Requisitions__to_ApprovalRecordsが呼び出された場合
          if (q) {
            // qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞って明細データを返す
            cacheData.forEach((el => {
              if (el.UniqueName == q) {
                response_to_ApprovalRecords = el.ApprovalRecords;
              }
            }))
            if (response_to_ApprovalRecords) {
              response_to_ApprovalRecords.$count = response_to_ApprovalRecords.length;
            }
            return response_to_ApprovalRecords;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // 全ての明細データを返す
            response_to_ApprovalRecords = [];
            cacheData.forEach((el) => {
              if (el.ApprovalRecords) {
                console.log(el.ApprovalRecords);
                el.ApprovalRecords.forEach(apRecord => {
                  response_to_ApprovalRecords.push(apRecord);
                })
              }
            })
            console.log(response_to_ApprovalRecords);
            response_to_ApprovalRecords.$count = response_to_ApprovalRecords.length;
            return response_to_ApprovalRecords;
          }
        case "ReportingService.C_Requisitions__to_LineItems":
          // C_Requisitions__to_LineItemsが呼び出された場合
          if (q) {
            // qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞って明細データを返す
            cacheData.forEach((el => {
              if (el.UniqueName == q) {
                response_to_LineItems = el.LineItems;
              }
            }))
            if (response_to_LineItems) {
              response_to_LineItems.$count = response_to_LineItems.length;
            }
            return response_to_LineItems;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // 全ての明細データを返す
            response_to_LineItems = [];
            cacheData.forEach((el) => {
              if (el.LineItems) {
                console.log(el.LineItems);
                el.LineItems.forEach(apRecord => {
                  response_to_LineItems.push(apRecord);
                })
              }
            })
            console.log(response_to_LineItems);
            response_to_LineItems.$count = response_to_LineItems.length;
            return response_to_LineItems;
          }
      }
    } else {
      // キャッシュがない OR (リストページでDateRangeが変わっている)
      !cacheData ? console_log_color(`    [Warning:${corrID}] キャッシュが存在しません 新規データを取得します`, "red") : console_log_color(`    [Warning:${corrID}] 日付のクエリ範囲が変更されました 新規データを取得します`, "red");

      // 今の日付クエリ条件を保存しておく
      const qo_default = ODataRequestHandler.parseFilter_ariba(req.query.SELECT);
      lastDateRange = qo_default.filters;

      let response_main = null;
      let response_to_ApprovalRecords = null;
      let response_to_LineItems = null;

      // 一旦 ヘッダ / 明細データ を取得してcacheDataに格納する
      const aribaOpenAPI = await cds.connect.to("aribaOpenAPI_OperationalReportingForProcurement");
      cacheData = await aribaOpenAPI.tx(req).run(req.query);

      console_log_color(`    [INFO: ${corrID}] キャッシュの先頭: ${cacheData[0].UniqueName}`, "red");

      switch (req.target.name) {
        case "ReportingService.C_Requisitions":
          // C_Requisitionsが呼び出された場合
          if (q) {
            // qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞ってヘッダデータを返す
            cacheData.forEach((el => {
              console.log(el.UniqueName);
              if (el.UniqueName == q) {
                response_main = el;
              }
            }))
            if (response_main) {
              response_main.$count = response_main.length;
            }
            console.log("response_main: ", response_main);
            return response_main;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // 全てのヘッダデータを返す
            cacheData.$count = cacheData.length;
            return cacheData;
          }
        case "ReportingService.C_Requisitions__to_ApprovalRecords":
          // C_Requisitions__to_ApprovalRecordsが呼び出された場合
          if (q) {
            // qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞って明細データを返す
            cacheData.forEach((el => {
              if (el.UniqueName == q) {
                response_to_ApprovalRecords = el.ApprovalRecords;
              }
            }))
            if (response_to_ApprovalRecords) {
              response_to_ApprovalRecords.$count = response_to_ApprovalRecords.length;
            }
            return response_to_ApprovalRecords;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // 全ての明細データを返す
            response_to_ApprovalRecords = [];
            cacheData.forEach((el) => {
              if (el.ApprovalRecords) {
                console.log(el.ApprovalRecords);
                el.ApprovalRecords.forEach(apRecord => {
                  response_to_ApprovalRecords.push(apRecord);
                })
              }
            })
            console.log(response_to_ApprovalRecords);
            response_to_ApprovalRecords.$count = response_to_ApprovalRecords.length;
            return response_to_ApprovalRecords;
          }
        case "ReportingService.C_Requisitions__to_LineItems":
          // C_Requisitions__to_LineItemsが呼び出された場合
          if (q) {
            // qが存在する場合、すなわちObjectページの場合
            // 対象のデータに絞って明細データを返す
            cacheData.forEach((el => {
              if (el.UniqueName == q) {
                response_to_LineItems = el.LineItems;
              }
            }))
            if (response_to_LineItems) {
              response_to_LineItems.$count = response_to_LineItems.length;
            }
            return response_to_LineItems;
          } else {
            // qが存在しない場合、すなわちListページの場合
            // 全ての明細データを返す
            response_to_LineItems = [];
            cacheData.forEach((el) => {
              if (el.LineItems) {
                console.log(el.LineItems);
                el.LineItems.forEach(apRecord => {
                  response_to_LineItems.push(apRecord);
                })
              }
            })
            console.log(response_to_LineItems);
            response_to_LineItems.$count = response_to_LineItems.length;
            return response_to_LineItems;
          }
      }
    }
  });
});
```

</details>
