---
title: AEM からのキャッシュされたページの無効化
description: 効率的なキャッシュ管理を確保するため、Dispatcher と AEM の間のインタラクションを設定する方法について説明します。
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: ht
source-wordcount: '1407'
ht-degree: 100%

---

# AEM からのキャッシュされたページの無効化 {#invalidating-cached-pages-from-aem}

Dispatcher を AEM と共に使用する際は、キャッシュが効果的に管理されるようにインタラクションを設定する必要があります。環境によっては、この設定でパフォーマンスを向上させることができます。

## AEM ユーザーアカウントの設定 {#setting-up-aem-user-accounts}

デフォルトの `admin` ユーザーアカウントを使用して、デフォルトでインストールされているレプリケーションエージェントを認証します。レプリケーションエージェントで使用する専用のユーザーアカウントを作成します。

詳しくは、AEM セキュリティチェックリストの[レプリケーションユーザーとトランスポートユーザーの設定](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#VerificationSteps)の節を参照してください。

<!-- OLD URL from above https://helpx.adobe.com/jp/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps -->

## オーサー環境からの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-the-authoring-environment}

ページが公開されると、AEM オーサーインスタンス上のレプリケーションエージェントがキャッシュの無効化要求を Dispatcher に送信します。Dispatcher では、新しいコンテンツが公開されると、最終的にキャッシュ内のファイルを更新します。

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

次の手順に従って、AEM オーサーインスタンスにレプリケーションエージェントを設定します。この設定により、ページのアクティベーション時に Dispatcher のキャッシュが無効になります。

1. AEM ツールのコンソールを開きます（`https://localhost:4502/miscadmin#/etc`）。
1. オーサーインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。

1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、URI を入力して Dispatcher にアクセスします。

   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新する必要がある可能性があります。例：https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **メモ：** Dispatcher フラッシュエージェントの場合は、パスベースの仮想ホストエントリを使用してファームを区別する場合にのみ URI プロパティが使用されます。このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. 「OK」をクリックしてエージェントをアクティベートします。

または、[AEM Touch UI](https://experienceleague.adobe.com/ja/docs/experience-manager-65/content/implementing/deploying/configuring/replication#configuring-a-dispatcher-flush-agent) から Dispatcher フラッシュエージェントにアクセスし、設定することもできます。

バニティー URL へのアクセスを有効にする方法について詳しくは、[バニティー URL へのアクセスの有効化](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls)を参照してださい。

>[!NOTE]
>
>Dispatcher のキャッシュをフラッシュするエージェントにユーザー名とパスワードは必要ありませんが、設定した場合は基本認証を使用して送信されます。

この方法には、次の 2 つの問題が発生する可能性があります。

* オーサリングインスタンスから Dispatcher に到達できるようにする必要があります。ただし、両者の間のアクセスが制限されるようにネットワーク（ファイアウォールなど）が設定されている場合は、その限りではありません。

* 公開とキャッシュの無効化は同時に行われます。タイミングによっては、キャッシュからページが削除された直後、新しいページが公開される直前に、削除されたページがリクエストされる場合があります。その場合は、AEM から古いページが返され、Dispatcher で再びキャッシュされます。この状況は、大規模なサイトの場合には、より大きな問題になります。

## パブリッシュインスタンスからの Dispatcher キャッシュの無効化 {#invalidating-dispatcher-cache-from-a-publishing-instance}

キャッシュ管理をオーサー環境からパブリッシュインスタンスに移行すると、パフォーマンスが向上する場合があります。この場合、公開されたページを受信したときにキャッシュの無効化リクエストを Dispatcher に送信するのは、AEM オーサー環境でなくパブリッシュ環境となります。

次のような場合が考えられます。

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* AEM Dispatcher とパブリッシュインスタンスの間で発生する可能性のあるタイミングの競合の回避（[オーサリング環境からの Dispatcher キャッシュの無効化](#invalidating-dispatcher-cache-from-the-authoring-environment)を参照）。
* システムには、高パフォーマンスサーバー上に存在する複数のパブリッシュインスタンスと、オーサリングインスタンスが 1 つのみ含まれます。

>[!NOTE]
>
>経験豊富な AEM 管理者が、この手法を使用するかどうかを判断してください。

パブリッシュインスタンス上で動作するレプリケーションエージェントが Dispatcher フラッシュを制御します。ただし、オーサリング環境で設定され、その後、エージェントをアクティベートして、転送されます。

1. AEM ツールのコンソールを開きます。
1. パブリッシュインスタンスの Tools/replication/Agents の下にある、必要なレプリケーションエージェントを開きます。デフォルトでインストールされている Dispatcher フラッシュエージェントを使用できます。
1. 「編集」をクリックし、「設定」タブで「**有効**」が選択されていることを確認します。
1. （オプション）エイリアスまたはバニティーパスの無効化要求を有効にするには、「**エイリアスの更新**」オプションを選択します。
1. 「トランスポート」タブで、必要な URI を入力して Dispatcher にアクセスします。\
   標準の Dispatcher フラッシュエージェントを使用している場合は、ホスト名とポートを更新します。例：`http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **メモ：** Dispatcher フラッシュエージェントの場合は、パスベースの仮想ホストエントリを使用してファームを区別する場合にのみ URI プロパティが使用されます。このフィールドを使用して、無効化するファームをターゲット設定します。例えば、ファーム #1 の仮想ホストは `www.mysite.com/path1/*` で、ファーム #2 の仮想ホストは `www.mysite.com/path2/*` です。この場合、`/path1/invalidate.cache` の URL を使用して最初のファームをターゲット設定し、`/path2/invalidate.cache` を使用して 2 つ目のファームをターゲット設定できます。詳しくは、[複数ドメインでの Dispatcher の使用](dispatcher-domains.md)を参照してください。 

1. 必要に応じて、その他のパラメーターを設定します。
1. パブリッシュインスタンスにログインし、フラッシュエージェントの設定を検証します。また、フラッシュエージェントが有効になっていることを確認します。
1. 適用するパブリッシュインスタンスごとに、手順を繰り返します。

設定後、オーサーからパブリッシュにページをアクティベートすると、このエージェントが標準のレプリケーションを開始します。ログには、次の例のようなパブリッシュサーバーからのリクエストを示すメッセージが含まれます。

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## 手動での Dispatcher キャッシュの無効化 {#manually-invalidating-the-dispatcher-cache}

ページをアクティベートせずに Dispatcher キャッシュを無効化（またはフラッシュ）するには、Dispatcher に HTTP リクエストを発行できます。例えば、管理者や他のアプリケーションによるキャッシュのフラッシュを可能にする AEM アプリケーションを作成できます。

HTTP リクエストによって、Dispatcher が特定のファイルをキャッシュから削除します。その後、オプションで、Dispatcher が新しいコピーを使用してキャッシュを更新します。

### キャッシュされたファイルの削除 {#delete-cached-files}

HTTP リクエストを発行します。これにより、Dispatcher がキャッシュからファイルを削除します。Dispatcher は、そのページに対するクライアント要求を受信した場合にのみ、ファイルを再びキャッシュします。このようなやり方でキャッシュファイルを削除する方法は、同じページに対する要求を同時に受信する可能性が低い Web サイトに適しています。

HTTP 要求の形式は以下のとおりです。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Dispatcher は、`CQ-Handler` ヘッダーの値に一致する名前を持つ、キャッシュされたファイルおよびフォルダーをフラッシュ（削除）します。例えば、`/content/geomtrixx-outdoors/en` という `CQ-Handle` は、以下のアイテムに一致します。

* `en` ディレクトリ内の `geometrixx-outdoors` という名前を持つ（あらゆるファイル拡張子の）すべてのファイル

* `en` ディレクトリ（存在する場合、ページのサブノードのキャッシュされたレンダリングを格納）の下にある「`_jcr_content`」という名前のディレクトリ

Dispatcher キャッシュ内のその他すべてのファイル（または、`/statfileslevel` 設定によっては特定のレベルまで）は、`.stat` ファイルにタッチすることによって無効化されます。このファイルの最終変更日が、キャッシュされたドキュメントの最終更新日と比較され、`.stat` ファイルのほうが新しい場合はドキュメントが再取得されます。詳しくは、[フォルダーレベルでのファイルの無効化](dispatcher-configuration.md#main-pars_title_26)を参照してください。

追加の `CQ-Action-Scope: ResourceOnly` ヘッダーを送信することによって、無効化（つまり .stat ファイルへのアクセス）を防ぐことができます。この機能を使用すると、特定のリソースをフラッシュできます。これらはすべて、JSON データなど、キャッシュの他の部分を無効にすることなく実行できます。そのデータは動的に作成され、キャッシュに関係なく、定期的にフラッシュする必要があります。例えば、ニュースや株価ティッカーなどを表示するためにサードパーティシステムから取得したデータを表します。

### ファイルの削除と再キャッシュ {#delete-and-recache-files}

HTTP リクエストを発行します。これにより、Dispatcher がキャッシュされているファイルを削除し、そのファイルを即座に取得して再キャッシュします。Web サイトが同じページに対するクライアント要求を同時に受信する可能性が高い場合は、ファイルを削除して即座に再キャッシュします。即時再キャッシュにより、Dispatcher は同時のクライアントリクエストそれぞれに対して 1 回ずつではなく、1 回だけページを取得してキャッシュします。

**メモ：**&#x200B;ファイルの削除と再キャッシュは、パブリッシュインスタンス上でのみ実行してください。オーサーインスタンスから実行すると、リソースを公開する前に再キャッシュが試みられた場合に、競合状態が発生します。

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

即時再キャッシュするページのパスは、メッセージ本文に別々の行として一覧表示されます。`CQ-Handle` の値は、再キャッシュするページを無効化するページのパスです。（[キャッシュ](dispatcher-configuration.md#main-pars_146_44_0010)設定項目の `/statfileslevel` パラメーターを参照してください）。次の例の HTTP リクエストメッセージでは、`/content/geometrixx-outdoors/en.html page` を削除して再キャッシュします。

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### サンプルのフラッシュサーブレット {#example-flush-servlet}

以下のコードは、無効化リクエストを Dispatcher に送信するサーブレットを実装するものです。このサーブレットは、`handle` パラメーターと `page` パラメーターを含む要求メッセージを受信します。これらのパラメーターはそれぞれ、`CQ-Handle` ヘッダーの値と再キャッシュするページのパスを提供します。サーブレットは、その値を使用して、Dispatcher 向けの HTTP リクエストを組み立てます。

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
