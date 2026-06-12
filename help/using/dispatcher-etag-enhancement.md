---
title: CDN再検証のためのDispatcher ETagの機能強化
description: AEM as a Cloud ServiceのINTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENTの可用性、サポートステータス、動作。
exl-id: 4409d0f0-05db-42f3-ace9-1516f1970891
source-git-commit: cddffe2194beea628f71b6631faada5df4555267
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# CDN再検証のためのDispatcher ETagの機能強化

## 概要

`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` フラグを使用すると、Dispatcherはキャッシュヒット時に`If-None-Match` リクエストヘッダーを評価できます。 受信`If-None-Match`の値がキャッシュされた`ETag`と一致すると、Dispatcherは`200 OK`ではなく`304 Not Modified`を返すことができます。

このビヘイビアーは、CDNとDispatcher間の不要なペイロード転送を減らし、コンディショナルキャッシュ効率を高めるように設計されています。

## 入手方法

- Dispatcher バージョン：`2.0.264`
- AEM SDK ビルド：`aem-sdk-2026.2.24464.20260214T050318Z-260100`

## AEM as a Cloud Serviceサポート

AEM as a Cloud Serviceでは、この機能はお客様の使用に対応しています。

お客様は、Cloud Managerで`INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT`環境変数を設定することで、これを有効にできます。 Adobeでは、必要に応じてお客様の代理で有効にすることもできます。

有効な場合、CDNが`If-None-Match`を送信し、関連する`ETag`がDispatcher キャッシュに存在する場合、CDNとDispatcher間の応答率が`304`高くなる可能性があります。 この増加は意図された結果です。

## 設定例（キャッシュ ETag ヘッダー）

この機能強化を効果的に行うには、Dispatcherが`ETag`応答ヘッダーをキャッシュし、web サーバーがファイルシステムベースのETagsの生成を避けるように設定されていることを確認します。

`dispatcher.any` キャッシュセクションの例：

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

Dispatcher vhost コンテキストでのApache ディレクティブの例：

```apache
FileETag none
```

ベースラインヘッダーキャッシングのガイダンスについては、[HTTP応答ヘッダーのキャッシュ &#x200B;](dispatcher-configuration.md#caching-http-response-headers)を参照してください。

## 検証例

環境変数を有効にし、設定の変更をデプロイした後：

1. キャッシュをウォームし、返された`ETag`をキャプチャするようにリクエストします。
1. `If-None-Match: <etag-value>`に再度要求します。
1. Dispatcherがキャッシュヒットの再検証フローに対して`304 Not Modified`を返すことを確認します。

## 参照（関連する動作）

Dispatcherでのヘッダーキャッシュと`ETag`処理に関するお客様向けベースラインガイダンスについては、次を参照してください。

- [Dispatcherの設定 – HTTP レスポンスヘッダーのキャッシュ](https://experienceleague.adobe.com/ja/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

「この機能は、Dispatcher `2.0.264` （AEM SDK `2026.2.24464`）で利用できます。 有効にすると、Dispatcherはキャッシュされた`ETag`値に対して`If-None-Match`を検証し、キャッシュヒット時に`304 Not Modified`を返すことができます。 AEM as a Cloud Serviceでは、これはサポートされており、Cloud Manager環境設定を通じて有効にできます。」
