---
title: AEM Dispatcher リリースノート
description: Adobe Experience Manager Dispatcher に固有のリリースノート。
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '1048'
ht-degree: 54%

---

# AEM Dispatcher リリースノート{#aem-dispatcher-release-notes}

## リリース情報 {#release-information}

|  |  |
|--- |--- |
| 商品 | Adobe Experience Manager (AEM) Dispatcher |
| バージョン | 4.3.7 |
| タイプ | マイナーリリース |
| 日付 | 2024年3月27日（PT） |
| ダウンロード URL | <ul><li>[Apache 2.4](#apache)</li><li>[Microsoft® インターネットインフォメーションサービス（IIS）](#iis)</li></ul> |
| 互換性 | AEM 6.1 以降 |

## システム要件および使用条件 {#system-requirements-and-prerequisites}

参照： [サポートされるプラットフォーム](https://experienceleague.adobe.com/en/docs/experience-manager-64/deploying/introduction/technical-requirements) 要件と前提条件について詳しくは、を参照してください。

Adobeでは、最新機能、最新のバグ修正、可能な限り最高のパフォーマンスを活用するために、最新バージョンのAEM Dispatcher を使用することをお勧めします。

## インストール手順 {#installation-instructions}

詳しい手順については、[Dispatcher のインストール](dispatcher-install.md)を参照してください。

## リリース履歴 {#release-history}

### リリース 4.3.7（2024年3月27日（PT）） {#march}

**改善点**：

* DISP-1009 - ヘッダー長を再度設定します。
* DISP-1013 - Linux® 用に openssl 3.0 のサポートを追加。
* DISP-1014 - response.location の処理が原因で、無効なリダイレクトが発生する。
* DISP-1017 - DTD 定義の変更。

### リリース 4.3.6（2023年7月25日）（PT） {#jyly}

**改善点**：

* DISP-911 AEM-05 - X-Edge-Key can be leaked in disp_apache2.c.
* DISP-937 すべてのセレクターをログに記録しています。
* DISP-998 起動時のバニティ URL の読み込みを設定可能。

### リリース 4.3.5（2022年4月4日）（PT）） {#apr}

**改善点**：

* DISP-954 - 有効期限が過ぎていない場合でも無効化をサポート
* DISP-949 - POSTリクエストがフィルターでブロックされた場合でも、Dispatcher は 404 ではなく 200 を返します。

### リリース 4.3.4（2021年11月29日）（PT） {#nov}

**バグ修正**：

* DISP-833 - X-Forwarded-Host ヘッダーには、コンマ区切りのホスト名のリストを含めることができます。
* DISP-835 - DispatcherUseForwardedHost は、ホストヘッダーが最後の場合にスワローします。

**改善点**：

* DISP-874 - フラグを通じて DISP-818 の実装をオンまたはオフにする Dispatcher 設定を作成します `DispatcherRestrictUncacheableContent`. デフォルト値はオフです。オンにすると、キャッシュできないコンテンツに対して mod 期限切れによって設定されたキャッシュヘッダーが削除されます。これは、バージョン 4.3.3（デフォルトはオン）の動作とは異なりますが、4.3.3 より前のバージョン（デフォルトはオフ）と同じです。ブラウザーのキャッシュの柔軟性を高めるため、`DispatcherRestrictUncacheableContent` のデフォルトをオフのままにすることをお勧めします。バージョン 4.3.3 から 4.3.4 にアップグレードする場合に、バージョン 4.3.3 と同じ動作を維持するには、を明示的に設定する必要があります `DispatcherRestrictUncacheableContent` をオンにします。
* DISP-841 - Dispatcher が 504 応答コードの /serverStaleOnError を考慮しない
* DISP-874 - DISP-818 の実装をオンまたはオフにする Dispatcher 設定を作成します
* DISP-883 - Dispatcher での URL 要求の分解を示すトレース
* DISP-944 - バニティ URL のプリロード

### リリース 4.3.3（2019年10月18日）（PT） {#october}

**バグ修正**：

* DISP-739 - ログレベル Dispatcher: **レベル** は動作しません。
* DISP-749 - Alpine Linux® Dispatcher がトレースログレベルでクラッシュする。

**改善点**：

* DISP-813 - openssl 1.1.x の Dispatcher でのサポート
* DISP-814 - キャッシュフラッシュ中の Apache 40x エラー
* DISP-818 - mod_expires がキャッシュできないコンテンツ用の Cache-Control ヘッダーを追加する
* DISP-821 - ソケットにログコンテキストを保存しない
* DISP-822 - Dispatcher はを使用する必要があります `ppoll` の代わりに `pselect`
* DISP-824 - セキュアな DispatcherUseForwardedHost
* DISP-825 - ディスクに空き容量がなくなった場合の特殊メッセージをログに記録する
* DISP-826 - クエリ文字列での URI の再フェッチをサポートします

**新機能**：

* DISP-703 - ファーム固有のキャッシュヒット率
* DISP-827 - テスト用のローカルサーバー
* DISP-828 - Dispatcher 用のテスト Docker イメージの作成

### リリース 4.3.2（2019年1月31日）（PT） {#jan}

**バグ修正**:

* DISP-734 - ハンドラーとして設定されていない場合、Dispatcher が insert_output_filter でクラッシュする
* DISP-735 - Alpine Linux では RE が機能しない®
* DISP-740 - macOS Mojave での Dispatcher の読み込みは、デフォルトで無効になっています
* DISP-742 - ブロックされたリクエストが、認証チェッカーで保護されたリソースに情報をリークする可能性があります

**改善点**：

* DISP-746 - dispatcher.any のラベルのない文字列で警告が生成される

**新機能**:

* DISP-747 - Apache 環境で要求の情報を提供

### リリース 4.3.1（2018年10月16日）（PT） {#oct}

**バグ修正**:

* DISP-656 - Dispatcher により誤った ETag ヘッダーが提供される
* DISP-694 - キープアライブ接続が古くなった場合に警告が表示されない
* DISP-714 - Cookie ベースのセッション管理が IIS で機能しない
* DISP-715 - RenderID cookie の Secure フラグ
* DISP-720 - 一時ファイルが閉じられていないため、システムに極端な負荷がかかる可能性がある（開いているファイルが多すぎる）
* DISP-721 - Apache が正常に子プロセスを再起動したとき、Dispatcher が poll() を中断する
* DESP-722 - キャッシュファイルが 8 進数モード 0600 で作成される
* DISP-723 - レンダータイムアウトが 0 に設定されている場合の暗黙的な 10 分のタイムアウト（および再試行）
* DISP-725 - 文字列後の末尾の文字が名称未設定の値に変換される
* DISP-726 - ファームが受信ホストと一致していない場合に警告が記録される
* DESP-727 - Dispatcher が空のキャッシュファイルのコンテンツの長さのリクエストをチェックする
* Dispatcher からヘッダーファイルにアクセスしようとすると、DISP-730 - 404 が発生する
* DISSP-731 - Dispatcher がログインジェクションに対して脆弱性を持つ
* DISP-732 - Dispatcher は URL 内の連続した「/」を削除する
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
* DISP-685 - 32 ビット Solaris™ SPARC® バイナリには、__divdi3 への未定義の参照があります
* DISP-688 - Dispatcher が 404 応答で「X-Cache-Info」ヘッダーを返さない
* DISP-690 - Last- Modified ヘッダーがキャッシュできない
* DISP-691 - w3wp. exe でのアクセス違反
* DISP-693 - Dispatcher ダウンロードページの Solaris™ サーバーのアーキテクチャの詳細の更新
* DISP-695 - Dispatcher モジュール 4.2.3 の DispatcherLog レベルに関する問題
* DISP-698 - Dispatcher TTL は s-maxage および private ディレクティブをサポートする必要がある
* DISP-700 - Alpine Linux でモジュールが正しく動作しない®
* DISP-704 - %2b を含むブラウザーリクエストが、エンコードされていない状態でパブリッシャーに送信される
* DISP-705 – 二重の空き容量または破損が原因で Dispatcher がクラッシュする（`fasttop`）
* DISP-706 – 無効化中、Dispatcher は、無限ループを引き起こす可能性のある後方参照シンボリックリンクをフォローしています
* DISP-709 - 一部のバニティー URL 拡張のブロック
* DISP-710 - Cent OS 6 で Linux® 用のビルドを使用できない

**機能強化**：

* DISP-652 - Dispatcher により誤った Data ヘッダーが提供される

## 役立つリソース {#helpful-resources}

* [AEM Dispatcher の概要](dispatcher.md)

## ダウンロード {#downloads}

### Apache 2.4 {#apache}

| プラットフォーム | アーキテクチャ | OpenSSL サポート | クリックしてダウンロード |
|---|---|---|---|
| Linux® | i686 （32 ビット） | なし | [`dispatcher-apache2.4-linux-i686-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.7.tar.gz) |
| Linux® | i686 （32 ビット） | 1.0 | [`dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.7.tar.gz) |
| Linux® | i686 （32 ビット） | 1.1 | [`dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.7.tar.gz) |
| Linux® | i686 （32 ビット） | 3.0 | [`dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl3.0-4.3.7.tar.gz) |
| Linux® | x86_64 （64 ビット版） | なし | [`dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.7.tar.gz) |
| Linux® | x86_64 （64 ビット版） | 1.0 | [`dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.7.tar.gz) |
| Linux® | x86_64 （64 ビット版） | 1.1 | [`dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.7.tar.gz) |
| Linux® | x86_64 （64 ビット版） | 3.0 | [`dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl3.0-4.3.7.tar.gz) |
| Linux® | aarch64 （64 ビット版） | なし | [`dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.7.tar.gz) |
| Linux® | aarch64 （64 ビット版） | 1.0 | [`dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.7.tar.gz) |
| Linux® | aarch64 （64 ビット版） | 1.1 | [`dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.7.tar.gz) |
| Linux® | aarch64 （64 ビット版） | 3.0 | [`dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl3.0-4.3.7.tar.gz) |
| macOS | arm64 （64 ビット版） | なし | [`dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.7.tar.gz) |
| macOS | x86_64 （64 ビット版） | なし | [`dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz`](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.7.tar.gz) |

### IIS {#iis}

| プラットフォーム | アーキテクチャ | OpenSSL サポート | クリックしてダウンロード |
|---|---|---|---|
| Windows | x86 （32 ビット） | なし | [`dispatcher-iis-windows-x86-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.7.zip) |
| Windows | x86 （32 ビット） | 1.0 | [`dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.7.zip) |
| Windows | x86 （32 ビット） | 1.1 | [`dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.7.zip) |
| Windows | x64 （64 ビット版） | なし | [`dispatcher-iis-windows-x64-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.7.zip) |
| Windows | x64 （64 ビット版） | 1.0 | [`dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.7.zip) |
| Windows | x64 （64 ビット版） | 1.1 | [`dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.7.zip) |
| Windows | x64 （64 ビット版） | 3.0 | [`dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip`](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl3.0-4.3.7.zip) |