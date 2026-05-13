---
title: CSRF 攻撃を防止するための Adobe Experience Manager Dispatcher の設定
description: クロスサイトリクエストフォージェリー攻撃を防ぐための Adobe Experience Manager Dispatcher の設定方法について説明します。
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
TQID: https://experienceleague.adobe.com/xbW-j06MGU1Ku5MwXscpLdpyw8KRLE18YIz-iUgAStI
product_v2: id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: d095671a-1355-40aa-8b5f-06c33c68080bid: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: b68483fc6956bc0e6c2b1939d2203311da62987e
workflow-type: tm+mt
source-wordcount: 236
ht-degree: 100%

---

# CSRF 攻撃を防止するための Adobe Experience Manager Dispatcher の設定{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM（Adobe Experience Manager）には、クロスサイトリクエストフォージェリー攻撃を防ぐことを目的としたフレームワークが用意されています。 このフレームワークを適切に利用するには、Dispatcher 設定を次のように変更します。

>[!NOTE]
>
>既存の設定に基づいて、以下の例のルール番号を必ず更新します。 Dispatcher は、最後に一致したルールを使用して許可または拒否するので、既存リストの下部にルールを配置します。

1. `author-farm.any` と `publish-farm.any` の `/clientheaders` セクションで、リストの下部に次のエントリを追加します。\
   `CSRF-Token`
1. `author-farm.any` と `publish-farm.any` または `publish-filters.any`ファイルの /filters セクションに次の行を追加して、Dispatcher 経由での `/libs/granite/csrf/token.json` に対するリクエストを許可します。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`

1. `publish-farm.any` の `/cache /rules` セクションに、Dispatcher が `token.json` ファイルをキャッシュできないようにするルールを追加します。 一般的に、オーサーインスタンスはキャッシュをバイパスするので、`author-farm.any` ファイルにルールを追加する必要はありません。

   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

設定が機能していることを検証するには、デバッグモードで dispatcher.log を監視します。 これは、`token.json` ファイルを検証して、キャッシュされたりフィルターによってブロックされたりしていないことを確認するのに役立ちます。 次のようなメッセージが表示されるはずです。\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

また、Apache の `access_log` で、リクエストが引き継がれていることを検証することもできます。 /libs/granite/csrf/token.json に対する要求には、HTTP 200 のステータスコードが返されます。
