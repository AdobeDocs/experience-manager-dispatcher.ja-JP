---
title: Web サイトのキャッシュパフォーマンスの最適化
seo-title: Optimizing a Website for Cache Performance
description: キャッシュのメリットを最大化するように Web サイトをデザインする方法について説明します。
seo-description: Dispatcher offers a number of built-in mechanisms that you can use to optimize performance. Learn how to design your web site to maximize the benefits of caching.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 762f575a58f53d25565fb9f67537e372c760674f
workflow-type: tm+mt
source-wordcount: '1134'
ht-degree: 100%

---


# Web サイトのキャッシュパフォーマンスの最適化 {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

Dispatcher は、パフォーマンスの最適化に利用できる多数の組み込みメカニズムを提供します。ここでは、キャッシュのメリットを最大化するように Web サイトをデザインする方法について説明します。

>[!NOTE]
>
>まず覚えておいてほしいのは、Dispatcher は標準の Web サーバーにキャッシュを格納するという点です。これは、次のことを意味します。
>
>* URL を使用してページおよび要求として格納できるデータはすべてキャッシュ可能です。
>* その他のデータ（HTTP ヘッダー、cookie、セッションデータ、フォームデータなど）は格納できません。
>
>通常、多くのキャッシュ戦略は適切な URL の選択を含んでおり、この追加データには依存しません。

## 一貫性のあるページエンコーディングの使用 {#using-consistent-page-encoding}

HTTP 要求ヘッダーはキャッシュされないので、ページエンコーディング情報をヘッダーに格納すると、問題が発生する可能性があります。この場合、Dispatcher がキャッシュからページを提供すると、Web サーバーのデフォルトのエンコーディングがそのページに使用されます。この問題を回避する方法は 2 つあります。

* 使用するエンコーディングが 1 つだけの場合は、Web サーバーで使用するエンコーディングが AEM の Web サイトのデフォルトのエンコーディングと同じであることを確認します。
* `<META>` タグを HTML の `head` セクションで使用して、エンコーディングを設定します。次に例を示します。

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## URL パラメーターの使用回避 {#avoid-url-parameters}

可能な限り、キャッシュするページには URL パラメーターを使用しないでください。例えば、サイトに写真ギャラリーがあるとします。このとき、次の URL はキャッシュされません（Dispatcher が[適切に設定されている](dispatcher-configuration.md#main-pars_title_24)場合を除く）。

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

しかし、次のようにして、これらのパラメーターをページ URL に追加することができます。

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>この URL は、gallery.html と同じページおよび同じテンプレートを呼び出します。テンプレートの定義では、ページをレンダリングするスクリプトを指定できます。または、すべてのページに同じスクリプトを使用できます。

## URL ごとのカスタマイズ {#customize-by-url}

ユーザーによるフォントサイズの変更（またはその他の任意のレイアウトのカスタマイズ）を許可する場合は、それぞれのカスタマイズが URL に反映されるようにする必要があります。

例えば、cookie はキャッシュされないので、フォントサイズを cookie（または同様のメカニズム）に格納した場合、キャッシュされたページではフォントサイズが維持されません。その結果、Dispatcher は任意のフォントサイズのドキュメントをランダムに返します。

フォントサイズを URL にセレクターとして含めれば、この問題を回避できます。

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>ほとんどのレイアウトの側面では、スタイルシートまたはクライアント側スクリプトを使用することもできます。通常、これらはキャッシュと非常にうまく連携します。
>
>これは印刷版でも役立ちます。次のような URL を使用できます。
>
>`www.myCompany.com/news/main.print.html`
>
>テンプレートの定義のスクリプトグロブを使用すると、印刷ページをレンダリングする個別のスクリプトを指定できます。

## タイトルとして使用する画像ファイルの無効化 {#invalidating-image-files-used-as-titles}

ページタイトルまたはその他のテキストを写真としてレンダリングする場合は、そのファイルを、ページ上のコンテンツの更新時に自動的に削除されるような方法で格納することをお勧めします。

1. ページと同じフォルダーに画像ファイルを配置します。
1. 画像ファイルに次の命名形式を使用します。



   `<page file name>.<image file name>`

例えば、ページ myPage.html のタイトルは myPage.title.gif ファイルに格納できます。ページが更新されると、このファイルは自動的に削除されるので、ページタイトルに対する変更はキャッシュに自動的に反映されます。

>[!NOTE]
>
>画像ファイルは必ずしも AEM インスタンスに物理的に存在するわけではありません。画像ファイルを動的に作成するスクリプトを使用できます。そのファイルを Dispatcher が Web サーバーに格納します。

## ナビゲーションに使用する画像ファイルの無効化 {#invalidating-image-files-used-for-navigation}

ナビゲーションエントリ用の写真を使用する場合の方法は、タイトルを使用する場合と基本的に同じですが、若干複雑になります。すべてのナビゲーション画像をターゲットページと共に格納します。通常用とアクティブ用の 2 つの写真を使用する場合は、次のスクリプトを使用できます。

* 通常どおりにページを表示するスクリプト
* 「.normal」要求を処理して、通常の写真を返すスクリプト
* 「.active」要求を処理して、アクティベートされた写真を返すスクリプト

ページと同じ命名ハンドルを使用してこれらの写真を作成し、コンテンツの更新によって写真とページが削除されるようにしてください。

変更されないページの場合、通常、そのページ自体は自動的に無効化されますが、写真はキャッシュに残ります。

## パーソナライズ機能 {#personalization}

Dispatcher はパーソナライズされたデータをキャッシュできないので、パーソナライズは必要な場所にのみ適用することをお勧めします。その理由は次のとおりです。

* 開始ページを自由にカスタマイズ可能にした場合は、ユーザーからそのページを要求されるたびにページを構築する必要があります。
* 一方、10 個の異なる開始ページのなかから選択するようにした場合は、各ページをキャッシュしてパフォーマンスを強化できます。

>[!NOTE]
>
>各ページをパーソナライズする（例えば、ユーザー名をタイトルバーに挿入する）場合は、パフォーマンスに重大な影響を及ぼす可能性があるので、ページをキャッシュできません。
>
>ただし、どうしても必要な場合は、次の方法があります。
>
>* iFrame を使用して、全ユーザーに共通する部分と、特定ユーザーのすべてのページに共通する部分とにページを分割します。こうすれば、それぞれの部分をキャッシュできます。
>* クライアント側 JavaScript を利用して、パーソナライズされた情報を表示します。ただし、ユーザーが JavaScript を無効にした場合でも、ページが正しく表示されるようにする必要があります。
>


## スティッキー接続 {#sticky-connections}

[スティッキー接続](dispatcher.md#TheBenefitsofLoadBalancing)を使用すると、1 人のユーザー用のドキュメントがすべて同じサーバーで作成されるようになります。ユーザーがそのフォルダーを離れて後から戻ってきた場合も、この接続は維持されます。これをおこなうには、Web サイトのスティッキー接続に必要なすべてのドキュメントを保持するためのフォルダーを 1 つ定義します。このフォルダーには他のドキュメントを格納しないようにします。そうすると、パーソナライズされたページとセッションデータを使用する場合に、ロードバランシングに影響が生じます。

## MIME タイプ {#mime-types}

ブラウザーがファイル形式を特定する方法は 2 つあります。

1. 拡張子（.html、.gif、.jpg など）
1. サーバーがファイルと共に送信する MIME タイプ

ほとんどのファイルでは、MIME タイプがファイル拡張子に暗に含まれています。つまり、次の方法でファイル形式を特定できます。

1. 拡張子（.html、.gif、.jpg など）
1. サーバーがファイルと共に送信する MIME タイプ

ファイル名に拡張子がない場合は、プレーンテキストとして表示されます。

MIME タイプは HTTP ヘッダーに含まれており、Dispatcher は HTTP ヘッダーをキャッシュしません。AEM アプリケーションから返されるファイルが、認識されたファイル拡張子を持たず、代わりに MIME タイプに依存する場合は、ファイルが正しく表示されない可能性があります。

ファイルが適切にキャッシュされるようにするには、次のガイドラインに従ってください。

* ファイルに必ず適切な拡張子を含めます。
* 「download.jsp?file=2214」のような URL を含む汎用的なファイル提供スクリプトの使用を避けます。代わりに、ファイル仕様を含む URL を使用するようにスクリプトを書き直します（前述の例ならば「download.2214.pdf」となります）。

