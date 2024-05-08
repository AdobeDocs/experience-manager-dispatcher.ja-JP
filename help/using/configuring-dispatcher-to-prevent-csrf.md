---
title: CSRF 攻撃を防止するための Dispatcher の設定
description: クロスサイトリクエストフォージェリー攻撃を防ぐための AEM Dispatcher の設定方法について説明します。
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '217'
ht-degree: 100%

---

# CSRF 攻撃を防止するための Dispatcher の設定{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM は、クロスサイトリクエストフォージェリー攻撃を防ぐことを目的としたフレームワークを。このフレームワークを適切に利用するには、Dispatcher 設定を次のように変更します。

>[!NOTE]
>
>既存の設定に基づいて、以下の例のルール番号を必ず変更します。Dispatcher は、最後に一致したルールを使用して許可または拒否するので、既存リストの下部にルールを配置します。

1. `author-farm.any` と `publish-farm.any` の `/clientheaders` セクションで、リストの下部に次のエントリを追加します。\
   `CSRF-Token`
1. `author-farm.any` と `publish-farm.any` または `publish-filters.any`ファイルの /filters セクションに次の行を追加して、Dispatcher 経由での `/libs/granite/csrf/token.json` に対するリクエストを許可します。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. `publish-farm.any` の `/cache /rules` セクションに、Dispatcher が `token.json` ファイルをキャッシュできないようにするルールを追加します。一般的に、オーサーインスタンスはキャッシュをバイパスするので、`author-farm.any` にルールを追加する必要はありません。\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

設定が機能していることを検証するには、DEBUG モードの dispatcher.log を見て、token.json ファイルがキャッシュされておらず、フィルターによってブロックされていることを検証します。次のようなメッセージが表示されるはずです。\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

また、Apache の `access_log` で、リクエストが引き継がれていることを検証することもできます。/libs/granite/csrf/token.json に対する要求には、HTTP 200 のステータスコードが返されます。
