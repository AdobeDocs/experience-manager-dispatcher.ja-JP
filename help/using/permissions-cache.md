---
title: セキュリティで保護されたコンテンツをキャッシュ
description: Dispatcher における権限を区別するキャッシュのしくみについて説明します。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 3d8d8204-7e0d-44ad-b41b-6fec2689c6a6
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '923'
ht-degree: 97%

---

# セキュリティで保護されたコンテンツをキャッシュ {#caching-secured-content}

権限を区別するキャッシュを使用して、セキュリティ保護されたページをキャッシュできます。Dispatcher は、ページに対するユーザーのアクセス権限を確認してから、キャッシュされたページを配信します。

Dispatcher には、権限を区別するキャッシュを実装する AuthChecker モジュールが含まれます。このモジュールがアクティベートされると、Dispatcher が AEM サーブレットを呼び出して、ユーザー認証とリクエストされたコンテンツに対する承認を実行します。サーブレットの応答によって、キャッシュからコンテンツを web ブラウザーに配信するかどうかが決定されます。

認証方法と承認方法は AEM デプロイメントに固有なので、サーブレットを作成する必要があります。

>[!NOTE]
>
>全体にセキュリティ制約を課すには、`deny` フィルターを使用します。ユーザーまたはグループのサブセットへのアクセスを許可するように設定されているページに対しては、権限を区別するキャッシュを使用します。

次の図は、web ブラウザーが権限を区別するキャッシュを使用するページをリクエストしたときに発生するイベントの順序を示したものです。

## ページがキャッシュされていて、ユーザーが承認されている場合 {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. Dispatcher が、リクエストされたコンテンツがキャッシュされていて有効であると判断します。
1. Dispatcher がレンダーにリクエストメッセージを送信します。HEAD セクションには、ブラウザーリクエストのヘッダー行がすべて含まれます。
1. レンダーが、認証チェッカーサーブレットを呼び出してセキュリティチェックを実行し、Dispatcher に応答します。応答メッセージには、ユーザーが承認されていることを示す HTTP ステータスコード 200 が含まれます。
1. Dispatcher がブラウザーに応答メッセージを送信します。応答メッセージは、レンダーの応答のヘッダー行と、本文としてキャッシュされたコンテンツで構成されます。

## ページがキャッシュされていないが、ユーザーが承認されている場合 {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. Dispatcher が、コンテンツがキャッシュされていない、または更新が必要であると判断します。
1. Dispatcher が、レンダーに元のリクエストを転送します。
1. レンダーが、AEM オーソライザーサーブレット（このサーブレットは Dispatcher 認証チェッカーサーブレットではありません）を呼び出して、セキュリティチェックを実行します。ユーザーが承認されると、レンダーは応答メッセージの本文にレンダリングされるページを含めます。
1. Dispatcher がブラウザーに応答を転送します。Dispatcher が、レンダーの応答メッセージの本文をキャッシュに追加します。

## ユーザーが承認されていない場合 {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. Dispatcher がキャッシュを調べます。
1. Dispatcher がレンダーにリクエストメッセージを送信します。リクエストメッセージには、ブラウザーのリクエストのヘッダー行がすべて含まれます。
1. レンダーが認証チェッカーサーブレットを呼び出してセキュリティチェックを実行し、失敗すると、Dispatcher に元のリクエストを転送します。
1. Dispatcher が、レンダーに元のリクエストを転送します。
1. レンダーが、AEM オーソライザーサーブレット（このサーブレットは Dispatcher 認証チェッカーサーブレットではありません）を呼び出して、セキュリティチェックを実行します。ユーザーが承認されると、レンダーは応答メッセージの本文にレンダリングされるページを含めます。
1. Dispatcher がブラウザーに応答を転送します。Dispatcher が、レンダーの応答メッセージの本文をキャッシュに追加します。

## 権限に依存するキャッシュの実装 {#implementing-permission-sensitive-caching}

権限を区別するキャッシュを実装するには、次のタスクを実行します。

* 認証と承認を実行するサーブレットの作成
* Dispatcher の設定

>[!NOTE]
>
>一般的に、セキュアなリソースは、セキュアではないファイルとは別のフォルダーに保存します。例：/content/secure/

>[!NOTE]
>
>Dispatcher の前に CDN（またはその他のキャッシュ）がある場合、CDN がプライベートコンテンツをキャッシュしないように、キャッシュヘッダーを設定する必要があります。例：`Header always set Cache-Control private`。
>>AEM as a Cloud Service におけるプライベートキャッシュヘッダーの設定方法について詳しくは、[キャッシュ](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/caching)ページを参照してください。

## 認証チェッカーサーブレットの作成 {#create-the-auth-checker-servlet}

Web コンテンツをリクエストするユーザーの認証と承認を実行するサーブレットを作成し、デプロイします。このサーブレットは、あらゆる認証を使用できます。また、あらゆる承認方法を使用することもできます。例えば、AEM ユーザーアカウントとリポジトリ ACL を使用できます。または、LDAP 検索サービスを使用できます。Dispatcher がレンダーとして使用する AEM インスタンスにサーブレットをデプロイします。

このサーブレットには、すべてのユーザーがアクセスできる必要があります。そのため、サーブレットは、`org.apache.sling.api.servlets.SlingSafeMethodsServlet` クラスを拡張して、システムに対して読み取り専用アクセス権を付与する必要があります。

サーブレットは、レンダーから HEAD 要求のみを受信するので、実装する必要があるのは `doHead` メソッドだけです。

レンダーには、要求されたリソースの URI が HTTP 要求のパラメーターとして含まれます。例えば、承認サーブレットには、`/bin/permissioncheck` からアクセスします。/content/geometrixx-outdoors/en.html ページに対するセキュリティチェックを実行するために、レンダーは HTTP 要求に次の URL を含めます。

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

このサーブレットの応答メッセージには、次の HTTP ステータスコードを含める必要があります。

* 200：認証および承認が成功しました。

次のサンプルサーブレットは、リクエストされたリソースの URL を HTTP リクエストから取得します。このコードでは、Felix SCR の `Property` 注釈を使用して、`sling.servlet.paths` プロパティの値を /bin/permissioncheck に設定しています。`doHead` メソッドでは、サーブレットがセッションオブジェクトを取得し、`checkPermission` メソッドを使用して適切な応答コードを判断します。

>[!NOTE]
>
>sling.servlet.paths プロパティの値は、`Sling` Servlet Resolver （org.apache.sling.servlets.resolver.SlingServletResolver）サービスで有効にする必要があります。

### サンプルサーブレット {#example-servlet}

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

import javax.jcr.Session;

@Component(metatype=false)
@Service
public class AuthcheckerServlet extends SlingSafeMethodsServlet {
 
    @Property(value="/bin/permissioncheck")
    static final String SERVLET_PATH="sling.servlet.paths";
    
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public void doHead(SlingHttpServletRequest request, SlingHttpServletResponse response) {
     try{ 
      //retrieve the requested URL
      String uri = request.getParameter("uri");
      //obtain the session from the request
      Session session = request.getResourceResolver().adaptTo(javax.jcr.Session.class);     
      //perform the permissions check
      try {
       session.checkPermission(uri, Session.ACTION_READ);
       logger.info("authchecker says OK");
       response.setStatus(SlingHttpServletResponse.SC_OK);
      } catch(Exception e) {
       logger.info("authchecker says READ access DENIED!");
       response.setStatus(SlingHttpServletResponse.SC_FORBIDDEN);
      }
     }catch(Exception e){
      logger.error("authchecker servlet exception: " + e.getMessage());
     }
    }
}
```

## 権限を区別するキャッシュ用の Dispatcher の設定 {#configure-dispatcher-for-permission-sensitive-caching}

>[!NOTE]
>
>要件で認証済みドキュメントのキャッシュが許可されている場合は、/cache セクションの /allowAuthorized プロパティを `/allowAuthorized 1` に設定します。詳しくは、[認証使用時のキャッシュ](/help/using/dispatcher-configuration.md)のトピックを参照してください。

dispatcher.any ファイルの auth_checker セクションで、権限を区別するキャッシュの動作を制御します。auth_checker セクションには、次のサブセクションが含まれます。

* `url`：セキュリティチェックを実行するサーブレットの `sling.servlet.paths` プロパティの値。

* `filter`：権限を区別するキャッシュを適用するフォルダーを指定するフィルター。一般的に、`deny` フィルターはすべてのフォルダーに適用され、`allow` フィルターはセキュリティ保護されたフォルダーに適用されます。

* `headers`：承認サーブレットが応答に含める HTTP ヘッダーを指定します。

Dispatcher が起動すると、Dispatcher のログファイルには、次のデバッグレベルのメッセージが含まれます。

`AuthChecker: initialized with URL 'configured_url'.`

次の auth_checker セクションの例では、前のトピックのサーブレットを使用するように Dispatcher を設定します。filter セクションは、権限チェックをセキュアな HTML リソースに対してのみ実行するようにします。

### 設定例 {#example-configuration}

```xml
/auth_checker
  {
  # request is sent to this URL with '?uri=<page>' appended
  /url "/bin/permissioncheck"
      
  # only the requested pages matching the filter section below are checked,
  # all other pages get delivered unchecked
  /filter
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "/content/secure/*.html"
      /type "allow"
      }
    }
  # any header line returned from the auth_checker's HEAD request matching
  # the section below will be returned as well
  /headers
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "Set-Cookie:*"
      /type "allow"
      }
    }
  }
```
