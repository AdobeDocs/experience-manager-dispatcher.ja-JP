---
title: CSRF 攻撃を防止するための Dispatcher の設定
description: クロスサイトリクエストフォージェリー攻撃を防ぐための AEM Dispatcher の設定方法について説明します。
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '217'
ht-degree: 46%

---

# CSRF 攻撃を防止するための Dispatcher の設定{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM には、クロスサイトリクエストフォージェリ攻撃を防ぐことを目的としたフレームワークがあります。このフレームワークを適切に使用するには、Dispatcher 設定に次の変更を加えます。

>[!NOTE]
>
>既存の設定に基づいて、以下の例のルール番号を必ず変更してください。Dispatcher は、最後に一致したルールを使用して許可または拒否を許可するので、ルールは既存のリストの下部付近に配置します。

1. が含まれる `/clientheaders` のセクション `author-farm.any` および `publish-farm.any`リストの下部に次のエントリを追加します。\
   `CSRF-Token`
1. の/filters セクション内 `author-farm.any` および `publish-farm.any` または `publish-filters.any` ファイルで、次の行を追加してリクエストを許可します： `/libs/granite/csrf/token.json` Dispatcher を使用する。\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. の下 `/cache /rules` のセクション `publish-farm.any`を追加し、Dispatcher によるキャッシュをブロックします。 `token.json` ファイル。 通常、作成者はキャッシュをバイパスするので、ルールをユーザーに追加する必要はありません `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

設定が機能していることを検証するには、DEBUG モードの dispatcher.log を見て、token.json ファイルがキャッシュされておらず、フィルターによってブロックされていることを検証します。次のようなメッセージが表示されるはずです。\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

また、リクエストが Apache で成功していることを検証することもできます `access_log`. /libs/granite/csrf/token.json に対する要求には、HTTP 200 のステータスコードが返されます。
