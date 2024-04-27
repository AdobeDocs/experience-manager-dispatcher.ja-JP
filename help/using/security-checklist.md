---
title: Dispatcher のセキュリティチェックリスト
description: 本番環境に進む前に、セキュリティチェックリストを完了する必要があります。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '591'
ht-degree: 56%

---

# Dispatcher のセキュリティチェックリスト{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobeでは、実稼動環境に移行する前に、次のチェックリストを確認することをお勧めします。

>[!CAUTION]
>
>運用開始前に、お使いのバージョンのAEMのセキュリティチェックリストを確認してください。 対応するを参照してください [Adobe Experience Manager ドキュメント](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist).

## 最新バージョンの Dispatcher の使用 {#use-the-latest-version-of-dispatcher}

お使いのプラットフォームで利用可能な最新バージョンをインストールします。 製品とセキュリティの強化を活用するには、最新バージョンを使用するように Dispatcher インスタンスをアップグレードしてください。 [Dispatcher のインストール](dispatcher-install.md)を参照してください。

>[!NOTE]
>
>Dispatcher ログファイルを確認して、インストールされている Dispatcher の現在のバージョンを確認します。
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>ログファイルを探すには、で Dispatcher 設定を調べます `httpd.conf`.

## キャッシュをフラッシュできるクライアントの制限 {#restrict-clients-that-can-flush-your-cache}

[キャッシュをフラッシュできるクライアントを制限する](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)ことをお勧めします。

## トランスポート層のセキュリティ用に HTTPS を有効にする {#enable-https-for-transport-layer-security}

Adobeでは、オーサーインスタンスとパブリッシュインスタンスの両方で HTTPS トランスポートレイヤーを有効にすることをお勧めします。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## アクセスの制限 {#restrict-access}

Dispatcher を設定する際は、外部アクセスをできるだけ制限します。 Dispatcher に関するドキュメントの[サンプルの /filter セクション](dispatcher-configuration.md#main-pars_184_1_title)を参照してください。

## 管理 URL へのアクセスの拒否 {#make-sure-access-to-administrative-urls-is-denied}

フィルターを使用して、Web コンソールなどすべての管理 URL への外部アクセスを確実にブロックします。

参照： [Dispatcher セキュリティのテスト](dispatcher-configuration.md#testing-dispatcher-security) （ブロックする必要がある URL のリスト）。

## ブロックリストの代わりに許可リストを使用 {#use-allowlists-instead-of-blocklists}

アクセスを制御する方法としては、許可リストのほうが優れています。許可リストは、明示的に許可リストに含まれているもの以外はすべてのアクセス要求を拒否するからです。このモデルを使用すると、特定の設定段階でレビューまたは考慮されていない可能性のある新しい要求を、より制限が厳しく制御できます。

## 専用システムユーザーでの Dispatcher の実行 {#run-dispatcher-with-a-dedicated-system-user}

Dispatcher を設定する場合は、権限が最小限の専用ユーザーで web サーバーが実行されていることを確認する必要があります。 Dispatcher キャッシュフォルダーへの書き込みアクセス権のみを付与することをお勧めします。

また、IIS ユーザーは、次のように Web サイトを設定する必要があります。

1. Web サイトの物理パス設定で、「**特定のユーザーとして接続**」を選択します。
1. ユーザーを設定します。

## サービス拒否（DoS）攻撃の防止 {#prevent-denial-of-service-dos-attacks}

サービス拒否（DoS）攻撃は、対象となるユーザーがコンピューターリソースを使用できない状態にするものです。

Dispatcher レベルでは、次のものがあります [dos 攻撃を防ぐための 2 つの設定方法](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/configure-aem-dispatcher-to-prevent-dos-attacks-aem-community/m-p/447780).

* mod_rewrite モジュール（[Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html) など）を使用し、URL 検証を実行します（URL パターン規則がそれほど複雑ではない場合）。

* を使用して、Dispatcher が誤った拡張子を持つ URL をキャッシュしないようにします。 [フィルター](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
  例えば、キャッシング規則を変更して、以下のような mime タイプのみをキャッシングするよう制限します。

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  [外部アクセスを制限する](#restrict-access)ための設定ファイルのサンプルを参照できます。このファイルには、mine タイプの制限も含まれます。

パブリッシュインスタンス上ですべての機能を安全に有効にするには、以下のノードへのアクセスを防ぐようにフィルターを設定します。

* `/etc/`
* `/libs/`

次に、以下のノードパスへのアクセスを許可するようにフィルターを設定します。

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` （JS、CSS、JSON）
* `/libs/cq/security/userinfo.json` （CQ ユーザー情報）
* `/libs/granite/security/currentuser.json`（**データをキャッシュしてはいけません**）

* `/libs/cq/i18n/*` （内部化）

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## CSRF 攻撃を防止するための Dispatcher の設定 {#configure-dispatcher-to-prevent-csrf-attacks}

AEM には、クロスサイトリクエストフォージェリ攻撃を防ぐことを目的とした[フレームワーク](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps)があります。このフレームワークを適切に使用するには、Dispatcher で CSRF トークンのサポートを許可リストに加えるする必要があります。
<!-- OLD URL ABOVE USED TO BE https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps -->
これは、次の手順で実現できます。

1. `/libs/granite/csrf/token.json` パスを許可するフィルターを作成する。
1. Dispatcher 設定の `clientheaders` セクションに `CSRF-Token` ヘッダーを追加する。

## クリックジャッキングの防止 {#prevent-clickjacking}

クリックジャッキングを防ぐには、`X-FRAME-OPTIONS` に設定した HTTP ヘッダー `SAMEORIGIN` を指定するように web サーバーを設定することをお勧めします。

クリックジャッキングについて詳しくは、[OWASP のサイト](https://owasp.org/www-community/attacks/Clickjacking)を参照してください。

## 侵入テストの実施 {#perform-a-penetration-test}

Adobeでは、実稼動環境に移行する前に、AEM インフラストラクチャの侵入テストを実施することをお勧めします。
