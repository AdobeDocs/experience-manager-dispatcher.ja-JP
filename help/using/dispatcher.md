---
title: Dispatcher の概要
seo-title: Adobe AEM Dispatcher Overview
description: AEM Cloud Services のセキュリティやキャッシュなどを強化するために Dispatcher を使用する方法を説明します。
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 85a7f38ecb5f5fe620ff51a7cdbe29f546559288
workflow-type: tm+mt
source-wordcount: '3190'
ht-degree: 94%

---

# Dispatcher の概要 {#dispatcher-overview}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。以前のバージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。

Dispatcher は、Adobe Experience Manager のキャッシュや負荷分散を利用するツールで、エンタープライズクラスの web サーバーと組み合わせて使用できます。

Dispatcher をデプロイするプロセスは、選択した Web サーバーや OS プラットフォームとは独立しています。

1. Dispatcher について学習します（このページ）。[Dispatcher に関するよくある質問](https://helpx.adobe.com/experience-manager/using/dispatcher-faq.html)も参照してください。
1. のインストール [サポート対象の web サーバー](https://helpx.adobe.com/jp/experience-manager/6-5/sites/deploying/using/technical-requirements.html) web サーバーのドキュメントに従って。
1. Web サーバーに [Dispatcher モジュールをインストール](dispatcher-install.md)し、このモジュールに合わせて Web サーバーを設定します。
1. [Dispatcher を設定](dispatcher-configuration.md)します（dispatcher.any ファイル）。
1. コンテンツの更新によってキャッシュが無効化されるように [AEM を設定](page-invalidate.md)します。

>[!NOTE]
>
>Dispatcher とAEMの連携の仕組みをより深く理解するには：
>
>* 詳しくは、 [2017 年 7 月のAEM Community Experts への質問](https://bit.ly/ATACE0717).
>* アクセス [このリポジトリ](https://github.com/adobe/aem-dispatcher-experiments). 実験の集まりを「テイクホーム」ラボ形式で収蔵しています。



必要に応じて、次の情報を使用します。

* [Dispatcher のセキュリティチェックリスト](security-checklist.md)
* [Dispatcher のナレッジベース](https://helpx.adobe.com/cq/kb/index/dispatcher.html)
* [Web サイトのキャッシュパフォーマンスの最適化](https://helpx.adobe.com/jp/experience-manager/6-4/sites/deploying/using/configuring-performance.html)
* [複数ドメインでの Dispatcher の使用](dispatcher-domains.md)
* [Dispatcher での SSL の使用](dispatcher-ssl.md)
* [権限に影響を受けるキャッシュの実装](permissions-cache.md)
* [Dispatcher に関する問題のトラブルシューティング](dispatcher-troubleshooting.md)
* [Dispatcher に関する主な問題とよくある質問](dispatcher-faq.md)

>[!NOTE]
>
>******Dispatcher の最も一般的な使用法は、AEM パブリッシュインスタンス**&#x200B;からの応答をキャッシュして、外部に公開されている Web サイトの応答性とセキュリティを高めることです。したがって、大部分の説明ではこのケースを想定しています。
>
>しかし、Dispatcher は&#x200B;**オーサーインスタンス**&#x200B;の応答性を高めるために使用することもできます。特に、多数のユーザーが Web サイトを編集および更新する場合には効果的です。このケースについて詳しくは、以下の[オーサリングサーバーでの Dispatcher の使用](#using-a-dispatcher-with-an-author-server)を参照してください。

## Dispatcher を使用してキャッシュを実装する理由 {#why-use-dispatcher-to-implement-caching}

Web パブリッシングには、次の 2 つの基本的な手段があります。

* **静的 Web サーバー**：Apache や IIS など、非常にシンプルですが処理の速い Web サーバーです。
* **コンテンツ管理サーバー**：動的で、リアルタイムの、インテリジェントなコンテンツですが、必要とする計算時間などのリソースがはるかに多くなります。

Dispatcher によって、高速かつ動的な環境を実現できます。Dispatcher は、Apache のような静的 HTML サーバーの一部として機能し、以下の目的を達成します。

* できるだけ多くのサイトコンテンツを、静的 Web サイトの形式で格納（キャッシュ）します。
* レイアウトエンジンへのアクセスをできるだけ少なくします。

つまり、以下のようになります。

* **静的コンテンツ**&#x200B;を、静的 Web サーバーとまったく同様に、高速で簡単に操作できます。*また、静的 Web サーバーで使用できる管理ツールおよびセキュリティツールを使用できます*。

* 必要に応じて&#x200B;**動的コンテンツ**&#x200B;を生成できます。このとき、システムの動作が必要以上に遅くなることはありません。

Dispatcher には、動的サイトのコンテンツに基づいて静的 HTML を生成および更新するメカニズムが含まれています。静的ファイルとして保存するドキュメントと、常に動的に生成するドキュメントを詳細に指定できます。

この節では、この機能の基本原理について説明します。

### 静的 Web サーバー {#static-web-server}

![](assets/chlimage_1-3.png)

Apache や IIS などの静的 Web サーバーは、Web サイトの訪問者に静的 HTML ファイルを提供します。静的ページの作成は 1 回だけなので、要求ごとに同じコンテンツが配信されます。

このプロセスはごく単純なので、非常に効率的です。訪問者がファイル（HTML ページなど）を要求すると、ファイルは通常メモリから直接取得され、最悪の場合でもローカルドライブから読み取られます。静的 Web サーバーは長い間使用されてきたので、様々な管理およびセキュリティ管理ツールがあり、ネットワークのインフラストラクチャーにも適切に統合できます。

### コンテンツ管理サーバー {#content-management-servers}

![](assets/chlimage_1-4.png)

AEM などのコンテンツ管理サーバーを使用する場合、訪問者からの要求は高度なレイアウトエンジンによって処理されます。このエンジンは、リポジトリからコンテンツを読み取り、スタイル、フォーマットおよびアクセス権を組み合わせて、コンテンツを訪問者のニーズや権限に合わせたドキュメントに変換します。

このエンジンによって、豊富で動的なコンテンツを作成でき、Web サイトの柔軟性と機能性を高めることができます。ただし、レイアウトエンジンは静的サーバーより多くの処理能力が必要なので、レイアウトエンジンを設定すると、多くの訪問者がシステムを使用した場合に動作が遅くなる可能性があります。

## Dispatcher によるキャッシュの実行方法 {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**キャッシュディレクトリ**：キャッシュ時、Dispatcher モジュールでは Web サーバーの静的コンテンツ提供機能を使用します。キャッシュされたドキュメントは、Dispatcher によって Web サーバーのドキュメントルートに配置されます。

>[!NOTE]
>
>HTTP ヘッダーキャッシュの設定がない場合、Dispatcher は、ページの HTML コードのみを保存します。この場合、HTTP ヘッダーは保存されません。Web サイト内で異なるエンコーディングを使用している場合、これらの HTTP ヘッダーが失われる可能性があるので、このことが問題になる可能性があります。HTTP ヘッダーキャッシュを有効にするには、 [Dispatcher キャッシュの設定](https://helpx.adobe.com/jp/experience-manager/dispatcher/using/dispatcher-configuration.html)

>[!NOTE]
>
>Web サーバーのドキュメントルートをネットワーク接続ストレージ（NAS）上に配置すると、パフォーマンスが低下します。また、NAS 上に配置されたドキュメントルートを複数の Web サーバーで共有すると、レプリケーションアクションの実行時に断続的なロックが発生することがあります。

>[!NOTE]
>
>Dispatcher は、キャッシュされたドキュメントを要求された URL と等しい構造に保存します。
>
>URL に多数のセレクターが含まれる場合など、ファイル名の長さには OS レベルでの制限がある場合があります。

### キャッシュの方法

Web サイトに変更があったとき、Dispatcher では主に 2 つの方法でキャッシュコンテンツを更新します。

* **コンテンツの更新**&#x200B;によって、変更されたページ、およびそのページに直接関連付けられたファイルを削除します。
* **自動無効化**&#x200B;によって、キャッシュで変更があった部分を自動的に無効化し、更新後に期限切れとします。つまり、関連するページに期限切れのフラグを付けるだけで、削除はおこないません。

### コンテンツの更新

コンテンツの更新では、1 つまたは複数の AEM ドキュメントが変更されます。AEM から Dispatcher にシンジケーション要求が送信され、この要求に応じて次のようにキャッシュの更新がおこなわれます。

1. 変更されたファイルを、キャッシュから削除します。
1. 同じハンドルで始まるすべてのファイルを、キャッシュから削除します。たとえば、ファイル /jp/index.html が更新された場合、/jp/index で始まるすべてのファイルは削除されます。このメカニズムによって、特に画像ナビゲーションに関して、キャッシュを効率的に使用するサイトをデザインできます。
1. **statfile** と呼ばれるファイルに *touch* します。touchすると、statfile のタイムスタンプが最終変更日に更新されます。

次のポイントに注意する必要があります。

* コンテンツの更新は、通常、置き換える必要のあるコンテンツを「把握している」作成システムと組み合わせて使用します。
* コンテンツの更新が適用されたファイルは削除されますが、すぐに置き換えられるわけではありません。このファイルが次回要求されたとき、Dispatcher は AEM インスタンスから新しいファイルを取得し、キャッシュに配置します。これで、古いコンテンツを上書きしたことになります。
* 通常、ページのテキストを取り込んで自動生成された画像は、同じハンドルで始まる画像ファイルに格納されます。したがって、ページと画像ファイルは関連があり、削除の対象となります。例えば、mypage.html というページのタイトルテキストを、mypage.titlePicture.gif ファイルとして同じフォルダーに格納できます。したがって、ページの更新ごとにキャッシュにある画像が自動的に削除されるので、画像のバージョンを常にページの現在のバージョンと合わせることができます。
* statfile は複数持つことができます。例えば、言語フォルダーごとに 1 つずつ持つことができます。ページが更新されると、AEM は statfile を含む次の親フォルダーを探し、そのファイルに *touch* します。

### 自動無効化

自動無効化は、キャッシュのパーツを自動的に無効化するもので、ファイル自体の削除は行いません。コンテンツの更新ごとに statfile と呼ばれるファイルに touch するので、statfile のタイムスタンプは最終更新日を示します。

Dispatcher は、自動無効化の対象となるファイルのリストを保持しています。このリストにあるドキュメントが要求されると、Dispatcher はキャッシュされたドキュメントの日付と statfile のタイムスタンプを比較します。

* キャッシュされたドキュメントのほうが新しい場合、そのドキュメントを返します。
* キャッシュされたドキュメントのほうが古い場合、Dispatcher は AEM インスタンスから現在のバージョンを取得します。

この場合も、注意すべきポイントがいくつかあります。

* 自動無効化は通常、HTML ページなど内部関係が複雑な場合に使用します。このようなページには、リンクやナビゲーションエントリが含まれるので、通常はコンテンツの更新後にこれらのリンクなどを更新する必要があります。自動生成される PDF や画像ファイルがある場合も、これらのファイルに対して自動無効化を選択できます。
* 自動無効化の機能は、statfile に touch する以外は、更新時の Dispatcher の動作に関与しません。ただし、statfile への touch によって、キャッシュコンテンツは自動的に古いものとされます。キャッシュ自体は削除されません。

## Dispatcher がドキュメントを返す方法 {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### ドキュメントがキャッシュの対象かどうかの判断

以下が可能です。 [設定ファイルで Dispatcher がキャッシュするドキュメントを定義する](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html). Dispatcher は、要求とキャッシュ可能なドキュメントのリストを照合します。ドキュメントがこのリストにない場合は、AEM インスタンスにドキュメントを要求します。

以下の場合、Dispatcher は&#x200B;*常に* AEM インスタンスに直接ドキュメントを要求します。

* 要求 URI に疑問符（「?」）が含まれている場合。疑問符は通常、キャッシュの必要がない、検索結果などの動的ページを指します。
* ファイル拡張子が不明の場合。Web サーバーでドキュメントのタイプ（MIME タイプ）を判別するために、拡張子が必要です。
* 認証ヘッダー（設定可）が設定されている場合。

>[!NOTE]
>
>（HTTP ヘッダー用の）GET または HEAD メソッドは、Dispatcher によってキャッシュ可能です。応答ヘッダーのキャッシュについて詳しくは、[HTTP 応答ヘッダーのキャッシュ](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html)セクションを参照してください。

### ドキュメントがキャッシュされているかどうかの判断

Dispatcher はキャッシュされたファイルを、静的 Web サイトに含まれる場合と同様に、Web サーバー上に格納しています。ユーザーがキャッシュ可能なドキュメントを要求すると、Dispatcher はそのドキュメントが Web サーバーのファイルシステムに存在するかどうかをチェックします。

* ドキュメントがキャッシュされている場合、Dispatcher はキャッシュされているファイルを返します。
* ドキュメントがキャッシュされていない場合、Dispatcher は AEM インスタンスにドキュメントを要求します。

### ドキュメントが最新かどうかの判断

ドキュメントが最新かどうかを判断するために、Dispatcher は次の 2 つの手順を実行します。

1. ドキュメントが自動無効化の対象であるかどうかチェックします。対象でない場合、ドキュメントは最新であると認識されます。
1. ドキュメントが自動無効化の対象として設定されている場合、Dispatcher は最新の変更情報と比べてドキュメントが古いかどうかチェックします。ドキュメントが古い場合、Dispatcher は AEM インスタンスに最新バージョンを要求し、キャッシュ内のバージョンを置き換えます。

>[!NOTE]
>
>**自動無効化**&#x200B;の対象でないドキュメントは、Web サイト上でコンテンツの更新などによって物理的に削除されるまでキャッシュに残ります。

## ロードバランシングのメリット {#the-benefits-of-load-balancing}

ロードバランシングとは、Web サイトの計算負荷を AEM の複数のインスタンスに分散させることです。

![](assets/chlimage_1-7.png)

以下のようなメリットがあります。

* **処理能力の向上**&#x200B;ロードバランシングを実行すると、Dispatcher と AEM の複数のインスタンスでドキュメント要求が共有されます。各インスタンスで処理するドキュメントの数が少なくなるので、応答時間を短縮できます。Dispatcher はドキュメントカテゴリごとに内部統計を保持するので、負荷を予測してクエリを効率的に分散させることができます。

* **フェイルセーフ対象の拡大**
インスタンスからの応答が受信されない場合、Dispatcher は自動的に要求を他のいずれかのインスタンスにリレーします。したがって、1 つのインスタンスが無効になった場合の影響は、計算能力の低下と比例してサイトの処理が遅くなることだけです。サービスはすべて続行します。

* 同じ静的 Web サーバー上で異なる Web サイトを管理することもできます。

>[!NOTE]
>
>ロードバランシングによって負荷を効率的に分散させる一方、キャッシュによって負荷を減らすことができます。したがって、ロードバランシングを設定する前に、キャッシュを最適化して全体の負荷を減らしてみてください。キャッシュを最適化することで、ロードバランサーのパフォーマンスを向上させたり、ロードバランシングを不要にしたりできます。

>[!CAUTION]
>
>通常は、単一の Dispatcher だけで使用可能なパブリッシュインスタンスの容量を満たすことができますが、一部のアプリケーションでは、2 つの Dispatcher インスタンス間でもロードバランシングをおこなうとよい場合が稀にあります。Dispatcher を追加すると、使用可能なパブリッシュインスタンスの負荷が大きくなり、ほとんどのアプリケーションでパフォーマンスが低下しやすくなるので、複数の Dispatcher の設定は慎重に考慮する必要があります。

## Dispatcher によるロードバランシングの実行方法 {#how-the-dispatcher-performs-load-balancing}

### パフォーマンスの統計

Dispatcher は、AEM の各インスタンスのドキュメント処理速度についての内部統計を保持しています。このデータに基づき、要求に対する応答時間が最も速いインスタンスを予測し、そのインスタンスで必要な計算時間をリザーブします。

要求のタイプによって完了までの時間の平均が変わるので、Dispatcher ではドキュメントのカテゴリを指定できます。指定したカテゴリは、予測時間の計算時に考慮されます。例えば、HTML ページと画像とでは標準的な応答時間が異なることが多いので、この両者を区別することができます。

詳細検索機能を使用する場合、検索クエリに新しいカテゴリを作成できます。カテゴリを使用すれば、応答の最も速いインスタンスに検索クエリを送信できます。これによって、複数の「高負荷」の検索クエリを受信した場合に、低速になったインスタンスが停止するのを防ぎつつ、他のインスタンスに「低負荷」の要求を渡します。

### 個人設定されたコンテンツ（スティッキー接続）

スティッキー接続によって、1 人のユーザーに対するドキュメントが、常に AEM の同じインスタンス上で構成されます。これは、パーソナライズされたページとセッションデータを使用する場合に重要です。データはインスタンスに格納されるので、以降の同じユーザーからの要求は、同じインスタンスに返す必要があります。

スティッキー接続をおこなうと、Dispatcher で要求を最適化する機能が制限されるので、スティッキー接続は必要な場合にのみ使用してください。「スティッキー」ドキュメントを保存するフォルダーは指定できるので、そのフォルダー内のすべてのドキュメントをユーザーごとに同じインスタンス上で構成できます。

>[!NOTE]
>
>スティッキー接続を使用するページでは、ほとんどの場合、キャッシュをオフにする必要があります。オフにしないと、セッション内容に関わらず、ページがすべてのユーザーに同じように表示されます。
>
>*一部*&#x200B;のアプリケーションで、スティッキー接続とキャッシュの併用が可能な場合があります。例えば、セッションにデータを書き込むためのフォームを表示する場合です。

## 複数の Dispatcher の使用 {#using-multiple-dispatchers}

複雑な設定をおこなう場合は、複数の Dispatcher を使用できます。例えば、次のように使用できます。

* 1 つ目の Dispatcher を、イントラネット上での Web サイトの公開に使用
* 2 つ目の Dispatcher を、異なるアドレスと異なるセキュリティ設定で、インターネット上での同じコンテンツの公開に使用

この場合、各要求が経由する Dispatcher は 1 つだけにしてください。別の Dispatcher から渡された要求は処理されません。したがって、どちらの Dispatcher も AEM Web サイトに直接アクセスするようにしてください。

## CDN での Dispatcher の使用 {#using-dispatcher-with-a-cdn}

Akamai Edge Delivery または Amazon Cloud Front などのコンテンツ配信ネットワーク（CDN）は、エンドユーザーに近い場所からコンテンツを配信します。そのため、以下のことが可能です。

* エンドユーザーに対する応答時間をスピードアップする
* サーバーから負荷を取り除く

HTTP インフラストラクチャの構成要素として、CDN は Dispatcher とほとんど同じ働きをします。CDN ノードが要求を受信すると、可能であれば（リソースがキャッシュ内にあって有効な場合）、キャッシュから要求に応えます。それ以外の場合は、次に最も近いサーバーに問い合わせてリソースを取得し、適切であれば、今後の要求に備えてキャッシュします。

「次に最も近いサーバー」は、固有の設定によって異なります。例えば、Akamai の設定では、要求は次のパスをたどることができます。

* Akamai Edge Node
* Akamai Midgres Layer
* ファイアウォール
* ロードバランサー
* Dispatcher
* AEM

ほとんどの場合は、Dispatcher が次のサーバーとなり、キャッシュからドキュメントを提供し、CDN サーバーに返される応答ヘッダーに影響を与えます。

## CDN キャッシュの制御 {#controlling-a-cdn-cache}

CDN が Dispatcher からリソースを再取得するまでのキャッシュ期間を制御するには、様々な方法があります。

1. 明確な設定\
   MIME タイプ、拡張子、要求タイプなどに応じて、特定のリソースを CDN のキャッシュに保持する期間を設定します。

1. 有効期限およびキャッシュ制御ヘッダー\
   有効期限およびキャッシュ制御ヘッダー
ほとんどの CDN は、アップストリームサーバーによって送信される場合に、HTTP ヘッダー `Expires:` および `Cache-Control:` を保持します。これらのヘッダーは、Apache モジュール [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) を使用するなどの方法で保持できます。

1. 手動での無効化\
   CDN では、Web インターフェイスを使用してリソースをキャッシュから削除できます。
1. API ベースの無効化\
   ほとんどの CDN には、リソースをキャッシュから削除できる REST または SOAP API も用意されています。

典型的な AEM 設定では、上記の 1 および 2 の方法で実行できる、拡張子やパス別の設定を使用して、デザイン画像やクライアントライブラリなど、頻繁には変更されず、よく使用されるリソースに対して妥当なキャッシュ期間を設定できます。新しいリリースがデプロイされると、一般的には手動での無効化が必要になります。

キャッシュで管理されているコンテンツに対してこの手法を使用した場合は、コンテンツの変更がエンドユーザーに表示されるのは、設定されているキャッシュ期間の有効期限が切れて、ドキュメントを再度 Dispatcher から取得したときだけです。

よりきめ細かな制御をおこなうために、API ベースの無効化を使用して、Dispatcher のキャッシュが無効になったら CDN のキャッシュを無効化することができます。CDN の API に基づいて、独自の [ContentBuilder](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/day/cq/replication/ContentBuilder.html) と [TransportHandler](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/day/cq/replication/TransportHandler.html)（API が REST ベースではない場合）を実装し、これらを使用して CDN のキャッシュを無効化するレプリケーションエージェントを設定できます。

>[!NOTE]
>
>[AEM（CQ）Dispatcher Security and CDN+Browser Caching](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) および [Dispatcher のキャッシュ](https://docs.adobe.com/content/ddc/en/gems/dispatcher-caching---new-features-and-optimizations.html)に関する録画済みのプレゼンテーションも参照してください。

## オーサリングサーバーでの Dispatcher の使用 {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>を使用している場合は、 [タッチ UI を使用したAEM](https://helpx.adobe.com/jp/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html) 以下を実行します。 **not** オーサーインスタンスのコンテンツをキャッシュします。 オーサーインスタンスに対してキャッシュが有効になっている場合、それを無効にしてキャッシュディレクトリの内容を削除する必要があります。キャッシュを無効にするには、`author_dispatcher.any` ファイルを編集し、`/cache` セクションの `/rule` プロパティを次のように変更します。

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Dispatcher をオーサーインスタンスの前方で使用して、オーサリングのパフォーマンスを向上させることができます。オーサリング Dispatcher を設定するには、次の手順を実行します。

1. Web サーバー（Apache または IIS Web サーバー。[Dispatcher のインストール](dispatcher-install.md)を参照）に Dispatcher をインストールします。
1. 新しくインストールした Dispatcher を動作中の AEM パブリッシュインスタンスに対してテストし、基礎となる正しいインストールが実行されたことを確認します。
1. 今度は、Dispatcher が TCP/IP を使用してオーサーインスタンスに接続できることを確認します。
1. Dispatcher のサンプルの dispatcher.any ファイルを、[Dispatcher ダウンロード](release-notes.md#downloads)で示されている author_dispatcher.any ファイルで置き換えます。
1. `author_dispatcher.any` をテキストエディターで開き、以下の変更をおこないます。

   1. `/renders` セクションの `/hostname` と `/port` がオーサーインスタンスを指すように変更します。
   1. `/docroot` セクションの `/cache` がキャッシュディレクトリを指すように変更します。を使用している場合 [タッチ UI を使用したAEM](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html)に設定する場合は、上記の警告を参照してください。
   1. 変更内容を保存します。

1. 上記で設定した `/cache`／`/docroot` ディレクトリ内にあるすべての既存ファイルを削除します。
1. Web サーバーを再起動します。

>[!NOTE]
>
>示されている `author_dispatcher.any` 設定を使用して `/libs` または `/apps` の下のすべてのコンテンツに影響を与える CQ5 機能パック、ホットフィックスまたはアプリケーションコードパッケージをインストールする場合は、Dispatcher のキャッシュ内のこれらのディレクトリの下にキャッシュされているファイルを削除して、次回要求されたときに、キャッシュされている古いファイルではなく、新たにアップグレードされたファイルが取得されるようにする必要があります。

>[!CAUTION]
>
>以前設定したオーサリング Dispatcher を使用し、*Dispatcher のフラッシュエージェント*&#x200B;を有効にしている場合は、以下を実行してください。

1. AEM オーサーインスタンス上の&#x200B;**オーサリング Dispatcher** のフラッシュエージェントを削除または無効にします。
1. 上記の新しい説明に従って、オーサリング Dispatcher の設定をやり直します。

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
