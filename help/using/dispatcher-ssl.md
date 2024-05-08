---
title: Dispatcher での SSL の使用
description: SSL 接続を使用して AEM と通信するよう Dispatcher を設定する方法について説明します。
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '1302'
ht-degree: 100%

---

# Dispatcher での SSL の使用 {#using-ssl-with-dispatcher}

Dispatcher とレンダリングコンピューター間には次の SSL 接続を使用します。

* [一方向 SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [相互 SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>SSL 証明書に関連する操作は、サードパーティ製品に固有です。Adobe Platinum Maintenance and Support 契約の対象ではありません。

## Dispatcher が AEM に接続するときに SSL を使用する {#use-ssl-when-dispatcher-connects-to-aem}

Dispatcher が SSL 接続を使用して AEM または CQ レンダーインスタンスと通信するように設定します。

Dispatcher を設定する前に、SSL を使用するように AEM または CQ を設定します。

* AEM 6.2：[HTTP over SSL の有効化](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.1：[HTTP over SSL の有効化](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* 旧バージョンの AEM：[このページ](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)を参照してください。

### SSL に関連する要求ヘッダー {#ssl-related-request-headers}

HTTPS 要求を受信すると、Dispatcher は AEM または CQ に送信する、以降の要求に次のヘッダーを含めます。

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Apache-2.4 と `mod_ssl` を使用したリクエストには、次の例のようなヘッダーが含まれます。

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### SSL を使用するように Dispatcher を設定する {#configuring-dispatcher-to-use-ssl}

SSL 経由で AEM または CQ と接続するように Dispatcher を設定するには、[dispatcher.any](dispatcher-configuration.md) ファイルに以下のプロパティが必要です。

* HTTPS 要求を処理する仮想ホスト。
* 仮想ホストの `renders` セクションに、HTTPS を使用する CQ または AEM インスタンスのホスト名およびポートを識別するアイテムを含めます。
* `renders` アイテムには、`secure` というプロパティ（値：`1`）が含まれます。

メモ：必要に応じて、HTTP 要求を処理するための別の仮想ホストを作成してください。

次のサンプルの `dispatcher.any` ファイルは、ホスト `localhost`、ポート `8443` で動作している CQ インスタンスに HTTPS を使用して接続するためのプロパティ値を示したものです。

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Dispatcher と AEM 間の相互 SSL の設定 {#configuring-mutual-ssl-between-dispatcher-and-aem}

相互 SSL を使用するように、Dispatcher とレンダーコンピューター（一般的には AEM または CQ パブリッシュインスタンス）間の接続を設定します。

* Dispatcher が SSL 経由でレンダーインスタンスに接続します。
* レンダーインスタンスが Dispatcher の証明書の有効性を確認します。
* Dispatcher が、レンダーインスタンスの証明書の CA が信頼済みであることを確認します。
* （オプション）Dispatcher が、レンダーインスタンスの証明書がレンダーインスタンスのサーバーアドレスと一致することを確認します。

相互 SSL を設定するには、信頼済みの証明機関（CA）によって署名されている証明書が必要です。自己署名証明書では不十分です。証明書に署名するために、ユーザーが CA の役目を果たすことも、サードパーティ CA のサービスを利用することもできます。相互 SSL を設定するには、以下のアイテムが必要です。

* レンダーインスタンスおよび Dispatcher 用の署名済み証明書
* CA 証明書（ユーザーが CA の役割を果たす場合）
* CA、証明書および証明書要求を生成するための OpenSSL ライブラリ

相互 SSL を設定するには、次の手順を実行します。

1. 使用するプラットフォームに適した最新バージョンの Dispatcher を[インストール](dispatcher-install.md)します。SSL をサポートしている Dispatcher バイナリを使用してください（SSL はファイル名に含まれています。例：`dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar`）。
1. Dispatcher およびレンダーインスタンス用に [CA 署名済みの証明書を作成または取得](dispatcher-ssl.md#main-pars-title-3)します。
1. [レンダーの証明書を格納したキーストアを作成](dispatcher-ssl.md#main-pars-title-6)し、レンダーの HTTP サービスを設定します。
1. 相互 SSL 用に [Dispatcher の Web サーバーモジュールを設定](dispatcher-ssl.md#main-pars-title-4)します。

### CA 署名済み証明書の作成または取得 {#creating-or-obtaining-ca-signed-certificates}

パブリッシュインスタンスと Dispatcher を認証する CA 署名済みの証明書を作成または取得します。

#### CA の作成 {#creating-your-ca}

ユーザーが CA の役目を果たす場合は、[OpenSSL](https://www.openssl.org/) を使用して、サーバーとクライアントの証明書に署名する証明機関を作成します。（OpenSSL ライブラリがインストールされている必要があります）。サードパーティ CA を使用する場合は、この手順を実行しないでください。

1. ターミナルを開き、現在のディレクトリを `CA.sh` ファイルを含むディレクトリ（例：`/usr/local/ssl/misc`）に変更します。
1. CA を作成するには、次のコマンドを入力し、プロンプトに従って値を指定します。

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >`openssl.cnf` ファイルのいくつかのプロパティによって、CA.sh スクリプトの動作が制御されます。CA を作成する前に、必要に応じてこのファイルを編集します。

#### 証明書の作成 {#creating-the-certificates}

OpenSSL を使用して証明書要求を作成し、サードパーティ CA に送信するか、自身の CA によって署名します。

証明書を作成する際、OpenSSL は Common Name プロパティを使用して証明書所有者を識別します。レンダーインスタンスの証明書については、証明書を受け入れるように Dispatcher を設定する場合、インスタンスコンピューターのホスト名を Common Name として使用します。これは、証明書がパブリッシュインスタンスのホスト名と一致する場合にのみ実行してください。[DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) プロパティを参照してください。

1. ターミナルを開き、現在のディレクトリを OpenSSL ライブラリの CH.sh ファイルを含むディレクトリに変更します。
1. 次のコマンドを入力し、プロンプトが表示されたら値を指定します。必要に応じて、パブリッシュインスタンスのホスト名を Common Name として使用します。ホスト名は、レンダーの IP アドレスに対して DNS 解決可能な名前です。

   ```shell
   ./CA.sh -newreq
   ```

   サードパーティ CA を使用する場合は、署名するために CA に newreq.pem ファイルを送信します。ユーザーが CA の役目を果たす場合は、手順 3 に進みます。

1. CA の証明書を使用して証明書に署名するには、次のコマンドを入力します。

   ```shell
   ./CA.sh -sign
   ```

   `newcert.pem` と `newkey.pem` という名前の 2 つのファイルが、CA 管理ファイルを含むディレクトリに作成されます。この 2 つのファイルは、それぞれレンダリングコンピューターの公開証明書と秘密鍵です。

1. `newcert.pem` の名前を `rendercert.pem` に変更し、`newkey.pem` の名前を `renderkey.pem` に変更します。
1. 手順 2 と 3 を繰り返して、Dispatcher モジュール用の証明書と公開鍵を作成します。必ず Dispatcher インスタンスに固有の Common Name を使用してください。
1. `newcert.pem` の名前を `dispcert.pem` に変更し、`newkey.pem` の名前を `dispkey.pem` に変更します。

### レンダーコンピューターでの SSL の設定 {#configuring-ssl-on-the-render-computer}

`rendercert.pem` ファイルと `renderkey.pem` ファイルを使用してレンダーインスタンス上の SSL を設定します。

#### レンダリング証明書の JKS（Java™ KeyStore）形式への変換 {#converting-the-render-certificate-to-jks-format}

次のコマンドを使用して、PEM ファイルであるレンダー証明書を PKCS#12 ファイルに変換します。レンダー証明書に署名した CA の証明書も含めます。

1. ターミナルウィンドウで、現在のディレクトリをレンダー証明書と秘密鍵の場所に変更します。
1. PEM ファイルであるレンダー証明書を PKCS#12 ファイルに変換するには、次のコマンドを入力します。レンダー証明書に署名した CA の証明書も含めます。

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. PKCS#12 ファイルを Java™ KeyStore（JKS）形式に変換するには、次のコマンドを入力します。

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Java™ Keystore は、デフォルトのエイリアスを使用して作成されます。必要に応じてエイリアスを変更します。

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### CA 証明書をレンダーの Truststore に追加 {#adding-the-ca-cert-to-the-render-s-truststore}

ユーザーが CA の機役目を果たす場合は、CA 証明書をキーストアに読み込みます。次に、キーストアを信頼するように、レンダーインスタンスを実行する JVM を設定します。

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. テキストエディターを使用して、cacert.pem ファイルを開き、次の行の前にあるすべてのテキストを削除します。

   `-----BEGIN CERTIFICATE-----`

1. 次のコマンドを使用して、証明書をキーストアに読み込みます。

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. キーストアを信頼するようにレンダーインスタンスを実行する JVM を設定するには、次のシステムプロパティを使用します。

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   例えば、crx-quickstart/bin/quickstart スクリプトを使用してパブリッシュインスタンスを起動する場合、CQ_JVM_OPTS プロパティを次のように変更できます。

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### レンダーインスタンスの設定 {#configuring-the-render-instance}

SSL を使用するようにレンダーインスタンスの HTTP サービスを設定するには、「*`Enable SSL on the Publish Instance`*」セクションの指示に従ってレンダー証明書を使用します。

* AEM 6.2：[HTTP over SSL の有効化](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.2：[HTTP over SSL の有効化](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* 旧バージョンの AEM：[このページ](https://experienceleague.adobe.com/ja/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)を参照してください。

### Dispatcher モジュール用の SSL の設定 {#configuring-ssl-for-the-dispatcher-module}

相互 SSL を使用するように Dispatcher を設定するには、Dispatcher 証明書を準備して、Web サーバーモジュールを設定します。

### Dispatcher の統合証明書の作成 {#creating-a-unified-dispatcher-certificate}

Dispatcher 証明書と暗号化されていない秘密鍵を組み合わせて、単一の PEM ファイルにします。テキストエディターまたは `cat` コマンドを使用して、以下のサンプルのようなファイルを作成します。

1. ターミナルを開き、現在のディレクトリを dispkey.pem ファイルがある場所に変更します。
1. 秘密鍵を復号化するには、次のコマンドを入力します。

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. テキストエディターまたは `cat` コマンドを使用して、暗号化されていない秘密鍵と証明書を組み合わせ、次のサンプルのような単一のファイルにします。

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Dispatcher 用に使用する証明書の指定 {#specifying-the-certificate-to-use-for-dispatcher}

以下のプロパティを（`httpd.conf` の）[Dispatcher モジュールの設定](dispatcher-install.md#main-pars-55-35-1022)に追加します。

* `DispatcherCertificateFile`：公開証明書と暗号化されていない秘密鍵を含む、Dispatcher の統合証明書ファイルへのパス。このファイルは、SSL サーバーが Dispatcher のクライアント証明書を要求する場合に使用します。
* `DispatcherCACertificateFile`：SSL サーバーが提示する CA がルート証明機関によって信頼されていない場合に使用する、CA 証明書ファイルへのパス。
* `DispatcherCheckPeerCN`：リモートサーバー証明書のホスト名チェックを有効（`On`）にするか無効（`Off`）にするか。

以下のコードは設定のサンプルです。

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
