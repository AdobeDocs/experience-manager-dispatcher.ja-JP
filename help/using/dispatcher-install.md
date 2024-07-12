---
title: Dispatcher のインストール
description: Microsoft® Internet Information Server、Apache web サーバーおよび Sun Java™ Web Server-iPlanet に Dispatcher モジュールをインストールする方法について説明します。
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: 9be9f5935c21ebbf211b5da52280a31772993c2e
workflow-type: tm+mt
source-wordcount: '3748'
ht-degree: 100%

---

# Dispatcher のインストール {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

使用するオペレーティングシステムと Web サーバー向けの最新の Dispatcher インストールファイルを取得するには、[Dispatcher リリースノート](release-notes.md)を使用します。Dispatcher のリリース番号は Adobe Experience Manager のリリース番号とは無関係で、Adobe Experience Manager 6.x、5.x および Adobe CQ 5.x リリースと互換性があります。

>[!NOTE]
>
>Adobe Experience Manager 6.5 ではバージョン 4.3.2 以降の Dispatcher が必要です。ただし、Dispatcher のバージョンは AEM から独立しています。例えば、Dispatcher バージョン 4.3.2 は Adobe Experience Manager 6.4 とも互換性があります。

次のファイル命名規則が使用されます。

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

例えば、`dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` ファイルには、Linux® i686 上で動作する Apache 2.4 web サーバー向けの Dispatcher バージョン 4.3.1 が含まれていて、**tar** 形式を使用してパッケージ化されています。

各 Web サーバーのファイル名に使用される Web サーバー識別子を以下の表に示します。

| Web サーバー | インストールキット |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters> |
| Microsoft® Internet Information Server 7.5、8、8.5、10 | dispatcher-**iis**-&lt;other parameters> |
| Sun Java™ Web Server iPlanet | dispatcher-**ns**-&lt;other parameters> |

>[!CAUTION]
>
>使用するプラットフォームで利用可能な最新バージョンの Dispatcher をインストールします。強化された機能を利用できるよう、毎年 Dispatcher インスタンスをアップグレードして、最新バージョンを使用します。

>[!NOTE]
>
>特にバージョン 4.3.3 からバージョン 4.3.4 にアップグレードするお客様は、キャッシュできないコンテンツに対するキャッシュヘッダーの設定方法の動作が異なることに気づくはずです。この変更について詳しくは、[リリースノート](/help/using/release-notes.md#nov)ページを参照してください。

アーカイブごとに次のファイルが含まれています。

* Dispatcher モジュール
* サンプルの設定ファイル
* インストール手順と最新の情報を含む README ファイル
* 現在および過去のリリースで修正された問題を記載した CHANGES ファイル

>[!NOTE]
>
>インストールを開始する前に、最新の変更やプラットフォーム固有の注意事項がないか README ファイルを確認します。

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific Web server installation procedures.</p>

 -->

## Microsoft® Internet Information Server {#microsoft-internet-information-server}

この web サーバーのインストール方法については、次のリソースを参照してください。

* インターネットインフォメーションサーバーに関する Microsoft® 独自のドキュメント
* [「Microsoft® IIS 公式サイト」](https://www.iis.net/)

### 必要な IIS コンポーネント {#required-iis-components}

IIS バージョン 8.5 および 10 には、以下の IIS コンポーネントがインストールされている必要があります。

* ISAPI 拡張機能

Web サーバー（IIS）の役割も追加する必要があります。役割とコンポーネントを追加するには、Server Manager を使用します。

## Microsoft® IIS - Dispatcher モジュールのインストール {#microsoft-iis-installing-the-dispatcher-module}

Microsoft® Internet Information System には、次のようなアーカイブが必要です。

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

ZIP ファイルには以下のファイルが含まれます。

| ファイル | 説明 |
|--- |--- |
| `disp_iis.dll` | Dispatcher のダイナミックリンクライブラリファイル。 |
| `disp_iis.ini` | IIS 用の設定ファイル。このサンプルを要件に合わせて更新できます。**注意**：ini ファイルの name-root は dll と同じである必要があります。 |
| `dispatcher.any` | Dispatcher 用のサンプルの設定ファイル。 |
| `author_dispatcher.any` | オーサーインスタンスで動作する Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。**メモ**：インストールを開始する前に、このファイルを確認します。 |
| 変更 | 現在および過去のリリースで修正された問題を記載したファイルを変更します。 |

以下の手順を実行して、Dispatcher ファイルを適切な場所にコピーします。

1. Windows エクスプローラーを使用して、`<IIS_INSTALLDIR>/Scripts` ディレクトリを作成します。例：`C:\inetpub\Scripts`

1. 以下のファイルを Dispatcher パッケージからこの Scripts ディレクトリに抽出します。

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Dispatcher が AEM オーサーインスタンスで動作しているか、パブリッシュインスタンスで動作しているかによって、以下のファイルのどちらか
      * オーサーインスタンス：`author_dispatcher.any`
      * パブリッシュインスタンス：`dispatcher.any`

## Microsoft® IIS - Dispatcher の INI ファイルの設定 {#microsoft-iis-configure-the-dispatcher-ini-file}

Dispatcher のインストールを設定するには、`disp_iis.ini` ファイルを編集します。`.ini` ファイルの基本的なフォーマットを次に示します。

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

各プロパティについて、以下の表に説明します。

| パラメーター | 説明 |
|--- |--- |
| `configpath` | ローカルファイルシステム内の `dispatcher.any` の場所（絶対パス）。 |
| `logfile` | `dispatcher.log` ファイルの場所。この場所が設定されていない場合、ログメッセージは Windows のイベントログに出力されます。 |
| `loglevel` | イベントログへのメッセージ出力に使用するログレベルを定義します。次の値は、ログファイルのログレベルで指定できます。<br/>0 - エラーメッセージのみ。<br/>1 - エラーと警告。<br/>2 - エラー、警告および情報メッセージ<br/>3 - エラー、警告、情報メッセージおよびデバッグメッセージ。<br/>**メモ**：インストールおよびテスト時はログレベルを 3 に、実稼動環境で実行する場合は 0 に設定します。 |
| `replaceauthorization` | HTTP 要求内の認証ヘッダーの処理方法を指定します。有効な値は次のとおりです。<br/>0 - 認証ヘッダーは変更されていません。<br/>1 -「Basic」を除く、「Authorization」という名前のすべてのヘッダーを、その「`Basic <IIS:LOGON\_USER>`」と同等のものに置き換えます。<br/> |
| `servervariables` | サーバー変数の処理方法を定義します。<br/>0 - IIS サーバー変数は Dispatcher および AEM に送信されません。<br/>1 - すべての IIS サーバー変数（`LOGON\_USER, QUERY\_STRING, ...` など）は、要求ヘッダー付きで Dispatcher に（およびキャッシュされていない場合は AEM インスタンスにも）送信されます。<br/>サーバー変数は、`AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` など多数あります。すべてのサーバー変数とその詳細の一覧は、IIS のドキュメントを参照してください。 |
| `enable_chunked_transfer` | クライアント応答のチャンク転送を有効（1）または無効（0）にするかを定義します。デフォルト値は 0 です。 |

設定例：

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Microsoft® IIS の設定 {#configuring-microsoft-iis}

Dispatcher ISAPI モジュールを統合するように IIS を設定します。IIS では、ワイルドカードアプリケーションマッピングを使用します。

### 匿名アクセスの設定 - IIS 8.5 および 10 {#configuring-anonymous-access-iis-and}

オーサーインスタンス上のデフォルトのフラッシュレプリケーションエージェントは、フラッシュ要求と一緒にセキュリティ資格情報を送信しないように設定されています。したがって、Dispatcher キャッシュとして使用する web サイトでは、匿名アクセスを許可する必要があります。

Web サイトが認証メソッドを使用する場合は、それに応じてフラッシュレプリケーションエージェントを設定する必要があります。

1. IIS Manager を開き、Dispatcher キャッシュとして使用する Web サイトを選択します。
1. 機能ビューモードを使用して、「IIS」セクションで「認証」をダブルクリックします。
1. 匿名認証が有効でない場合は「匿名認証」を選択し、「操作」領域で「有効」をクリックします。

### Dispatcher ISAPI モジュールの統合 - IIS 8.5 および 10 {#integrating-the-dispatcher-isapi-module-iis-and}

以下の手順を実行して、Dispatcher ISAPI モジュールを IIS に追加します。

1. IIS Manager を開きます。
1. Dispatcher キャッシュとして使用する Web サイトを選択します。
1. 機能ビューモードを使用して、「IIS」セクションで「ハンドラーマッピング」をダブルクリックします。
1. ハンドラーマッピングページの操作パネルで、「ワイルドカードスクリプトマップを追加」をクリックし、次のプロパティ値を追加して、「OK」をクリックします。

   * 要求パス：&#42;
   * 実行可能ファイル：disp_iis.dll ファイルの絶対パス。例：`C:\inetpub\Scripts\disp_iis.dll`
   * 名前：ハンドラーマッピングのわかりやすい名前。例：`Dispatcher`

1. 表示されるダイアログボックスで、disp_iis.dll ライブラリを ISAPI および CGI の制限リストに追加して、「**はい**」をクリックします。

   IIS 7.0 および 7.5 の場合、設定は完了です。IIS 8.0 を設定する場合は、残りの手順を続行します。

1. （IIS 8.0）ハンドラーマッピングのリストで、作成したハンドラーマッピングを選択し、アクション領域で「編集」をクリックします。
1. （IIS 8.0）スクリプトマップの編集ダイアログボックスで、「リクエストの制限」ボタンをクリックします。
1. （IIS 8.0）ハンドラーをまだキャッシュされていないファイルおよびフォルダー用に使用するには、「**要求のマップ先が次の場合のみハンドラーを呼び出す**」をオフにします。「**OK**」をクリックします。
1. （IIS 8.0）スクリプトマップの編集ダイアログボックスで、「OK」をクリックします。

### キャッシュへのアクセス権の設定 - IIS 8.5 および 10 {#configuring-access-to-the-cache-iis-and}

デフォルトのアプリケーションプールユーザーに、Dispatcher キャッシュとして使用するフォルダーへの書き込みアクセス権を付与します。

1. Dispatcher キャッシュとして使用する Web サイトのルートフォルダー（`C:\inetpub\wwwroot` など）を右クリックして、「プロパティ」をクリックします。
1. 「セキュリティ」タブで「編集」をクリックし、権限ダイアログボックスで「追加」をクリックします。ユーザーアカウントを選択するためのダイアログボックスが表示されます。「ロケーション」ボタンをクリックし、コンピューター名を選択して、「OK」をクリックします。

   次の手順が完了するまで、このダイアログボックスは開いたままにします。

1. IIS Manager で、Dispatcher キャッシュに使用する IIS サイトを選択し、ウィンドウの右側で「詳細設定」をクリックします。
1. Application Pool プロパティの値を選択し、クリップボードにコピーします。
1. 開いているダイアログボックスに戻ります。「選択するオブジェクト名を入力してください」ボックスに「`IIS AppPool\`」と入力してから、クリップボードの内容を貼り付けます。値は次の例のようになります。

   `IIS AppPool\DefaultAppPool`

1. 「名前の確認」ボタンをクリックします。Windows によってユーザーアカウントが解決されたら、「OK」をクリックします。
1. Dispatcher フォルダーの権限ダイアログボックスで、追加したアカウントを選択し、アカウントに対するすべての権限（**フルコントロール以外**）を有効にして、「OK」をクリックします。「OK」をクリックして、フォルダーのプロパティダイアログボックスを閉じます。

### JSON の MIME タイプの登録 - IIS 8.5 および 10 {#registering-the-json-mime-type-iis-and}

Dispatcher で JSON 呼び出しを許可する場合、以下の手順を実行して、JSON の MIME タイプを登録します。

1. IIS Manager で、web サイトを選択し、機能ビューを使用して、「MIME タイプ」をダブルクリックします。
1. JSON の拡張子がリストにない場合は、操作パネルで「追加」をクリックし、以下のプロパティ値を入力して、「OK」をクリックします。

   * ファイル名拡張子：`.json`
   * MIME Type: `application/json`

### 非表示セグメント bin の削除 - IIS 8.5 および 10 {#removing-the-bin-hidden-segment-iis-and}

以下の手順を実行して、非表示セグメント `bin` を削除します。新しくない Web サイトには、この非表示セグメントが含まれていることがあります。

1. IIS Manager で、web サイトを選択し、機能ビューを使用して、「要求のフィルタリング」をダブルクリックします。
1. `bin` セグメントを選択し、「削除」をクリックしてから、確認ダイアログボックスで「はい」をクリックします。

### IIS メッセージのファイルへの記録 - IIS 8.5 および 10 {#logging-iis-messages-to-a-file-iis-and}

以下の手順を実行して、Dispatcher のログメッセージを Windows のイベントログではなくログファイルに書き込みます。Dispatcher がログファイルを使用するように設定し、そのファイルへの書き込みアクセス権を IIS に付与します。

1. Windows エクスプローラーを使用して、IIS インストール環境の logs フォルダーの下に `dispatcher` というフォルダーを作成します。一般的なインストール環境のこのフォルダーのパスは、`C:\inetpub\logs\dispatcher` です。

1. Dispatcher フォルダーを右クリックして、「**プロパティ**」をクリックします。
1. 「セキュリティ」タブで、「**編集**」をクリックします。
1. 権限ダイアログボックスで、「**追加**」をクリックします。ユーザーアカウントを選択するためのダイアログボックスが表示されます。「ロケーション」ボタンをクリックし、コンピューター名を選択して、「OK」をクリックします。

   次の手順が完了するまで、このダイアログボックスは開いたままにします。

1. IIS Manager で、Dispatcher キャッシュに使用する IIS サイトを選択し、ウィンドウの右側で「詳細設定」をクリックします。
1. Application Pool プロパティの値を選択し、クリップボードにコピーします。
1. 開いているダイアログボックスに戻ります。「選択するオブジェクト名を入力してください」ボックスに「`IIS AppPool\`」と入力してから、クリップボードの内容を貼り付けます。値は次の例のようになります。

   `IIS AppPool\DefaultAppPool`

1. 「名前の確認」ボタンをクリックします。Windows によってユーザーアカウントが解決されたら、「OK」をクリックします。
1. Dispatcher フォルダーの権限ダイアログボックスで、追加したアカウントを選択し、アカウントに対するすべての権限（**フルコントロール以外**）を有効にして、「OK」をクリックします。「OK」をクリックして、フォルダーのプロパティダイアログボックスを閉じます。
1. テキストエディターを使用して、`disp_iis.ini` ファイルを開きます。
1. ログファイルの場所を設定するには、次の例のようなテキスト行を追加し、ファイルを保存します。

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### 次の手順 {#next-steps}

Dispatcher の使用を開始する前に、次のことを理解しておく必要があります。

* [Dispatcher の設定](dispatcher-configuration.md)
* Dispatcher と連携するように [AEM を設定](page-invalidate.md)します。

## Apache Web サーバー {#apache-web-server}

>[!CAUTION]
>
>インストール手順は、**Windows** の場合と **UNIX®** の場合の両方について記載されています。手順は慎重に実行してください。

### Apache Web サーバーのインストール {#installing-apache-web-server}

Apache Web サーバーのインストールについては、[オンライン](https://httpd.apache.org/)またはディストリビューション内のインストールマニュアルを参照してください。

>[!CAUTION]
>
>ソースファイルをコンパイルして Apache バイナリファイルを作成する場合は、必ず **`dynamic modules support`** を有効にしてください。このオプションを有効にするには、任意の **--enable-shared** オプションを使用します。少なくとも、`mod_so` モジュールを含めます。
>
>詳しくは、Apache web サーバーのインストールマニュアルを参照してください。

Apache HTTP サーバーの[セキュリティに関するヒント](https://httpd.apache.org/docs/2.4/misc/security_tips.html)および[セキュリティレポート](https://httpd.apache.org/security_report.html)も参照してください。

### Apache Web サーバー - Dispatcher モジュールの追加 {#apache-web-server-add-the-dispatcher-module}

Dispatcher は次のいずれかの形式で提供されます。

* **Windows**：ダイナミックリンクライブラリ（DLL）
* **UNIX®**：動的共有オブジェクト（DSO）

インストールアーカイブファイルには、次のファイルが含まれています。選択した環境が Windows か UNIX® かによって異なります。

| ファイル | 説明 |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows：Dispatcher のダイナミックリンクライブラリファイル。 |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | UNIX®：Dispatcher の共有オブジェクトライブラリファイル。 |
| mod_dispatcher.so | UNIX®：サンプルリンク。 |
| http.conf.disp&lt;x> | Apache サーバー用のサンプル設定ファイル。 |
| dispatcher.any | Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。**メモ**：インストールを開始する前に、このファイルを確認します。 |
| 変更 | 現在および過去のリリースで修正された問題を記載したファイルを変更します。 |

次の手順を実行して、Apache Web サーバーに Dispatcher を追加します。

1. Dispatcher ファイルを、適切な Apache モジュールディレクトリに配置します。

   * **Windows**：`disp_apache<x.y>.dll` を、`<APACHE_ROOT>/modules` に配置します。
   * **UNIX®**：インストール環境に応じて、`<APACHE_ROOT>/libexec` ディレクトリまたは `<APACHE_ROOT>/modules` ディレクトリを指定します。\
     `dispatcher-apache<options>.so` を、このディレクトリにコピーします。\
     長期的な保守を簡単にするために、`mod_dispatcher.so` という名前で Dispatcher へのシンボリックリンクを作成することもできます。\
     `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. dispatcher.any ファイルを `<APACHE_ROOT>/conf` ディレクトリにコピーします。

   **メモ：** Dispatcher モジュールの DispatcherLog プロパティが適切に設定されていれば、このファイルを別の場所に配置できます（以下の Dispatcher 固有の設定エントリを参照してください）。

### Apache Web サーバー - SELinux プロパティの設定 {#apache-web-server-configure-selinux-properties}

Red Hat® Linux Kernel 2.6 上で SELinux を有効にして Dispatcher を実行する場合、Dispatcher のログファイルに次のようなエラーメッセージが書き込まれることがあります。

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

このエラーは、SELinux セキュリティの有効化が原因の可能性があります。その場合は、次のタスクを実行します。

* Dispatcher モジュールファイルの SELinux コンテキストを設定する。
* ネットワーク接続を行う HTTPD スクリプトおよびモジュールを有効にする。
* キャッシュされたファイルを保存するドキュメントルートの SELinux コンテキストを設定する。

`[path to the dispatcher.so file]` を Apache Web サーバーにインストールした Dispatcher モジュールへのパスで置き換え、*`path to the docroot`* を docroot があるパス（例：`/opt/cq/cache`）で置き換えて、以下のコマンドをターミナルウィンドウに入力します。

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_rw_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_rw_content_t "[path to the docroot](/.*)?"
```

### Apache Web サーバー - Dispatcher 用の Apache Web サーバーの設定 {#apache-web-server-configure-apache-web-server-for-dispatcher}

Apache Web サーバーは、`httpd.conf` を使用して設定する必要があります。Dispatcher インストールキットには、`httpd.conf.disp<x>` というサンプルの設定ファイルがあります。

以下の手順は必ずおこなってください。

1. `<APACHE_ROOT>/conf` に移動します。
1. `httpd.conf` を開いて編集します。
1. 次の設定エントリを説明の順に追加する必要があります。

   * **LoadModule**：起動時のモジュールを読み込みます。
   * Dispatcher 固有の設定エントリ（**DispatcherConfig、DispatcherLog**、**DispatcherLogLevel** など）。
   * **SetHandler**：Dispatcher をアクティベートします。**LoadModule**
   * **ModMimeUsePathInfo**：**mod_mime** の動作を設定します。

1. （オプション）htdocs ディレクトリの所有者を変更することをお勧めします。

   * Apache サーバーはルートとして起動しますが、子プロセスはデーモンとして起動します（セキュリティのため）。ドキュメントルート（`<APACHE_ROOT>/htdocs`）は、ユーザーデーモンに属している必要があります。

     ```xml
     cd <APACHE_ROOT>  
     chown -R daemon:daemon htdocs
     ```

**LoadModule**

次の表に、使用例を示します。実際のエントリは、使用する Apache Web サーバーによって異なります。

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| UNIX®（シンボリックリンクと仮定） | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>各ステートメントの最初のパラメーターは、以上の例のとおりに記述する必要があります。
>
>このコマンドについて詳しくは、付属のサンプルの設定ファイル、および Apache Web サーバーのドキュメントを参照してください。

**Dispatcher 固有の設定エントリ**

Dispatcher 固有の設定エントリは、LoadModule エントリの後に配置されます。次の表に設定例を示します。この例は UNIX® でも Windows でも使用できます。

**Windows および UNIX®**

```
...
<IfModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

>[!NOTE]
>
>特にバージョン 4.3.3 からバージョン 4.3.4 にアップグレードするお客様は、キャッシュできないコンテンツに対するキャッシュヘッダーの設定方法の動作が異なることに気づくはずです。この変更について詳しくは、[リリースノート](/help/using/release-notes.md#nov)ページを参照してください。

個別の設定パラメーターの内容：

| パラメーター | 説明 |
|--- |--- |
| DispatcherConfig | Dispatcher 設定ファイルの場所と名前。<br/>このプロパティがメインサーバー設定にある場合、すべての仮想ホストがプロパティ値を継承します。ただし、仮想ホストに DispatcherConfig プロパティを含めて、メインサーバー設定をオーバーライドできます。 |
| DispatcherLog | ログファイルの場所と名前。 |
| DispatcherLogLevel | ログファイルのログレベル：<br/>0 - エラー <br/> 1 - 警告 <br/>2 - 情報 <br/>3 - デバッグ <br/>**メモ**：インストールおよびテスト時はログレベルを 3 に設定し、実稼動環境で実行する場合は 0 に設定します。 |
| DispatcherNoServerHeader | *このパラメーターは非推奨であり、無効です。*<br/><br/>&#x200B;使用するサーバーヘッダーを定義します。<br/><ul><li>未定義または 0 - HTTP サーバーヘッダーには AEM バージョンが含まれます。 </li><li>1 - Apache サーバーヘッダーを使用します。</li></ul> |
| DispatcherDeclineRoot | ルート「/」への要求を拒否するかどうかを定義します。<br/>**0** - / への要求を受け入れます。<br/>**1** - / への要求を Dispatcher は処理しません。代わりに、適切なマッピングを行うには mod_alias を使用してください。 |
| DispatcherUseProcessedURL | Dispatcher による以降の処理のすべてに、事前に処理された URL を使用するかどうかを定義します。<br/>**0** - Web サーバーに渡された元の URL を使用します。<br/>**1** - Dispatcher は、web サーバーに渡された元の URL の代わりに、Dispatcher に先行するハンドラー（つまり、`mod_rewrite`）が既に処理した URL を使用します。例えば、元の URL または処理された URL のどちらかが Dispatcher のフィルターと一致する場合などです。URL は、キャッシュファイル構造の基礎としても使用されます。mod_rewrite について詳しくは、Apache web サイトのドキュメント（Apache 2.4 など）を参照してください。mod_rewrite を使用する場合は、フラグ「passthrough」（次のハンドラーにパススルー）を使用して、書き換えエンジンが内部 request_rec 構造の URI フィールドをファイル名フィールドの値に設定するように強制します。 |
| DispatcherPassError | ErrorDocument 処理のエラーコードのサポート方法を定義します。<br/>**0** - Dispatcher はクライアントへのすべてのエラー応答をスプールします。<br/>**1** - Dispatcher は、クライアントへのエラー応答をスプールしません（ステータスコードが 400 以上の場合）。代わりに、ステータスコードを Apache に渡すと、ErrorDocument ディレクティブでこのようなステータスコードを処理できます。<br/>**コード範囲** - 応答を Apache に渡すエラーコードの範囲を指定します。その他のエラーコードはクライアントに渡されます。例えば、次の設定では、エラー 412 の応答をクライアントに渡し、その他すべてのエラーを Apache に渡します。DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | キープアライブタイムアウトを秒単位で指定します。Dispatcher バージョン 4.2.0 以降では、デフォルトのキープアライブ値は 60 です。値 0 はキープアライブを無効にします。 |
| DispatcherNoCanonURL | このパラメーターをオンに設定すると、正規化された URL ではなく生の URL がバックエンドに渡され、DispatcherUseProcessedURL の設定が上書きされます。デフォルト値はオフです。<br/>**メモ**：Dispatcher 設定内のフィルタールールは、常に生の URL ではなく、サニタイズされた URL に対して評価されます。 |

>[!NOTE]
>
>パスエントリには、Apache web サーバーのルートディレクトリの相対パスを指定します。

>[!NOTE]
>
>サーバーヘッダーのデフォルトの設定は次のとおりです。
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>この設定は、（統計目的で）AEM バージョンを示します。このような情報をヘッダー内で利用できないようにするには、次のように設定します。
>
>`ServerTokens Prod`
>
>詳しくは、[ServerTokens 命令に関する Apache ドキュメント（Apache 2.4 の場合など）](https://httpd.apache.org/docs/2.4/mod/core.html)を参照してください。

**SetHandler**

前述のエントリの後に、**SetHandler** ステートメントを設定のコンテキスト（`<Directory>`、`<Location>`）に追加し、Dispatcher が受信する要求を処理できるようにする必要があります。次の例では、Web サイト全体に対する要求を処理するように Dispatcher を設定します。

**Windows および UNIX®**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

次の例では、仮想ドメインに対する要求を処理するように Dispatcher を設定します。

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**UNIX®**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>**SetHandler** ステートメントのパラメーターは、モジュールで定義されているハンドラーの名前なので、*上記の例とまったく同じ*&#x200B;ように記述する必要があります。
>
>このコマンドについて詳しくは、付属のサンプルの設定ファイル、および Apache Web サーバーのドキュメントを参照してください。

**ModMimeUsePathInfo**

**SetHandler** ステートメントの後に、**ModMimeUsePathInfo** の定義も追加する必要があります。

>[!NOTE]
>
>Dispatcher バージョン 4.0.9 以降を使用している場合にのみ、`ModMimeUsePathInfo` パラメーターを使用および設定します。
>
>Dispatcher バージョン 4.0.9 は 2011年にリリースされました。それ以前のバージョンを使用している場合は、最新の Dispatcher バージョンにアップグレードすることをお勧めします。

すべての Apache 設定で、次のように **ModMimeUsePathInfo** パラメーターを `On` にする必要があります。

`ModMimeUsePathInfo On`

mod_mime モジュール（例：[Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)）は、コンテンツメタデータを HTTP 応答用に選択されたコンテンツに割り当てる際に使用するものです。デフォルトの設定では、`mod_mime` によってコンテンツタイプが決定されます。そのため、ファイルまたはディレクトリにマッピングされる URL の部分のみが考慮されます。

`ModMimeUsePathInfo`パラメーターを `On` にすると、`mod_mime` によるコンテンツタイプの指定が&#x200B;*完全な* URL に基づいて行われます。つまり、仮想リソースには拡張子に基づいてメタ情報が適用されます。

以下に **ModMimeUsePathInfo** のアクティベートの例を示します。

**Windows および UNIX®**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### HTTPS のサポートの有効化（UNIX® および Linux®）{#enable-support-for-https-unix-and-linux}

Dispatcher は、OpenSSL を使用して HTTP 経由でのセキュアな通信を実装します。Dispatcher バージョン **4.2.0** からは、OpenSSL 1.0.0 および OpenSSL 1.0.1 がサポートされています。デフォルトでは、Dispatcher は OpenSSL 1.0.0 を使用します。OpenSSL 1.0.1 を使用するには、以下の手順を実行して、Dispatcher がインストールされている OpenSSL ライブラリを使用できるように、シンボリックリンクを作成します。

1. ターミナルを開き、現在のディレクトリを OpenSSL ライブラリがインストールされているディレクトリに変更します。例を以下に示します。

   ```shell
   cd /usr/lib64
   ```

1. シンボリックリンクを作成するには、以下のコマンドを入力します。

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>カスタマイズバージョンの Apache を使用している場合は、Apache と Dispatcher が同じバージョンの [OpenSSL](https://www.openssl.org/source/) を使用してコンパイルされていることを確認してください。

### 次の手順 {#next-steps-1}

Dispatcher の使用を開始する前に、次の作業を実行する必要があります。

* [Dispatcher の設定](dispatcher-configuration.md)
* Dispatcher と連携するように [AEM を設定](page-invalidate.md)します。

## Sun Java™ System Web Server／iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Windows と UNIX® の両方の環境での手順を記載しています。
>
>実行する手順を選択する際に注意してください。

### Sun Java™ System Web Server／iPlanet - Web サーバーのインストール {#sun-java-system-web-server-iplanet-installing-your-web-server}

次の web サーバーのインストール方法について詳しくは、各サーバーのドキュメントを参照してください。

* Sun Java™ System Web Server
* iPlanet Web Server

### Sun Java™ System Web Server／iPlanet - Dispatcher モジュールの追加 {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Dispatcher は次のいずれかの形式で提供されます。

* **Windows**：ダイナミックリンクライブラリ（DLL）
* **UNIX®**：動的共有オブジェクト（DSO）

インストールアーカイブファイルには、次のファイルが含まれています。選択した環境が Windows か UNIX® かによって異なります。

| ファイル | 説明 |
|---|---|
| `disp_ns.dll` | Windows：Dispatcher のダイナミックリンクライブラリファイル。 |
| `dispatcher.so` | UNIX®：Dispatcher の共有オブジェクトライブラリファイル。 |
| `dispatcher.so` | UNIX®：サンプルリンク。 |
| `obj.conf.disp` | iPlanet／Sun Java™ System Web Server 用のサンプルの設定ファイル。 |
| `dispatcher.any` | Dispatcher 用のサンプルの設定ファイル。 |
| README | インストール手順と最新の情報を含む Readme ファイル。**メモ**：インストールを開始する前に、このファイルを確認します。 |
| 変更 | 現在および過去のリリースで修正された問題を記載したファイルを変更します。 |

次の手順を実行して、Web サーバーに Dispatcher を追加します。

1. 次の Dispatcher ファイルを、Web サーバーの `plugin` ディレクトリに配置します。

### Sun Java™ System Web Server／iPlanet - Dispatcher 用の設定 {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Web サーバーは、`obj.conf` を使用して設定する必要があります。Dispatcher インストールキットには、`obj.conf.disp` というサンプルの設定ファイルがあります。

1. `<WEBSERVER_ROOT>/config` に移動します。
1. `obj.conf` を開いて編集します。
1. 次で始まる行をコピーします。\
   `Service fn="dispService"`\
   `obj.conf.disp` から `obj.conf` の初期化セクションにコピーします。

1. 変更内容を保存します。
1. `magnus.conf` を開いて編集します。
1. 次のように始まる 2 つの行をコピーします。\
   `Init funcs="dispService, dispInit"`\
   および\
   `Init fn="dispInit"`\
   `obj.conf.disp` から `magnus.conf` の初期化セクションにコピーします。

1. 変更内容を保存します。

>[!NOTE]
>
>次の設定はすべて 1 行にする必要があります。また、`$(SERVER_ROOT)` と `$(PRODUCT_SUBDIR)` はそれぞれの値に置き換える必要があります。

**Init**

次の表に、使用例を示します。実際のエントリは、使用する Web サーバーによって異なります。

**Windows および UNIX®**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

条件：

| パラメーター | 説明 |
|--- |--- |
| `config` | 設定ファイル `dispatcher.any.` の場所と名前。 |
| `logfile` | ログファイルの場所と名前。 |
| `loglevel` | ログファイルにメッセージを書き込む際のログレベル：<br/>**0** - エラー <br/>**1** - 警告 <br/>**2** - 情報 <br/>**3** - デバッグ <br/>**メモ**：インストールおよびテスト時はログレベルを 3 に設定し、実稼動環境で実行する場合は 0 に設定します。 |
| `keepalivetimeout` | キープアライブタイムアウトを秒単位で指定します。Dispatcher バージョン 4.2.0 以降では、デフォルトのキープアライブ値は 60 です。値 0 はキープアライブを無効にします。 |

要件に応じて、Dispatcher をオブジェクトのサービスとして定義できます。Web サイト全体に Dispacher を設定するには、デフォルトのオブジェクトを次のように編集します。

**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**UNIX®**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### 次の手順 {#next-steps-2}

Dispatcher の使用を開始する前に、次の作業を実行する必要があります。

* [Dispatcher の設定](dispatcher-configuration.md)
* Dispatcher と連携するように [AEM を設定](page-invalidate.md)します。
