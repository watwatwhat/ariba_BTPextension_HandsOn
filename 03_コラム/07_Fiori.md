# SAP Fiori の概要

## デザイン原則
SAP Fioriは、使いやすさを重視したデザインが特徴であり、以下の原則に基づいています。
- ロールベース
- レスポンシブ
- シンプル
- 一貫性
- コヒーレント

## 技術的枠組み
SAP Fioriは、HTML5、CSS3、JavaScriptに基づくフレームワークである**SAPUI5**（または**OpenUI5**）を使用しています。これにより、洗練されたユーザーインタフェースの構築が可能です。

## アーキテクチャ
SAP Fiori アプリケーションは主に以下の三層で構成されています：
1. **プレゼンテーション層**：HTML5とSAPUI5を使用してUIがブラウザ上でレンダリングされます。
2. **アプリケーション層**：SAP NetWeaver Gatewayを通じてODataプロトコルを用いたデータ交換が行われます。
3. **データベース層**：SAP HANAなどのデータベースにアクセスします。

## フロントエンドとバックエンドの開発
- **フロントエンド**：SAPUI5を使用し、クライアントサイドでHTML、CSS、JavaScriptによるUIが構築されます。
- **バックエンド**：
  - **ABAP**: SAPのサーバー上で実行されるアプリケーションロジックとデータベースとのやり取りが行われます。SAP NetWeaver Gatewayを通じてABAPでODataサービスが開発されます。
  - **SAP BTP**: SAP Business Technology Platform 上でSAP Cloud Application Programming Model (CAP) を使用した開発が行われます。CAPは、Node.jsやJavaでのアプリケーションをサポートし、SAP Fioriと組み合わせてエンドツーエンドのソリューションを提供します。

## 進化
SAP Fioriは、初期のバージョンから進化を遂げ、現在ではSAP S/4HANAの主要なユーザーインターフェースとして機能し、クラウドサービスとしても提供されています。
