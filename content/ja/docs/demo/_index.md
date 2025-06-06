---
title: OpenTelemetryデモ ドキュメント
linkTitle: デモ
cascade:
  repo: https://github.com/open-telemetry/opentelemetry-demo
weight: 180
default_lang_commit: 548e5e29f574fddc3ca683989a458e9a6800242f
---

[OpenTelemetryデモ](/ecosystem/demo/)のドキュメンテーションへようこそ。
ここでは、デモのインストール方法や実行方法、そしてOpenTelemetryの動作を確認できるいくつかのシナリオについて説明しています。

## デモの実行 {#running-the-demo}

デモをデプロイして動作を確認したい場合は、ここから始めてください。

- [Docker](docker-deployment/)
- [Kubernetes](kubernetes-deployment/)

## 言語機能リファレンス {#language-feature-reference}

特定の言語の計装がどのように機能するかを理解したい場合は、ここから始めてください。

| 言語       | 自動計装                                               | 計装ライブラリ                                                                        | 手動計装                                                                              |
| ---------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| .NET       | [会計サービス](services/accounting/)                   | [カートサービス](services/cart/)                                                      | [カートサービス](services/cart/)                                                      |
| C++        |                                                        |                                                                                       | [通貨サービス](services/currency/)                                                    |
| Go         |                                                        | [決済サービス](services/checkout/), [商品カタログサービス](services/product-catalog/) | [決済サービス](services/checkout/), [商品カタログサービス](services/product-catalog/) |
| Java       | [広告サービス](services/ad/)                           |                                                                                       | [広告サービス](services/ad/)                                                          |
| JavaScript |                                                        |                                                                                       | [支払いサービス](services/payment/)                                                   |
| Kotlin     |                                                        | [不正検知サービス](services/fraud-detection/)                                         |                                                                                       |
| PHP        |                                                        | [見積サービス](services/quote/)                                                       | [見積サービス](services/quote/)                                                       |
| Python     | [レコメンデーションサービス](services/recommendation/) |                                                                                       | [レコメンデーションサービス](services/recommendation/)                                |
| Ruby       |                                                        | [メールサービス](services/email/)                                                     | [メールサービス](services/email/)                                                     |
| Rust       |                                                        | [配送サービス](services/shipping/)                                                    | [配送サービス](services/shipping/)                                                    |

## サービスドキュメント {#service-documentation}

各サービスのOpenTelemetryのデプロイの仕方に関する具体的な情報は、こちらで確認できます

- [会計サービス](services/accounting/)
- [広告サービス](services/ad/)
- [カートサービス](services/cart/)
- [決済サービス](services/checkout/)
- [メールサービス](services/email/)
- [フロントエンド](services/frontend/)
- [負荷生成ツール](services/load-generator/)
- [支払いサービス](services/payment/)
- [商品カタログサービス](services/product-catalog/)
- [見積サービス](services/quote/)
- [レコメンデーションサービス](services/recommendation/)
- [配送サービス](services/shipping/)
- [画像プロバイダーサービス](services/image-provider/?i18n-patch)
- [React Native アプリケーション](services/react-native-app/)

## フィーチャーフラグシナリオ {#feature-flag-scenarios}

OpenTelemetryを使用してどのように問題を解決できるでしょうか？
これらの[フィーチャーフラグ有効化シナリオ](feature-flags/)では、事前に設定された問題を紹介し、それらを解決するためにOpenTelemetryのデータをどのように解釈するかを説明します。

## リファレンス {#reference}

要件や機能マトリクスなどのプロジェクトリファレンスドキュメント。

- [アーキテクチャ](architecture/)
- [開発](development/)
- [フィーチャーフラグリファレンス](feature-flags/)
- [メトリック機能マトリクス](telemetry-features/metric-coverage/)
- [要件](./requirements/)
- [スクリーンショット](screenshots/)
- [サービス](services/)
- [スパン属性リファレンス](telemetry-features/manual-span-attributes/)
- [テスト](tests/)
- [トレース機能マトリクス](telemetry-features/trace-coverage/)
