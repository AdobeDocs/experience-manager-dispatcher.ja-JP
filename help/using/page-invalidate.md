---
title: AEM からのキャッシュされたページの無効化
description: Dispatcher とAdobe Experience Manager間のインタラクションを設定して、効果的なキャッシュ管理を確実に行う方法を説明します。
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '1408'
ht-degree: 65%

---

# AEM からのキャッシュされたページの無効化 {#invalidating-cached-pages-from-aem}

Dispatcher を AEM と共に使用する際は、キャッシュが効果的に管理されるようにインタラクションを設定する必要があります。環境によっては、この設定でパフォーマンスを向上させることができます。

## AEM ユーザーアカウントの設定 {#setting-up-aem-user-accounts}

デフォルトの `admin` ユーザーアカウントを使用して、デフォルトでインストールされているレプリケーションエージェントを認証します。レプリケーションエージェント用の専用ユーザーアカウントを作成します

詳しくは、 [設定のレプリケーションとユーザーのトランスポート](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#VerificationSteps) AEM セキュリティチェックリストのの節。

<!-- OLD URL from above https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps -->

## オーサリング環境からの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-the-authoring-environment}

ページが公開されると、AEM オーサーインスタンス上のレプリケーションエージェントがキャッシュの無効化要求を Dispatcher に送信します。この要求により、新しいコンテンツが公開されたときに Dispatcher が最終的にキャッシュ内のファイルを更新します。

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

以下の手順を実行して、ページのアクティベート時に Dispatcher のキャッシュを無効化するよう、AEM オーサーインスタンス上のレプリケーションエージェントを設定します。

1. AEM ツールのコンソールを開きます（`https://localhost:4502/miscadmin#/etc`）。
1. オーサーインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。

1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新します（例：https://&lt;）*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **注意：** Dispatcher フラッシュエージェントの場合、「URI」プロパティは、パスに基づく仮想ホストエントリを使用してファームを区別する場合にのみ使用されます。 このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. 「OK」をクリックして、エージェントをアクティブにします。

または、[AEM Touch UI](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/deploying/configuring/replication#configuring-a-dispatcher-flush-agent) から Dispatcher フラッシュエージェントにアクセスし、設定することもできます。

バニティ URL へのアクセスを有効にする方法の詳細については、を参照してください。 [バニティー URL へのアクセスの有効化](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>Dispatcher キャッシュをフラッシュするためのエージェントにユーザー名とパスワードは必要ありませんが、設定されている場合は基本認証で送信されます。

この方法には、次の 2 つの問題が発生する可能性があります。

* オーサリングインスタンスから Dispatcher に到達できるようにする必要があります。ネットワーク（ファイアウォールなど）が、2 つのネットワーク間のアクセスが制限されるように設定されている場合は、このようになっていない可能性があります。

* 公開とキャッシュの無効化は同時に行われます。タイミングによっては、キャッシュからページが削除された直後、新しいページが公開される直前に、削除されたページがリクエストされる場合があります。その場合は、AEM から古いページが返され、Dispatcher で再びキャッシュされます。大規模なサイトの場合は、これが大きな問題になります。

## パブリッシュインスタンスからの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-a-publishing-instance}

状況によっては、オーサリング環境からパブリッシュインスタンスにキャッシュ管理を移行することで、パフォーマンスを向上させることができます。 その場合、公開されたページを受け取ると、キャッシュ無効化リクエストを Dispatcher に送信するのは（AEM オーサリング環境ではなく）公開環境になります。

次のような場合が考えられます。

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Dispatcher とパブリッシュインスタンス間で発生する可能性のあるタイミングの競合の回避（[オーサリング環境からの Dispatcher キャッシュの無効化](#invalidating-dispatcher-cache-from-the-authoring-environment)を参照）。
* システムには、高パフォーマンスのサーバー上に存在する複数のパブリッシュインスタンスと、1 つのオーサリングインスタンスのみが含まれています。

>[!NOTE]
>
>この方法を使用するかどうかは、経験豊富な AEM 管理者によって決定される必要があります。

Dispatcher のフラッシュは、パブリッシュインスタンス上で動作するレプリケーションエージェントによって制御されます。 ただし、オーサリング環境で設定され、その後、エージェントをアクティベートすることで転送されます。

1. AEM ツールのコンソールを開きます。
1. パブリッシュインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。
1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、Dispatcher へのアクセスに必要な URI を入力します。\
   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新する必要があります。 例：

   `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **注意：** Dispatcher フラッシュエージェントの場合、「URI」プロパティは、パスに基づく仮想ホストエントリを使用してファームを区別する場合にのみ使用されます。 このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. パブリッシュインスタンスにログインし、フラッシュエージェント設定を確認します。 また、フラッシュエージェントが有効になっていることを確認します。
1. 適用するパブリッシュインスタンスごとに、手順を繰り返します。

設定後、オーサーからパブリッシュにページをアクティベートすると、このエージェントが標準のレプリケーションを開始します。ログには、次の例のようなパブリッシュサーバーからのリクエストを示すメッセージが含まれます。

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## 手動での Dispatcher キャッシュの無効化 {#manually-invalidating-the-dispatcher-cache}

ページをアクティベートせずに Dispatcher のキャッシュを無効（またはフラッシュ）にするには、Dispatcher に HTTP リクエストを発行します。 例えば、管理者や他のアプリケーションによるキャッシュのフラッシュを可能にする AEM アプリケーションを作成できます。

この HTTP リクエストによって、Dispatcher が特定のファイルをキャッシュから削除します。その後、オプションで、Dispatcher が新しいコピーを使用してキャッシュを更新します。

### キャッシュされたファイルの削除 {#delete-cached-files}

HTTP 要求を発行することによって、Dispatcher がキャッシュからファイルを削除します。Dispatcher は、そのページに対するクライアント要求を受信した場合にのみ、ファイルを再びキャッシュします。このようなやり方でキャッシュファイルを削除する方法は、同じページに対する要求を同時に受信する可能性が低い Web サイトに適しています。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher は、`CQ-Handler` ヘッダーの値に一致する名前を持つ、キャッシュされたファイルおよびフォルダーをフラッシュ（削除）します。例えば、`/content/geomtrixx-outdoors/en` という `CQ-Handle` は、以下のアイテムに一致します。

* `en` ディレクトリ内の `geometrixx-outdoors` という名前を持つ（あらゆるファイル拡張子の）すべてのファイル

* 「」という名前のすべてのディレクトリ `_jcr_content`en ディレクトリの下（存在する場合は、ページのサブノードのキャッシュされたレンダリングが含まれます）

Dispatcher キャッシュ内のその他すべてのファイル（または特定のレベルに至るまで） `/statfileslevel` （設定）は、に触れることにより無効化されます。 `.stat` ファイル。 このファイルの最終変更日は、キャッシュされたドキュメントの最終変更日と比較され、 `.stat` ファイルの方が新しい。 詳しくは、[フォルダーレベルでのファイルの無効化](dispatcher-configuration.md#main-pars_title_26)を参照してください。

無効化（.stat ファイルへのタッチ）は、追加のヘッダーを送信することで防ぐことができます `CQ-Action-Scope: ResourceOnly`. これを使用すると、動的に作成され、キャッシュに依存しない定期的なフラッシュを必要とする JSON データなど、キャッシュの他の部分を無効にすることなく、特定のリソースをフラッシュできます。 例えば、サードパーティのシステムから取得したデータを表してニュースやストックティッカーを表示する場合などです。

### ファイルの削除と再キャッシュ {#delete-and-recache-files}

HTTP リクエストを発行することによって、Dispatcher がキャッシュされているファイルを削除し、そのファイルを即座に取得して再キャッシュします。Web サイトが同じページに対するクライアント要求を同時に受け取る可能性が高い場合は、ファイルを削除して直ちに再キャッシュします。 即時再キャッシュにより、Dispatcher は同時のクライアント要求それぞれに対して 1 回ずつではなく、1 回だけページを取得してキャッシュします。

**注意：** ファイルの削除と再キャッシュは、パブリッシュインスタンスでのみ実行する必要があります。 オーサーインスタンスから実行すると、リソースを公開する前に再キャッシュが試みられた場合に、競合状態が発生します。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

即時再キャッシュするページのパスは、メッセージ本文に別々の行として一覧表示されます。`CQ-Handle` の値は、再キャッシュするページを無効化するページのパスです。（[キャッシュ](dispatcher-configuration.md#main-pars_146_44_0010)設定項目の `/statfileslevel` パラメーターを参照してください）。以下の HTTP 要求メッセージの例では、`/content/geometrixx-outdoors/en.html page` ページを削除して再キャッシュします。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### サンプルのフラッシュサーブレット {#example-flush-servlet}

次のコードは、無効化リクエストを Dispatcher に送信するサーブレットを実装します。 このサーブレットは、`handle` パラメーターと `page` パラメーターを含む要求メッセージを受信します。これらのパラメーターはそれぞれ、`CQ-Handle` ヘッダーの値と再キャッシュするページのパスを提供します。サーブレットは、その値を使用して、Dispatcher 向けの HTTP リクエストを組み立てます。

サーブレットをパブリッシュインスタンスにデプロイすると、次の URL によって、Dispatcher が /content/geometrixx-outdoors/en.html ページを削除し、新しいコピーをキャッシュします。

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>このサンプルのサーブレットは安全ではありません。HTTP POST リクエストメッセージの使用法を説明することだけを目的としたものです。ソリューションは、サーブレットへのアクセスをセキュリティ保護する必要があります。
>

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```
