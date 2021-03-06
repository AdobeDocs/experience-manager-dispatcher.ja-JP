---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
translation-type: tm+mt

---
# Adobe Experience Managerドキュメントへの貢献に関するガイドライン

## ドキュメントの理念

Adobe Experience Manager ユーザーは、競争の厳しい環境でビジネスを展開しており、競合他社より優位に立つためのデジタルエクスペリエンスを構築しようと懸命に努力しています。したがって、アドビが AEM で高度な新しいツールを提供する際には、お客様が AEM への投資をすぐに活かし ROI を最大化できるような正確かつ明快なドキュメントで、これらのツールが補完されることが不可欠です。

AEM ドキュメントの目標は、ドキュメントをできるだけ早く AEM ユーザーに届けることです。そのため、正確で使いやすいドキュメントを最優先し、ドキュメントの継続的な更新と改善に努めます。

## ドキュメント貢献度

AEMドキュメントを継続的に改善するために、AEMユーザーのコミュニティ全体がドキュメントに貢献することを歓迎します。 変更修正依頼（プル要求）であるか問題報告（イシュー）であるかを問わず、修正、明確化、増補、例の追加などがドキュメントの改善になります。

## ドキュメント標準

アドビのドキュメントへの貢献を歓迎しますが、AEM ドキュメントへの貢献は、変更修正依頼または問題報告の形式でおこない、貢献とドキュメントに関するアドビの標準に準拠する必要があります。

これらの標準に合致しない貢献は拒否される可能性があります。

### 標準的な使用例について記載します。

AEM ドキュメントでは標準的な使用例を扱っています。製品の標準的なインストールや使用の範囲を超える使用例は、AEM ドキュメントに含まれていません。

### 一般に、バグやそれらの回避策については記載しません。

AEM ドキュメントでは標準的な使用例を扱っています。そのため、バグ、バグによる影響、バグの回避策は、一般にドキュメントには記載されていません。

ただし、既知の問題と AEM 製品管理部門で承認された実行可能な解決策を記載できるリリースノートは、このルールの例外です。

### ドキュメントへの貢献は、技術的な質問に回答するためのものではありません。

AEM ドキュメントの改善に役立つアイデアは、貢献として歓迎します。ただし、コメント、問題報告および変更修正依頼は、*貢献*&#x200B;のみを目的としています。これらを、AEM の使用方法、AEM プロジェクトの実装方法、技術的な問題の解決方法についての質問に回答するために使用することはできません。

Any questions about the usage of AEM or technical errors you may have should be reported through the normal support process via the [Experience Cloud Enterprise Support portal](https://helpx.adobe.com/contact/enterprise-support.ec.html) or discussed in the [Experience Manager community](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager).

***AEMドキュメントの寄稿はアドビカスタマーケアの代わりではありません*** 。サポートに関する質問に対する回答を求める寄稿は拒否されます。

### 貢献では、影響を受けるドキュメントページを明確に参照する必要があります。

ドキュメントの改善を提案する問題を作成する場合は、影響を受けるページへのリンクを含める必要があります。 ドキュメントページで「**Edit this page**」リンクを使用して問題報告を作成すると、その問題報告は自動的に該当ページへのリンク付きで作成されます。

なお、これはプル要求には当てはまりません。プル要求ではもともと、影響を受けるページを参照するからです。

## ドキュメントガイドライン

アドビのドキュメントに貢献いただく場合は、特定のスタイルガイドラインに従ってください。

これらのガイドラインに従っていただくと、貢献のレビューが容易になり、貢献がアドビのドキュメントにすばやく統合されるようになります。

### 言葉遣いとスタイル

#### 言葉遣い

* AEM ドキュメントは米国英語で作成および維持管理されています。
* 文章をできるだけ簡単にしてください。
* 言葉遣いを明快かつ簡潔にしてください。

AEM ドキュメントの読者は世界中におり、英語を母国語としていたり流暢に使えるとは限りません。口語的な表現を避け、言葉遣いをできるだけ明快で簡潔なものにしてください。

#### マイクロソフトのスタイルガイドへの準拠

マイクロソフトのスタイルガイド『[The Microsoft Manual of Style](https://docs.microsoft.com/en-us/style-guide/welcome/)』（英語版）は、ソフトウェアドキュメントに重点を置いたドキュメントスタイルガイドで、無料で入手できます。AEM ドキュメントは、可能な限り、このガイドに従っています。

### 書式設定

| 項目 | スタイル |
|---|---|
| UI 要素またはオプション | **太字** |
| ファイル名、パス、ユーザー入力、パラメーター値 | `monospaced` |
| コード、コマンドライン | ```Code Block``` |

### スクリーンショット

スクリーンショットは慎重に、かつ、テキストによる説明が不十分な場合にのみ使用します。

マーカーやその他の注釈（赤枠、矢印、テキストなど）はスクリーンショットに対して使用しないでください。こうすれば、スクリーンショットをドキュメントのローカライズ版で再利用または複製しやすくなります。

### バージョン固有の参照

ドキュメントコンテンツ全体で特定バージョンへの直接参照をできるだけ避けてください。これにより、今後のバージョンについて、ドキュメントの柔軟性と拡張性が高まります。

### 呼称としての Day、AEM、CQ、CRX の使用

ドキュメントでの初出時には必ず、本製品をフルネームである **Adobe Experience Manager** と記載します。その後は **AEM** と記載してもかまいません。

Day、Day Software、CQ、CRX は、クラス名や AEM の歴史に言及する場合など、やむを得ない場合を除き、使用しないでください。