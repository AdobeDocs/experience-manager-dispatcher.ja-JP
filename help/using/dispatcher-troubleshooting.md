---
title: Dispatcher に関する問題のトラブルシューティング
description: Dispatcher に関する問題のトラブルシューティングについて説明します。
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: ht
source-wordcount: '472'
ht-degree: 100%

---

# Dispatcher に関する問題のトラブルシューティング {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher のバージョンは AEM とは無関係です。ただし、Dispatcher のドキュメントは AEM のドキュメントに組み込まれています。最新バージョンの AEM のドキュメントに組み込まれている Dispatcher のドキュメントを必ず使用してください。
>
>Dispatcher のドキュメントへのリンクをたどると、このページにリダイレクトされる可能性があります。このリンクは、旧バージョンの AEM のドキュメントに埋め込まれていたものです。

>[!NOTE]
>
>詳しくは、<!-- URL is 404[Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), -->[Dispatcher のフラッシュ問題のトラブルシューティング](https://experienceleague.adobe.com/search.html?lang=ja#q=troubleshooting%20dispatcher%20flushing%20issues&sort=relevancy&f:el_product=[Experience%20Manager])および [Dispatcher に関する主な問題とよくある質問](dispatcher-faq.md)を参照してください。

## 基本設定の確認 {#check-the-basic-configuration}

通常どおり、基本事項を確認することが最初の手順です。

* [基本操作の確認](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Web サーバーおよび Dispatcher のログファイルをすべて確認します。必要に応じて、Dispatcher の[ログ](/help/using/dispatcher-configuration.md#logging)に使用する `loglevel` を増やします。

* [設定の確認](/help/using/dispatcher-configuration.md)：

   * 複数の Dispatcher があるか。

      * 調査中の Web サイトまたはページを処理している Dispatcher が特定されているか。

   * フィルターが実装されているか。

      * これらのフィルターが調査中の問題に影響しているか。

## IIS 診断ツール {#iis-diagnostic-tools}

IIS には、実際のバージョンに応じて様々なトレースツールがあります。

* IIS 6 - IIS 診断ツールをダウンロードし設定できます。
* IIS 7 - トレース機能が完全に組み込まれています。

これらのツールを使用して、アクティビティを監視できます。

<!-- Both URLs in this topic 404! >
## IIS and 404 Not Found {#iis-and-not-found}

When using IIS, you might experience `404 Not Found` being returned in various scenarios. If so, see the following Knowledge Base articles.

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Requests to URLs that contain the base path `/bin` return a `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Also check that the Dispatcher cache root and the IIS document root are set to the same directory. -->

## ワークフローモデルの削除に関する問題 {#problems-deleting-workflow-models}

**症状**

Dispatcher 経由で AEM のオーサーインスタンスへのアクセス時に、ワークフローモデルの削除を試みた際に発生する問題。

**再現手順：**

1. オーサーインスタンスにログインします（リクエストが Dispatcher を経由することを確認します）。
1. 新しいワークフローを作成します。タイトルは、例えば workflowToDelete と設定します。
1. ワークフローが正しく作成されたことを確認します。
1. ワークフローを選択して右クリックし、「**削除**」をクリックします。

1. 「**はい**」をクリックして確定します。
1. 次のメッセージを示すエラーメッセージボックスが表示されます。\
   `ERROR 'Could not delete workflow model!!`。

**解決方法**

`dispatcher.any` ファイルの `/clientheaders` セクションに、次のヘッダーを追加します。

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## mod_dir（Apache）による干渉 {#interference-with-mod-dir-apache}

このプロセスでは、Dispatcher と Apache web サーバー内の `mod_dir` がどのようにやり取りするかを説明します。このやり取りは、予期しない様々な影響を与える可能性があります。

### Apache 1.3 {#apache}

Apache 1.3 では、`mod_dir` は URL がファイルシステムのディレクトリにマッピングされたすべてのリクエストを処理します。

次のいずれかを実行します。

* 要求を既存の `index.html` ファイルへリダイレクト
* ディレクトリのリストを生成

Dispatcher が有効な場合、Dispatcher をコンテンツタイプ `httpd/unix-directory` のハンドラーとして登録することで、このようなリクエストを処理します。

### Apache 2.x {#apache-x}

Apache 2.x では、内容が異なります。モジュールで、URL の修正など、リクエストの様々なステージを処理できます。`mod_dir` でこのステージを処理するには、リクエスト（URL がディレクトリにマッピングされている場合）を `/` 付きの URL にリダイレクトします。

Dispatcher は `mod_dir` による修正の処理に影響しませんが、リダイレクトされた（`/` 付きの）URL へのリクエストを完全に処理します。この処理は、リモートサーバー（AEM など）で `/a_path` へのリクエストの処理が `/a_path/` へのリクエストの処理と異なる場合（`/a_path` が既存のディレクトリにマッピングされる場合）に、問題となることがあります。

状況が発生した場合は、次のいずれかを実行します。

* Dispatcher によって処理される `Directory` サブツリーまたは `Location` サブツリーでの `mod_dir` を無効にします。

* `DirectorySlash Off` を使用して、「`/`」が付加されないように `mod_dir` を設定します。
