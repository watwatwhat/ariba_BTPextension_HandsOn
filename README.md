# SAP Ariba x SAP BTP による Side-by-Side拡張 ハンズオン

## はじめに
このドキュメントでは、SAP AribaとSAP Business Technology Platform (BTP) を用いたSide-by-Side拡張のハンズオンについて解説を行います。


## 拡張開発シナリオ 概要

![BusinessRequirements](./00_Assets/00_BusinessRequirements.png)

**ユーザーの抱える課題**
1. Excel 形式で SAP Ariba のデータを抽出し、他のシステムに連携したい
2. SAP S/4HANA Cloud と親和性の高い Fiori UI を用いて、SAP Ariba に蓄積されたデータを確認したい

**上記課題に対する解決策**
* SAP Ariba に用意された Operational Reporting API を用いてデータを抽出します
* SAP BTP 上で、データを適切に処理するバックエンドアプリケーションを構築します
* 高度に自動化されたフロントエンド開発ツールを用いて、SAP S/4HANA Cloud の標準UIである Firoi でカスタムUIを構築します


## 本ハンズオンの流れ
本ハンズオンは、以下のステップに従って進行します。
1. [開発環境のセットアップ](./01_マニュアル/01_開発環境のセットアップ/README.md)
2. [バックエンドアプリの構築](./01_マニュアル/02_バックエンドアプリの構築/README.md)
3. [フロントエンドアプリの構築](./01_マニュアル/03_フロントエンドアプリの構築/README.md)
4. [デプロイと結果の確認](./01_マニュアル/04_デプロイと結果の確認/README.md)


## 本リポジトリのディレクトリ構成

| ディレクトリ     |  内容                        |
| -------------- |-------------------------- |
| `00_Assets`    | 管理用: README 素材ディレクトリ|
| `01_マニュアル`  |  本ハンズオンで利用するマニュアル |
| `02_ソースコード` | ソースコード                 |
| `./README.md`  |  本スタートガイド             |

## <span style="color: pink">次のステップ</span>

[1. 開発環境のセットアップ](./01_マニュアル/01_開発環境のセットアップ/README.md)


## 終わりに
このハンズオンを通じて、SAP AribaとSAP BTPを活用した拡張開発の基本を理解し、実際のプロジェクトに応用できる知識と経験を得ることができます。


## さらなる深みへ！ 参考文献のご紹介
| サイト名        |   URL   |
| -------------- | ------- |
| CAP 公式ドキュメント: CAPire        | https://cap.cloud.sap/docs/get-started/hello-world |