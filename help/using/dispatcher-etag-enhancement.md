---
title: CDN 再検証のためのDispatcher ETag の機能強化
description: AEM as a Cloud Serviceの INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT の可用性、サポートステータスおよび動作。
source-git-commit: ac0fafd060643903735ff565072ef2c5bee970be
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# CDN 再検証のためのDispatcher ETag の機能強化

## 概要

`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` フラグを使用すると、Dispatcherはキャッシュヒット時に `If-None-Match` リクエストヘッダーを評価できます。 受信 `If-None-Match` の値がキャッシュされた `ETag` と一致する場合、Dispatcherは `200 OK` ではなく `304 Not Modified` を返すことができます。

この動作は、CDN とDispatcherの間の不要なペイロード転送を減らし、条件付きキャッシュの効率を高めるように設計されています。

## 入手方法

- Dispatcherのバージョン：`2.0.264`
- AEM SDK ビルド：`aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Service サポート

AEM as a Cloud Serviceでは、お客様による使用のために、この機能がサポートされています。

この機能を有効にするには、Cloud Managerで `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` 環境変数を設定します。 Adobeでは、必要に応じて、お客様に代わってテンプレートを有効にすることもできます。

有効にすると、CDN が `If-None-Match` を送信し、関連する `ETag` がDispatcherのキャッシュに存在する場合、CDN とDispatcherの間の `304` い応答率が期待されます。 この増加は、意図された結果です。

## 設定例（キャッシュ ETag ヘッダー）

この機能強化を有効にするには、Dispatcherが `ETag` レスポンスヘッダーをキャッシュし、web サーバーがファイルシステムベースの ETag を生成しないように設定されていることを確認します。

キャッシュセクショ `dispatcher.any` の例：

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Dispatcher vhost コンテキストでの Apache ディレクティブの例：

```apache
FileETag none
```

ベースラインヘッダーキャッシュガイダンスについては、[HTTP 応答ヘッダーのキャッシュ &#x200B;](dispatcher-configuration.md#caching-http-response-headers) を参照してください。

## 検証の例

環境変数を有効にして設定の変更をデプロイした後、次の操作を行います。

1. キャッシュをウォームアップし、返された `ETag` をキャプチャするために 1 回リクエストします。
1. `If-None-Match: <etag-value>` を使用して再度リクエストします。
1. Dispatcherがキャッシュヒットの再検証フローに対して `304 Not Modified` を返すことを確認します。

## 公開リファレンス（関連動作）

Dispatcherでのヘッダーのキャッシュと `ETag` ータ処理に関するお客様へのベースラインガイダンスについては、以下を参照してください。

- [Dispatcherの設定 – HTTP 応答ヘッダーのキャッシュ](https://experienceleague.adobe.com/ja/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

「この機能は、Dispatcher `2.0.264` （AEM SDK `2026.2.24464`）で使用できます。 有効にすると、Dispatcherはキャッシュされた `ETag` 値に対して `If-None-Match` を検証し、キャッシュヒット時に `304 Not Modified` を返すことができます。 AEM as a Cloud Serviceでは、これがサポートされており、Cloud Manager Environment Configuration を通じて有効にすることができます。」
