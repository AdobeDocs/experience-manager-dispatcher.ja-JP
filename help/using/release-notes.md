---
title: AEM Dispatcher リリースノート
seo-title: AEM Dispatcher Release Notes
description: Adobe Experience Manager Dispatcher 固有のリリースノート
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 230f656999d95b3d5f95e97bfb7383b95bcf0a75
workflow-type: ht
source-wordcount: '1101'
ht-degree: 100%

---

# AEM Dispatcher リリースノート{#aem-dispatcher-release-notes}

## リリース情報 {#release-information}

|  |  |
|--- |--- |
| 商品 | Adobe Experience Manager (AEM) Dispatcher |
| バージョン | 4.3.7 |
| タイプ | マイナーリリース |
| 日付 | 2024年3月27日（PT） |
| ダウンロード URL | <ul><li>[Apache 2.4](#apache)</li><li>[Microsoft Internet Information Services（IIS）](#iis)</li></ul> |
| 互換性 | AEM 6.1 以降 |

## システム要件および使用条件 {#system-requirements-and-prerequisites}

要件と前提条件について詳しくは、[サポートされたプラットフォーム](https://helpx.adobe.com/jp/experience-manager/6-4/sites/deploying/using/technical-requirements.html)ページを参照してください。

アドビでは、最新バージョンの AEM Dispatcher を使用して、最新の機能、最新のバグ修正、可能な限り最高のパフォーマンスを活用することを強くお勧めします。

## インストール手順 {#installation-instructions}

詳しい手順については、[Dispatcher のインストール](dispatcher-install.md)を参照してください。

## リリース履歴 {#release-history}

### リリース 4.3.7（2024年3月27日（PT）） {#march}

**改善点**：

* DISP-1009 - ヘッダー長を再度設定
* DISP-1013 - Linux 用の openssl 3.0 サポートを追加
* DISP-1014 - response.location 処理により無効なリダイレクトが発生
* DISP-1017 - DTD 定義を変更

### リリース 4.3.6（2023年7月25日）（PT） {#jyly}

**改善点**：

* DISP-911 AEM‑05 ‑ X‑Edge‑Key が disp_apache2.c で漏洩する可能性あり
* DISP-937 すべてのセレクターをログに記録
* DISP-998 起動時のバニティ URL の読み込みを設定可能に

### リリース 4.3.5（2022年4月4日）（PT）） {#apr}

**改善点**：

* DISP-954 - 有効期限が過ぎていない場合でも無効化をサポート
* DISP-949 - フィルターによって POST 要求がブロックされている場合でも、Dispatcher が 404 ではなく 200 を返す

### リリース 4.3.4（2021年11月29日）（PT） {#nov}

**バグ修正**：

* DISP-833 - X-Forwarded-Host ヘッダーには、コンマで区切られたホスト名のリストが含まれる場合がある
* DISP-835 - DispatcherUseForwardedHost が、最後にある場合は Host ヘッダーをスワローする

**改善点**：

* DISP-874 - `DispatcherRestrictUncacheableContent` フラグを使用して、DISP-818 の実装をオンまたはオフにする Dispatcher 設定を作成する。デフォルト値はオフです。オンにすると、キャッシュできないコンテンツに対して mod 期限切れによって設定されたキャッシュヘッダーが削除されます。これは、バージョン 4.3.3（デフォルトはオン）の動作とは異なりますが、4.3.3 より前のバージョン（デフォルトはオフ）と同じです。ブラウザーのキャッシュの柔軟性を高めるため、`DispatcherRestrictUncacheableContent` のデフォルトをオフのままにすることをお勧めします。バージョン 4.3.3 から 4.3.4 にアップグレードする際に、バージョン 4.3.3 と同じ動作を保持する場合は、`DispatcherRestrictUncacheableContent` を明示的にオンに設定する必要があります。
* DISP-841 - Dispatcher が 504 応答コードの /serverStaleOnError を考慮しない
* DISP-874 - DISP-818 の実装をオンまたはオフにする Dispatcher 設定を作成する
* DISP-883 - Dispatcher での URL 要求の分解を示すトレース
* DISP-944 - バニティ URL をプリロードする

### リリース 4.3.3（2019年10月18日）（PT） {#october}

**バグ修正**：

* DISP-739 - LogLevel Dispatcher：**レベル**&#x200B;が機能しない
* DISP-749 - Alpine Linux Dispatcher がトレースログレベルでクラッシュする

**改善点**：

* DISP-813 - openssl 1.1.x の Dispatcher でのサポート
* DISP-814 - キャッシュフラッシュ中の Apache 40x エラー
* DISP-818 - mod_expires がキャッシュできないコンテンツ用の Cache-Control ヘッダーを追加する
* DISP-821 - ソケットにログコンテキストを保存しない
* DISP-822 - Dispatcher は pselect の代わりに ppoll を使用する必要がある
* DISP-824 - セキュアな DispatcherUseForwardedHost
* DISP-825 - ディスクに空き容量がなくなった場合の特殊メッセージをログに記録する
* DISP-826 - クエリ文字列を使用した URI の再取得をサポートする

**新機能**：

* DISP-703 - ファーム固有のキャッシュヒット率
* DISP-827 - テスト用のローカルサーバー
* DISP-828 - Dispatcher のテスト用 Docker イメージを作成する

### リリース 4.3.2（2019年1月31日）（PT） {#jan}

**バグ修正**:

* DISP-734 - ハンドラーとして設定されていない場合、Dispatcher が insert_output_filter でクラッシュする
* DISP-735 - Alpine Linux で RE が機能しない
* DISP-740 - MacOS Mojave での Dispatcher の読み込みがデフォルトで無効になっている
* DISP-742 - ブロックされたリクエストによって Auth Checker で保護されたリソースに情報が漏洩する場合がある

**機能強化**：

* DISP-746 - dispatcher にラベルのない文字列が存在する場合は警告が表示される

**新機能**:

* DISP-747 - Apache 環境で要求の情報を提供

### リリース 4.3.1（2018年10月16日）（PT） {#oct}

**バグ修正**:

* DISP-656 - Dispatcher により誤った ETag ヘッダーが提供される
* DISP-694 - キープアライブ接続が古くなった場合に警告が表示されない
* DISP-714 - Cookie ベースのセッション管理は IIS では機能しない
* DISP-715 - RenderID cookie の Secure フラグ
* DISP-720 - 一時ファイルが閉じられていないため、システムに極端な負荷がかかる可能性がある（開いているファイルが多すぎる）
* DISP-721 - Apache が正常に子プロセスを再起動したとき、Dispatcher が poll() を中断する
* DESP-722 - キャッシュファイルが 8 進数モード 0600 で作成される
* DISP-723 - レンダリングタイムアウトが 0 に設定されている場合の暗黙的な 10 分のタイムアウト（および再試行）
* DISP-725 - 文字列後の末尾の文字が名称未設定の値に変換される
* DISP-726 - ファームが受信ホストと一致していない場合に警告が記録される
* DESP-727 - Dispatcher が空のキャッシュファイルのコンテンツの長さのリクエストをチェックする
* DISP-730 - Dispatcher を介してヘッダーファイルにアクセスしようとしたときの 404
* DISSP-731 - Dispatcher がログインジェクションに対して脆弱性を持つ
* DISSP-732 - Dispatcher は、URL内の連続した「/」を削除する必要がある
* DISP-733 - Dispatcher は、Age ヘッダーを設定（計算）する必要がある

**機能強化**：

* DISP-656 - Dispatcher により誤った ETag ヘッダーが提供される
* DISP-694 - キープアライブ接続が古くなった場合に警告が表示されない
* DISP-715 - RenderID cookie の Secure フラグ
* DESP-722 - キャッシュファイルが 8 進数モード 0600 で作成される
* DISP-726 - ファームが受信ホストと一致していない場合に警告が記録される

### リリース 4.3.0（2018年6月13日（PT）） {#jun}

**バグ修正**:

* DISP-682 - 数値ログレベルが誤って適用される
* DISSP-685 - 32 ビット Solaris SPARC バイナリでは __ divdi3 への未定義の参照がある
* DISSP-688 - Dispatcher が404応答で「X- Cache- Info」ヘッダーを返さない
* DISP-690 - Last- Modified ヘッダーがキャッシュできない
* DISP-691 - w3wp. exe でのアクセス違反
* DISP-693 - Dispatcher ダウンロードページ上の Solaris サーバーのアーキテクチャ詳細を更新する必要がある
* DISP-695 - Dispatcher モジュール 4.2.3 の DispatcherLog レベルに関する問題
* DISP-698 - Dispatcher TTLは、s- maxageおよび private ディレクティブをサポートする必要がある
* DISP-700 - Alpine Linux ではモジュールが正しく機能しない
* DISP-704 - %2b を含むブラウザーリクエストが、エンコードされていない状態でパブリッシャーに送信される
* DISP-705 - 二重の空きまたは破損（fasttop）により Dispatcher がクラッシュする。
* DISP-706 - 無効化中、Dispatcher が逆参照シンボリックリンクをたどるため無限ループが発生する可能性がある
* DISP-709 - 一部のバニティー URL 拡張のブロック
* Disp-710- Cent OS 6 では使用できない Linux 用ビルド

**機能強化**：

* DISP-652 - Dispatcher により誤った Data ヘッダーが提供される

## 役立つリソース {#helpful-resources}

* [AEM Dispatcher の概要](dispatcher.md)

## ダウンロード {#downloads}

### Apache 2.4 {#apache}

| プラットフォーム | アーキテクチャ | OpenSSL サポート | ダウンロード |
|---|---|---|---|
| Linux | i686（32 ビット） | なし | [dispatcher-apache2.4-linux-i686-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.7.tar.gz) |
| Linux | i686（32 ビット） | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz) |
| Linux | i686（32 ビット） | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz) |
| Linux | i686（32 ビット） | 3.0 | [dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz) |
| Linux | x86_64（64 ビット） | なし | [dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz) |
| Linux | x86_64（64 ビット） | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz) |
| Linux | x86_64（64 ビット） | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz) |
| Linux | x86_64（64 ビット） | 3.0 | [dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz) |
| Linux | aarch64（64 ビット） | なし | [dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz) |
| Linux | aarch64（64 ビット） | 1.0 | [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz) |
| Linux | aarch64（64 ビット） | 1.1 | [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz) |
| Linux | aarch64（64 ビット） | 3.0 | [dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz) |
| macOS | arm64（64 ビット） | なし | [dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz) |
| macOS | x86_64（64 ビット） | なし | [dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz) |

### IIS {#iis}

| プラットフォーム | アーキテクチャ | OpenSSL サポート | ダウンロード |
|---|---|---|---|
| Windows | x86（32ビット） | なし | [dispatcher-iis-windows-x86-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.7.zip) |
| Windows | x86（32ビット） | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip) |
| Windows | x86（32ビット） | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip) |
| Windows | x64（64 ビット） | なし | [dispatcher-iis-windows-x64-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.7.zip) |
| Windows | x64（64 ビット） | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip) |
| Windows | x64（64 ビット） | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip) |
| Windows | x64（64 ビット） | 3.0 | [dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip) |