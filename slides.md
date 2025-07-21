---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: PHPでやってみよう！テストだけじゃない、デシジョンテーブル（決定表）実装の勘所
info: |
  PHPカンファレンス関西2025
  https://2025.kphpug.jp/
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
addons:
  - '@katzumi/slidev-addon-qrcode'
  - "@katzumi/slidev-addon-ogp-image"
  - slidev-addon-components
  - slidev-addon-rabbit
---

# PHPでやってみよう！テストだけじゃない、デシジョンテーブル（決定表）実装の勘所
PHP カンファレンス関西 2025 Jul 19, 2025.  
v0.0.6  
@katzumi(かつみ)

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/k2tzumi/decision-table-implementation-tips" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
PHPでやってみよう！テストだけじゃない、デシジョンテーブル実装の勘所というタイトルで発表します
-->

---
transition: fade-out
layout: two-cols-header
---

# 自己紹介

katzumi（かつみ）と申します。

「障害のない社会をつくる」をビジョンに掲げている「LITALICO」という会社に所属しています
<a href="https://litalico.co.jp/">
<img src="https://litalico.co.jp/ogp.png" class="w-40" />
</a>

以下のアカウントで活動しています。

::left::

<div class="float-left">
<img src="https://pbs.twimg.com/profile_images/1768978237210935296/idy9J4l6_400x400.jpg" class="rounded-full w-40 mr"/>  
<simple-icons-x /> <a href="https://twitter.com/katzchum">katzchum</a></div>  
<QRCode :width="180" :height="180" value="https://twitter.com/katzchum" color="4329B9" image="Logo_of_X.svg" />

::right::

<img src="https://avatars.githubusercontent.com/u/1182787?v=4" class="rounded-full w-40 mr-12"/>

<logos-github-octocat /> [k2tzumi](https://github.com/k2tzumi)  
<simple-icons-zenn /> [katzumi](https://zenn.dev/katzumi)  

<br />

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

<!--
はじめましてkatzumiと申します  
LITALICOで働いており、ご覧のXとGitHubで活動しています
-->

---
layout: two-cols-header
transition: fade-out
---

# お願い 🙏

写真撮影、SNS での実況について

登壇者の励みになるので是非ともご意見やご感想など、フィードバック頂けると助かります mm  
📝 このスライドにはコードがたくさん登場します。  
スライドの内容は、すでに以下の場所で公開されていますので、ぜひお手元でご覧ください  

* [forteeのプロポーザルページ](https://fortee.jp/phpcon-kansai2025/proposal/44d7c3a3-1137-482a-9f44-33c0371dbfb9)
* または <fa6-brands-square-x-twitter /> の投稿

::left::

<Transform :scale="2.5">
　　　🙆‍♀📷<ph-projector-screen-chart-light /><br />
　　　🙅‍♂📹💸<br />
　　　🙅📸👨‍👦‍👦<br />
</Transform>

::right::

<br />
<Transform :scale="2">
<fa6-brands-square-x-twitter />
</Transform>
<br />
<a href="https://x.com/search?q=%23phpkansai%20%23b&f=live">#phpkansai #b</a>

<!--
まず最初にお願いがあります。  
登壇者の励みになりますので、写真撮影やSNSでの実況、そしてご意見・ご感想の投稿をぜひお願いします！

このスライドではコードがたくさん出てきます。すでにforteeやXに投稿してあります。  
ちょっと見づらいかもなので、こちらで手元に表示しながら見てください！
-->

---
layout: default
transition: slide-up
---

# 本セッションで得られること
if 文地獄から抜け出し、堅牢で保守しやすい実装へ

<Transform :scale="1.4">

* **🧠 デシジョンテーブルを設計・実装に活かすための理解**  
テスト設計だけではない実装パターンとしての価値

* **🧩 複雑な条件分岐を整然と構造化する方法**  
入れ子になった if, switch 文からの脱却

* **💡 現場ですぐ試せる実装アイデア**  
  🧾 Enum と型を活用した堅牢な実装方法  
  🏗️ 保守性と品質を両立するアーキテクチャ

* **✅ 全パターンテスト可能な設計アプローチ**  
条件判定処理の妥当性を全網羅テストする方法

</Transform>

<!--
今日は「デシジョンテーブル」について、テスト設計の枠を超えた実装パターンについてお話します  
このスライドに書かれていることを理解・習得することを目標にしています。
-->

---

# デシジョンテーブルとは何か
基本概念と構造

<Transform :scale="1.6">

* **📘 デシジョンテーブル（決定表）の定義**  
  🧮 複数の条件と結果の組み合わせを表形式で整理したもの  
  🧱 条件部（Condition）と動作部（Action）で構成される

* **📊 一般的な表現形式**  
  🧷 条件の組み合わせを列として表現  
  🔘 各条件の状態を「Yes/No」「True/False」などで表す

* **🧪 テスト設計での活用例**  
  🚫 テストケースの漏れを防ぐ  
  👁️ 条件の組み合わせを網羅的に可視化する

</Transform>

<!--
まずデシジョンテーブルとは何かをお話します

デシジョンテーブル、日本語では「決定表」と呼ばれます  
ざっくり説明すると「いくつかの条件の組み合わせに対して、どんな処理や結果になるかを整理した表」です  
つまり、「こういうときは、こうする」を一覧にしたものです。

一般的な表現形式として、条件の組み合わせを列として表現します。

各条件の状態を「Yes/No」や「True/False」などで表します。

テスト設計の文脈で使われることが多く、主にテストケースを漏れなく纏めるために使われます
-->

---

# デシジョンテーブル例（住宅ローン審査基準の場合）
条件に雇用形態、勤続年数、年収を持つ場合


<Transform :scale="0.8">

|      |       | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|------|-------|---|---|---|---|---|---|---|---|---|
| 条件 | 正社員 | Y | Y | Y | Y | N | N | N | N | N |
|      | 契約社員 | N | N | N | N | Y | Y | Y | Y | N |
|      | 無職  | N | N | N | N | N | N | N | N | Y |
|      | 勤続3年以上 | Y | Y | N | N | Y | Y | N | N | - |
|      | 年収500万以上 | Y | N | Y | N | Y | N | Y | N | - |
| 動作 | 承認  | X | - | - | - | - | - | - | - | - |
|      | 条件付承認 | - | X | X | - | X | - | - | - | - |
|      | 詳細審査 | - | - | - | X | - | X | X | - | - |
|      | 不承認 | - | - | - | - | - | - | - | X | X |

<Arrow v-click="[1]" x1="90" y1="10" x2="120" y2="60" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[2]" x1="950" y1="0" x2="850" y2="20" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[3]" x1="370" y1="450" x2="370" y2="360" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[4]" x1="950" y1="150" x2="840" y2="170" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[4]" x1="950" y1="150" x2="780" y2="145" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[4]" x1="590" y1="500" x2="600" y2="400" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[4]" x1="590" y1="500" x2="690" y2="450" color="#953" width="2" arrowSize="1" />

<Arrow v-click="[4]" x1="590" y1="500" x2="750" y2="490" color="#953" width="2" arrowSize="1" />

</Transform>

<!--
実際の例を見てみましょう。

住宅ローンの審査基準を想定したケースを例にします  

[click] ここでは「雇用形態」「勤続年数」「年収」の3つの条件があるとします  

[click] こちらはサンプルのため簡略化されていますが、9つのケースパターンが存在することを示しています。  

[click] 正社員、勤続3年以上、年収500万以上なら承認。

[click] 契約社員や無職の場合は条件付承認、詳細審査、不承認といった組み合わせが存在することを表現しています。

[click] このように、複雑な判定ロジックを表形式で整理することで、すべての条件パターンが一目で理解できます。
-->

---
layout: two-cols-header
transition: fade-out
---

# テスト設計手法としてのデシジョンテーブル
一般的な利用法と導入活用イメージ

::left::

<Transform :scale="1.2">

* **⚙️ テストケース設計の効率化**
  - 条件の組み合わせを網羅的に可視化
  - 漏れや重複のないテストケース作成が可能

* **🔍 テスト観点の明確化**
  - 条件と結果の関係性を明示的に表現
  - チーム内での認識共有が容易

* **🧪 境界値・同値分割との組み合わせ**
  - 各条件の境界値を考慮したテストケース設計
  - 効率よく、重要なパターンをカバーする


</Transform>

::right::

<v-click>

<Transform :scale="1.2">

* **🧠 複雑なビジネスルールのテスト**
  - 複数条件が絡み合う業務ロジックの検証
  - 例外ケースの漏れを防止

* **🔁 回帰テストへの活用**
  - 仕様変更時の影響範囲の特定
  - 変更部分に関連するテストケースの選定

</Transform>

</v-click>

<!--
デシジョンテーブルは、テスト設計で条件の組み合わせを整理するためによく使われてきた手法です。  

条件の組み合わせを整理することで、テストケースを漏れなく効率的に設計できます。

条件と結果の関係を整整理することで、テスト観点が明確になり、チームでの認識も共有しやすくなります。

同値分割や境界値分析と組み合わせることで、重要なパターンを効率よく押さえ、テストの抜け漏れを防げます。

[click] 複雑なビジネスルールのテストでは、条件が複雑に絡むロジックや例外ケースの見落としを防げます。  

回帰テストでは、仕様変更の影響範囲を把握したり、関連するテストケースを選ぶのにも使えます。
-->

---
layout: image-right
image: /theme.jpeg
transition: fade-out
---

# 今回のテーマ

<!--
今回のテーマです
-->

---
layout: statement
transition: slide-up
---

# 🙅 デシジョンテーブル
# （決定表）を書く
テスト設計とか、仕様をまとめるのに利用するのではなく。。

<!--
今回は、テスト設計とか、仕様をまとめるのに利用するのではなく、
-->

---
layout: statement
---

# 🙆 デシジョンテーブル
# （決定表）を実装する

<!--
デシジョンテーブルを実際にコードとして実装することに焦点を当てます。
-->

---
layout: statement
transition: slide-up
---

# 改めて自己紹介

<!--
本日４分ぶり２回目の自己紹介です
-->

---

# 日々複雑なドメインと向き合っている者です
圧巻の 1.5K 頁オーバー。3 年に 1 回、大改訂（大改定）があります。レセプト業務の基盤システムを開発しています！

<img src="/ISBN-978-4805887332.png" />

<!--
私は日々、複雑なドメインと向き合っている者です    
障害福祉領域のレセプト業務のシステムを開発しています！

どれくらい複雑か？というのは、この写真にあるように持ち運ぶには憚られる分厚い本が物語っています
-->

---

# 「レセプト業務」って何？
医療で言うところの診療報酬明細書（医療点数が書かれているアレ）を作成するお仕事です

<Transform :scale="1.2">

* **🧾 概要**  
障害福祉サービスや介護事業所で提供されたサービスの費用を計算し、  
市町村や国保連などの支払い機関に請求する業務です。  
利用者は費用の一部（利用者負担額）を支払い、  
残りの公費・保険負担分を正確に計算し請求するのが重要な役割です。  
計算の基となるのは、サービス内容に応じて定められた「単位数」です。  
これは「単位数表」で定義されています。  

* **🗂️ 計算ルールをまとめたもの=単位数表を図式化したもの＝算定構造**  
単位数を計算するプロセスを「算定」と呼びます。  
算定には国が定めた非常に細かくて複雑なルールがたくさんあります。  
これらの算定ルール及び、単位数表の関係を図式化したものを「算定構造」と呼びます。  
この算定構造は表形式でまとめられています。

</Transform>

<!--
レセプト業務は非常に複雑な計算ルールを持つ業務です  

概要として、障害福祉サービスや介護事業所で提供されたサービスの費用を計算し、市町村や国保連などの支払い機関に請求する業務です。  

利用者は費用の一部、利用者負担額、を支払い、残りの公的支援分を正確に計算し、請求するのが重要な役割です。  

算定構造の複雑さですが、計算の基となるのは、サービス内容に応じて定められた「単位数」です。これは「単位数表」で定義されています。  
単位数を計算するプロセスを「算定」と呼び、国が定めた非常に細かくて複雑なルールがたくさんあります。
-->

---

# 算定構造表（実物）
https://www.mhlw.go.jp/content/12200000/001205331.pdf#page=2

<Transform :scale="0.7">

<img src="/calculation-structure.png" />

</Transform>

<!--
実際の算定構造表。厚生労働省が公開している公式資料になります  
ご覧いただくとわかる通り、非常に複雑で、さまざまな条件に応じた算定ルールが細かく定められています。
-->

---
layout: statement
---

# 結構複雑。。

<!--
結構複雑です  
例として1画面にある程度収まるものをピックアップしたのですが、もっと巨大で複雑なものがあります
-->

---

# 元の原文（実物）
報酬告示という内容になります。官報で縦書き

<Transform :scale="0.7">

<img src="/notice.png" />

</Transform>

https://www.mhlw.go.jp/content/001239565.pdf#page=3

<!--
さらに複雑なのは、これらの表形式の資料の元となる原文です。  
報酬告示と呼ばれるものです。  
官報という形式で縦書き且つ、法律の文体（イロハにというような号立て）で書かれており、非常に理解しづらくなっています。
-->

---
layout: statement
---

# もっと複雑。。

<!--
時間の関係で詳細は省略しますが、このような複雑なルールをいかにコードで表現するかが課題となります。
-->

---
layout: statement
---

# 😥 どうやって実装すれば。。

<!--
このような複雑なルールを、どのようにコードとして実装すべきでしょうか？
-->

---
transition: slide-up
---

# ともあれ何も考えなしに実装するとこうなりがち。。
算定ルールは巨大な if と switch 文となる

<Tweet id="476731996878553088" />

<!--
ともあれ考えなしに実装すると、算定ルールは巨大なif文とswitch文となってしまいます。  

いわゆる「波動拳」の様なネストの深いコードになりがちです
-->

---
transition: fade
---

# 手続き的にルールを書くことの弊害
色々あるけれど

* **コードのネストが深く可読性が低い**  
波動拳が炸裂しがち
* **認知負荷が高い**  
元々複雑^[本質的な(Essential)複雑さ]で図式化されているのに、手続きとして文章化（コード化）すると、複雑さが増す^[付随的な(Accidental)複雑さ]  
インピーダンスミスマッチ ^[ルールは宣言的だが、手続きとして纏め直すと直感的でなくなる。重複も発生する] が発生する
* **同一規則のルールが散らばってしまう**  
* **条件に関する入力データ（事実）も条件の組み合わせ（ルール）によって必要なものが変わってくる**  
事実、条件、動作までを密結合させてしまうと、更に複雑になってしまう

<!--
手続き的にルールを書くことの弊害として以下の問題があります

まず、コードのネストが深くなって可読性が低くなります

図で整理されたルールも、文章やコードにするとわかりにくくなりがちです。  
宣言的な内容を手続き的に書き直すと、直感的に理解しづらくなり、重複やズレが起きることがあります。　　　

同じようなルールが複数箇所に書かれてしまい、重複や矛盾が生じやすくなります。  

条件に対応した入力データは、ルールや組み合わせによって必要なものが変わってきます  
これらを動作ロジックと密結合すると、構造が複雑化し、一貫性の確保や保守が困難になります
-->

---
layout: statement
transition: slide-up
---

# 💡デシジョンテーブル（決定表）の実装の機運！

<!--
そこで、デシジョンテーブルの実装パターンの出番です！
-->

---
layout: two-cols-header
transition: fade-out
---

# デシジョンテーブル（決定表）の使いドコロ
このスライドにピンときたら

::left::


## 💼 有効な業務

* 審査・承認業務  
* 診断・レコメンデーション業務  
* リスク評価・不正検知
* 自動応答・問い合わせルーティング
* 業務フローの自動化・タスク割り当て
* コンフィギュレーション・設定

::right::

## 🏛️ 適している業務の特徴

* 判断基準が明確  
意思決定や処理の分岐が、複数の論理的なルールによって定義できる。
* ルールの変更が発生する可能性  
<span v-mark="{ at: 1, color: 'orange', type: 'highlight' }">法改正</span>、社内規定の変更、新商品の追加などにより、判断基準が<span v-mark="{ at: 1, color: 'orange', type: 'highlight' }">比較的頻繁変わる</span>ことがある。
* 例外処理が多い  
定型的な処理だけでなく、多様な例外ケースに対する対応が必要。
* 属人化された知識の形式知化  
特定の担当者の経験や知識に基づいた判断基準を、誰でも理解できる形で共有・自動化したい。

<!--
このスライドにピンときたらディシジョンテーブルを使ってみてください。  

ルールや判断ロジックに基づいて処理・応答・割り当てを行う、自動化・支援に適した業務が有効になります 

[click] 判断基準は明確でルール化しやすいものの、頻繁な制度変更や例外対応により、運用が複雑化しやすいのが適している業務の特徴です
-->

---
layout: image-right
image: /theme.jpeg
transition: fade-out
---

# PHPでのデシジョンテーブル実装テクニック

<!--
それでは、具体的な実装テクニックを見ていきましょう。
-->

---

# Enumを活用した条件定義
条件定義の構造化アプローチ

<Transform :scale="1.1">

* **📦 条件は全てEnum（列挙型）の配列で定義**  
  🚫 クラス定数や文字列のマジックナンバー使用を排除  
  🛠️ 条件の定義場所を一元化し保守性を向上

* **🗣️ Enumの区分値はドメイン用語（日本語）で定義**  
  🤝 開発者とビジネス担当者の共通言語として機能  
  例: `EmploymentStatus::社員`, `EmploymentStatus::契約社員`  
  🔍 コードとビジネスルールの対応関係を明確化

* **🔢 数値条件もEnum化**  
  📏 境界値を明示的に表現  
  例:  
`AnnualIncome::３００万未満`, `AnnualIncome::３００-５００万未満`,  `AnnualIncome::５００万以上`  
  🧪 変換ロジックも凝集させてテストしやすく

</Transform>

<!--
まず、条件の定義を構造化します  

条件は全てEnum（列挙型）の配列で定義します。  
クラス定数や文字列のマジックナンバー使用を排除し、条件の定義場所を一元化し保守性を向上させます。  

Enumの区分値はドメイン用語を日本語で定義します。  
開発者とビジネス担当者の共通言語として機能し、例えば、EmploymentStatus(エンプロイメント・ステータス)・社員、若しくは契約社員のように、コードとビジネスルールの対応関係を明確化できます。  

数値条件もEnum化します。  
境界値を明示的に表現し、例えば、AnnualIncome(アニュアル・インカム)・300万未満、300から500万未満、500万以上のように、変換ロジックも凝集させてテストしやすくします。

実際のコード例を見てみましょう。
-->

---

# Enumを活用した条件定義（コード例）

* **📦 条件は全てEnum（列挙型）の配列で定義**  
日本語も使うと、決定表とほぼ同じになる(正社員、勤続3年以上、年収500万以上の例)  
  ```php
  [ EmploymentStatus::社員, Tenure::３年以上, AnnualIncome::５００万以上 ]
  ```

* **🔢 数値条件もEnum化**  
境界値もわかりやすく
```php {*|3-5|7-14|*}{lines:true}
enum AnnualIncome
{
  case ３００万未満;
  case ３００-５００万未満;
  case ５００万以上;

  public static function convert(int $value) : self
  {
    return match(true) {
      $value >= 500 => self::５００万以上,
      $value >= 300 => self::３００-５００万未満,
      default => self::３００万未満
    };
  }
}
```

<!--
条件は全てEnum、列挙型、の配列で定義します  
日本語も使うと、デシジョンテーブルと同じになります。  
  
数値条件もEnum化し、境界値もわかりやすくします。  

[click] 3つの区分値に対して  
[click] 変換処理を定義します

[click] このように、Enumを使用することで型安全性を確保しながら、ドメインルールを直接コードに反映できます。
-->

---

# 決定ルールをGenerator関数で定義
効率的なルール定義と実行

<Transform :scale="1.2">

* **⚙️ 決定ルールはGenerator関数で定義**  
  🔁 イテレータとして条件の組み合わせを生成  
  `yield` と `yield from` で柔軟に手続き的にルールを宣言できる

* **📆 時系列で条件を変化させる**  
  🕰️ 制度変更や期間限定ルールに対応  
  📅 日付による条件の切り替えを明示的に表現

* **🧱 ルールの実態クラスのオブジェクトは別途生成**  
  📜 規約ベースでルールを生の配列で記述する方が、見やすくメンテナンスしやすい  
  🧊 必要なケースだけを遅延評価でオブジェクト生成させる  
  大半のオブジェクトが生成（評価）されないので Generator と相性が良い

</Transform>

<!--
次に、決定ルールの定義方法です  
  
決定ルールはジェネレーター関数で定義し、yield や yield from を使って条件の組み合わせを柔軟に生成・記述できます。  


時系列に応じて条件を切り替えることで、制度変更や期間限定ルールなど、日付ベースの分岐を明示的に表現できます。

ルールの実体クラスは必要な分だけ遅延生成し、定義自体は配列ベースで記述します  
これにより、見通しがよくメンテナンスしやすくなり、大半のルールが未評価のままで済む為、ジェネレーターとの相性も良いです。
-->

---

# ⚙️ 決定ルールはGenerator関数で定義（コード例）
📆 時系列で条件を変化させたり、部分的に繰り返しルール適用したりする

<Transform :scale="1.2">

```php {*|1,4-5|6,11-16|8-9|*}{lines:true}
/**　@return Generator<array{ 0: string, 1: list<Condition>}> 0: 動作部, 1: 条件部 */
public function generateRules(): Generator
{
  yield ['承認', [EmploymentStatus::社員, Tenure::勤続３年以上, AnnualIncome::３００-５００万未満]];
  yield ['承認', [EmploymentStatus::社員, Tenure::勤続３年以上, AnnualIncome::５００万以上]];
  yield from generateDisapprovalRules();  // ❌ 無職は一律非承認

  if ($reviewIn >= '202507')  // 📅 2025年7月以降
    yield ['詳細審査', [EmploymentStatus::契約社員, Tenure::勤続３年以上, AnnualIncome::５００万以上]];
}
private function generateDisapprovalRules(): Generator
{
  foreach (Tenure::cases() as $t)
    foreach (AnnualIncome::cases() as $i)
      yield ['非承認', [EmploymentStatus::無職, $t, $i]];
}
```

</Transform>

<!--
こちらのページにあるように決定ルールをGenerator関数で定義します

[click] yieldで戻り値の要素となる決定ルールを順次生成していきます

[click] yield fromで他のジェネレーター関数に委譲して定義して、まとめて返却することができます。  
ここでは無職は一律非承認のルール生成して一括返却しています

[click] 時系列で決定ルールを変化させます  
ここでは7月以降のみルールを緩和させています

[click] このGenerator関数では規約ベースで生の配列で定義します。  
こうすることで見通しがよくメンテナンスしやすいです。
-->

---

# 🧱 ルールの実態クラスのオブジェクトは別途生成
🧊 オブジェクト生成は外側で行い、ルール自体は遅延評価する

<Transform :scale="1.5">

```php {*|1-6,9|3|4|1,9-12|*}{lines:true}
public function createRuleInstances(): Generator
{
    foreach ($this->generateRules() as [$action, $conditions]) {
        yield new Rule($action, $conditions);
    }
}
public function getMatchAction(array $facts): ?Action
{
    foreach ($this->createRuleInstances() as $ruleInstance) {
        if ($ruleInstance->canMatch($facts))
            return $ruleInstance->makeAction(); // ⚡️ 動作を実行。以降は⛔️
    }    
    return null; // 条件にマッチするものがない
}
```

</Transform>

<!--
ルールの実態クラスのオブジェクト生成はGenerator関数の外側で行います  

[click] ルールの実態オブジェクトの生成自体もGenerator関数で定義します

[click] 生のルールの配列を1件づつ読み込みます

[click] ルールの実態オブジェクトをここで初めて生成します

[click] ルール実態を評価して、マッチしたら動作を実行します。以降はオブジェクト生成含めて評価されなくなります

[click] この様な処理手順でルール自体を遅延評価させることで、余分なオブジェクト生成しないので効率的な処理ができます
-->
---

# 特殊条件の型による制御
型システムを活用した柔軟な条件定義

<Transform :scale="1.2">

* **🧩全ての条件に共通となるマーカーインタフェースを付与（🛡️ 型安全性の確保）**  
```php {*|1|2-4|5-6|*}{lines:true}
interface Condition {}
enum AnnualIncome implements Condition {
  // .. snip ..
}
/** @param list<Condition> $facts */
public function getMatchAction(array $facts): ?Action
```

<v-click>

* **🎯複合条件の中から、一つの条件を選択**  
複数条件の組み合わせを型で制御  
  🏷️ 例: 顧客に複数の割引条件が適用可能な場合に、一番割引率が高いもののみを適用させる
</v-click>

<v-click>

* **⚙️動作への特殊制御**  
特殊制御用の型を定義する。決定ルールには使われないが、動作には反映される  
  🔁 例: 動作を繰り返し実行させる
</v-click>

</Transform>

<!--
より高度な制御のために、型システムを活用します。　　

全ての条件に共通となるマーカーインタフェースを付与し、型安全性を確保します。

[click] Conditionがマーカーインターフェースになります

[click] 各条件クラスはこのマーカーインターフェースを実装します

[click] 条件を扱う関数はすべて型安全で書ける様になります

[click] この共通インターフェースは次の型制御につながります

[click] 複合条件の中から、一つの条件を選択し、複数条件の組み合わせを型で制御することができます

[click] 動作への特殊制御として、特殊制御用の型を定義します。決定ルールには使われませんが、動作には反映されます。例えば、動作を繰り返し実行させることができます。
-->

---

# 🎯複合条件の中から、一つの条件を選択（型宣言例）
割引率を定義できるようにして、1 つの条件のみを選択させる

<Transform :scale="1.2">

```php {*|1-4|5|3,11-18|*}{lines:true}
interface DiscountCondition extends Condition
{
  public function getDiscountRate(): float;
}
enum MemberRank implements DiscountCondition
{
  case ランクA;
  case ランクB;
  case ランクC;

  public function getDiscountRate(): float
  {
    return match($this) {  // この割引率を他のDiscountConditionのものと比較する
      self::ランクA => 0.3,
      self::ランクB => 0.15,
      self::ランクC => 0.0
    };
  }
}
```

</Transform>

<!--
複合条件の中から、一つの条件を選択し、複数条件の組み合わせを型で制御する例です

[click] 具体的な実装例では、割引率を定義できるようインターフェースを定義します

[click] 会員ランクに応じた割引率を定義します

[click] この会員ランクの割引率と、例えば誕生月の割引率を比較とかして一番割引率が高いものを適用させることができます

[click] 一つの条件のみを選択させるような実装が可能です。
-->

---

# ⚙️動作への特殊制御（型宣言例）
ルールの条件として含まれない特殊な型で、動作の評価時に参照して特別な処理を行う

<Transform :scale="1.4">

```php {*|1-4|1,5,11-15|*}{lines:true}
interface RepeatableCondition extends Condition
{
  public function getCount(): int;
}
enum RepeatCount: int implements RepeatableCondition
{
  case なし = 0;
  case １回 = 1;
  case ２回 = 2;

  public function getCount(): int
  {
    // この回数分、動作を繰り返す
    return $this->value;
  }
}
```

</Transform>

<!--
動作への特殊制御として、特殊制御用の型を定義します。

決定ルールには使われませんが、動作には反映されます。例えば、動作を繰り返し実行させることができます。

[click] 動作の繰り返し条件のインターフェースを定義します

[click] この回数分動作を繰り返すという感じです

[click] 実用例として、キャンペーン景品の配布があります。  
例えばキャンペーンで「この条件に当てはまった時に景品をN個プレゼントする」という処理が実現できます。
-->

---
transition: fade
---

# 責務分離による実装
一番複雑な決定ルールを安定させる為にできること

<Transform :scale="1.4">

* **✂️ 決定ルール・Enum変換・条件判定はそれぞれ独立したクラスに**  
  **📐 単一責任の原則（SRP）に基づく設計**  
  **🔹 変更の影響範囲を最小化**  
  入力データに関連する変化があっても決定ルールには影響しない  
  **🧪 テスト容易性の向上**  
  入力データとなる事実の収集は I/O が重いので、分離したほうが良い  
  一番テストケースが多い決定ルールのテストサイズを小さくできる
* **🔌 条件判定処理を共通化し決定ルールの差し替えを可能とする**  
  責務が分離できていれば決定ルールの差し替え

</Transform>

<!--
より安定した実装にするために、決定ルール・Enum変換・条件判定はそれぞれ独立したクラスにします。  

クラスを分離することで、変更の影響を局所化し、テストしやすく保守性の高い設計が可能になります。 条件判定処理を共通化すれば、決定ルールの差し替えも容易になり、柔軟な拡張が可能です。
-->

---
layout: image
image: /decision-table-class-diagram.svg
backgroundSize: 80%
transition: slide-up | slide-down
---

<!--
ディシジョンテーブルの関連クラスはこの様に層を分けて責務を分離します  
-->

---

# デシジョンテーブルの品質を安定させる為に
ディシジョンテーブルの構造を最大限に利用する

<Transform :scale="1.4">

* **✅️ デシジョンテーブルの型を厳密にチェックする**  
  🛡️ 決定ルール判定時に条件の Enum の過不足がないことをチェックする  
  ⚠️ Enum 変換時に決定ルールの変換漏れや重複はバグなので咎める  
  🚫 Enum が存在しない = 条件を不問とはしない
* **📊 デシジョンテーブルから全因子テストを行う**  
  🔁 Enum の cases 全ての組み合わせを生成して後続処理も含めて動作検証する  
  🔍 ディシジョンテーブルに矛盾がないか？をセルフチェックできる

</Transform>

<!--
デシジョンテーブルの特性を最大限に活用して品質を高めます　　

デシジョンテーブルの型を厳密にチェックします。　　
決定ルール判定時に条件のEnumの過不足がないことをチェックし、Enum変換時に決定ルールの変換漏れや重複はバグなので例外をスローして咎めます。

デシジョンテーブルから全因子テストを行います。  
Enumのケース、全ての組み合わせを生成して後続処理も含めて動作検証します。デシジョンテーブルに矛盾がないか？をセルフチェックできます。
-->

---
layout: image-right
image: /theme.jpeg
transition: fade-out
---

# まとめ

<!--
まとめです
-->

---

# 導入のメリットと実践的な効果
デシジョンテーブル自体の特性によるメリット

<Transform :scale="1.4">

* **🧼 コードの品質と保守性向上**  
  **🧩 条件分岐ロジックを構造化・可視化**  
  複雑な条件分岐を表形式で整理することで直感的な理解が可能  
  **🧹 ネストの深いif文やswitch文を解消**  

* **✏️ 変更容易性の大幅な向上**  
  **💪 ビジネスルールの変更に強い構造**  
    ルールの追加・変更・削除が表形式で明示的に管理可能  
    条件の組み合わせパターンごとに独立して修正できるため変更の影響が局所化  
  **⏱️ 時間軸に沿った変更**  
    適用日や期間限定ルールを明示的に管理  
    バージョン管理や履歴保持が容易になる構造

</Transform>

<!--
条件分岐ロジックを表形式で構造化・可視化することで、ネストの深いif文やswitch文を解消し、コードの可読性と保守性が向上します。  
ビジネスルールの変更にも強く、ルールの追加・変更・削除が局所的に行えるため、変更の影響範囲を最小化できます。
-->

---

# 導入のメリットと実践的な効果
デシジョンテーブルとその周辺の構造（アーキテクチャ）がもたらすメリット

<Transform :scale="1.5">

* **✂️ 決定ルールと入力データの関心事を分離**  
  ビジネスルールとデータ処理ロジックの明確な切り分け  
  コードの責務が明確になり変更の影響範囲が限定される

* **🛡️ 決定ルール自体の品質担保が容易**  
  ルールだけを独立してテスト可能  
  変更箇所が明示的で影響分析が簡単

* **🔄 入力データが変わっても影響が少ない**  
  数値項目と条件の変換は必要だが、基本ロジックは安定  
  入力データ形式の変更に強い設計

</Transform>

<!--
ビジネスルールとデータ処理を分離することで、コードの責務が明確になり、変更の影響を最小限に抑えられます。  

また、ルールは単体でテストしやすく、入力データの変更にも柔軟に対応できるため、品質と保守性の高い設計が可能です。
-->

---

# 導入のメリットと実践的な効果
デシジョンテーブルを活用することでのメリット

<Transform :scale="1.5">

* **🔍 条件判定結果の検証が容易**  
  対応するマスターデータとの照合が容易  
  想定結果との差異を迅速に特定可能

* **🧪 全パターンテストによる品質向上**  
  条件判定処理の妥当性を自動的に検証  
  組み合わせテストの実装が標準化され効率的

* **👥 PICTとの相性の良さ**  
  組み合わせテスト手法との親和性が高い  
  テストケース数の効率的削減が可能

</Transform>

<!--
条件判定の結果はマスターデータと照合しやすく、差異の特定も容易です。  
全パターンテストを自動化することで、品質を高めつつ効率的な検証が可能になります。  
また、PICTなどの組み合わせテスト手法とも相性が良く、テストケース数を抑えつつ、条件間の相互作用を網羅できます
-->

---

# 参考資料

<OgpImage url="https://kikakurui.com/x0/X0125-1986-01.html" />

<OgpImage url="https://www.slideshare.net/slideshow/php55/14301315" />

<OgpImage url="https://media.shaho.co.jp/n/n9b8a01360419" />

<!--
参考資料をまとめておきます
-->

---
layout: end
---

ご清聴ありがとうございました

<!--
ご清聴ありがとうございました
-->
