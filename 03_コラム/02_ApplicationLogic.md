# 本ハンズオンで利用しているアプリケーションコードの実装解説

## 概要

本ハンズオンは、CAP(Cloud Application Programming Model)というSAP独自のWebフレームワークに基づいたアプリケーションを扱います。
処理内容としては、SAP Ariba API により提供される REST API から取得した内容を、ODataもどきに変換し、フロントエンドアプリケーションであるSAPUI5アプリケーションへデータを整形して渡します。

## ディレクトリ構造

このアプリケーションに含まれるファイルと、それぞれの役割は以下のとおりです。

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
|  | `/lib/ODataRequestHandler.js` | フロントエンドからのODataクエリをパースするカスタムモジュール |
|  | `/lib/ODataResponseHandler.js` | フロントエンドへのODataレスポンスを$selectや$orderbyにより調整するカスタムモジュール |
| `./xs-security.json`       | | アプリを利用するために必要な権限ロールの設定 |
| `./mta.yaml`           | | Multi Target Application (mta) : BTP上のマイクロサービスを横断・統合したデプロイ設定 |
| `./package.json`       | | プロジェクトのメタデータと設定 |
| `./README.md`          | | 本スタートガイド |

## 個別ファイルの役割の解説

### ./mta.yaml
MTA（Multi Target Application）の設定ファイルです。大枠は以下の通りになっています。

```yaml
_schema-version: "3.1"                        # Schemaのバージョンを明記することで、mta.yamlのプロセッサとのバージョンを合わせる
ID: aribaOpenAPI_proxy                        # SAP BTP, Cloud Foundry Runtime 上で一意となるIDを付与
description: ariba OpenAPI proxy to OData     # このアプリケーションの説明
version: 1.0.0                                # このアプリケーションのバージョン
modules:                                      # このアプリケーションのバージョン
- name: aribaOpenAPI_proxy-srv
  type: nodejs
  path: gen/srv
  requires:
  - name: aribaOpenAPI_proxy-destination
  - name: aribaOpenAPI_proxy-auth
  provides:
  - name: srv-api
    properties:
      srv-url: ${default-url}
  parameters:
    buildpack: nodejs_buildpack
  build-parameters:
    builder: npm
- name: aribaOpenAPI_proxy-app-content
  type: com.sap.application.content
  path: .
  requires:
  - name: aribaOpenAPI_proxy-repo-host
    parameters:
      content-target: true
  build-parameters:
    build-result: resources
    requires:
    - artifacts:
      - aribaopenapiuiaribaopenapiuifs.zip
      name: aribaopenapiuiaribaopenapiuifs
      target-path: resources/
    - artifacts:
      - aribaopenapiuifearibaopenapiuife.zip
      name: aribaopenapiuifearibaopenapiuife
      target-path: resources/
    - artifacts:
      - aribaopenapiuifemodaribaopenapiuifemod.zip
      name: aribaopenapiuifemodaribaopenapiuifemod
      target-path: resources/
- name: aribaopenapiuiaribaopenapiuifs
  type: html5
  path: app/ariba-openapi-ui-fs
  build-parameters:
    build-result: dist
    builder: custom
    commands:
    - npm install
    - npm run build:cf
    supported-platforms: []
- name: aribaopenapiuifearibaopenapiuife
  type: html5
  path: app/ariba-openapi-ui-fe
  build-parameters:
    build-result: dist
    builder: custom
    commands:
    - npm install
    - npm run build:cf
    supported-platforms: []
- name: aribaOpenAPI_proxy-destination-content
  type: com.sap.application.content
  requires:
  - name: aribaOpenAPI_proxy-destination
    parameters:
      content-target: true
  - name: aribaOpenAPI_proxy-repo-host
    parameters:
      service-key:
        name: aribaOpenAPI_proxy-repo-host-key
  - name: aribaOpenAPI_proxy-auth
    parameters:
      service-key:
        name: aribaOpenAPI_proxy-auth-key
  parameters:
    content:
      instance:
        destinations:
        - Name: aribaopenapi_aribaOpenAPI_proxy_repo_host
          ServiceInstanceName: aribaOpenAPI_proxy-html5-srv
          ServiceKeyName: aribaOpenAPI_proxy-repo-host-key
          sap.cloud.service: aribaopenapi
        - Authentication: OAuth2UserTokenExchange
          Name: aribaopenapi_aribaOpenAPI_proxy_auth
          ServiceInstanceName: aribaOpenAPI_proxy-auth
          ServiceKeyName: aribaOpenAPI_proxy-auth-key
          sap.cloud.service: aribaopenapi
        existing_destinations_policy: ignore
  build-parameters:
    no-source: true
- name: aribaopenapiuifemodaribaopenapiuifemod
  type: html5
  path: app/ariba-openapi-ui-fe-mod
  build-parameters:
    build-result: dist
    builder: custom
    commands:
    - npm install
    - npm run build:cf
    supported-platforms: []
resources:
- name: aribaOpenAPI_proxy-destination
  type: org.cloudfoundry.managed-service
  parameters:
    config:
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
          - Authentication: NoAuthentication
            Name: ui5
            ProxyType: Internet
            Type: HTTP
            URL: https://ui5.sap.com
          existing_destinations_policy: update
    service: destination
    service-plan: lite
  requires:
  - name: srv-api
- name: aribaOpenAPI_proxy-auth
  type: org.cloudfoundry.managed-service
  parameters:
    config:
      tenant-mode: dedicated
      xsappname: aribaOpenAPI_proxy-${org}-${space}
    path: ./xs-security.json
    service: xsuaa
    service-plan: application
- name: aribaOpenAPI_proxy-repo-host
  type: org.cloudfoundry.managed-service
  parameters:
    service: html5-apps-repo
    service-name: aribaOpenAPI_proxy-html5-srv
    service-plan: app-host
parameters:
  deploy_mode: html5-repo
  enable-parallel-deployments: true
build-parameters:
  before-all:
  - builder: custom
    commands:
    - npx cds build --production

```

### ./srv/server.js

このファイルでは、ミドルウェアを実装しています。

### ./srv/reporting-service.cds

あああ

### ./srv/reporting-service.js

あああ

このタスクはコード生成ではなく、既存のコードの解説を求めています。したがって、コード生成のフォーマットは適用されません。

``と`reporting-service.js`のファイルは、SAP Aribaのデータを取得し、キャッシュし、クライアントに提供するためのサービスを定義しています。

`reporting-service.cds`では、SAP Aribaから取得するデータのエンティティとその関連を定義しています。具体的には、購買依頼（C_Requisitions）、購買依頼の承認履歴（C_Requisitions__to_ApprovalRecords）、購買依頼の品目（C_Requisitions__to_LineItems）、請求書（C_Invoices）のエンティティが定義されています。これらのエンティティは読み取り専用であり、それぞれに対してUIアノテーションが付けられています。これにより、各エンティティの表示方法が定義されています。

一方、`reporting-service.js`では、これらのエンティティに対する操作（特にREAD操作）が定義されています。このファイルでは、SAP Aribaからデータを取得し、一時的にキャッシュする機能が実装されています。また、クライアントからのリクエストに応じて、キャッシュからデータを取得し、必要に応じてフィルタリングやソートを行い、クライアントに返す機能も実装されています。このファイルでは、キャッシュの有効性を確認し、キャッシュが無効な場合や存在しない場合には新たにデータを取得するロジックが含まれています。
