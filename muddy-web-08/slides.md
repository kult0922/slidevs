---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides, markdown enabled
title: 新規と並走したリファクタリング戦略
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# 新規開発と並走した

# リファクタリング戦略

株式会社 AI-Shift Frontend Engineer

Kazuma.Kurisaki (@KK_sep_TT)

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
それでは
-->

---

### Who am I

# 栗崎一真 (Kurisaki Kazuma)

#### 2023.5 AI-Shift 中途入社 フロントエンドエンジニア

<br>
<br>

- **Carrier:** 2021卒 Rakuten → CyberAgent
- **Role:** ️Chat bot, Voice bot の管理画面の開発、保守、運用
- **Hobby:** ️♠️ 🦑 💻 🚶

<img
  class="absolute my-auto top-0 bottom-0 right-12 w-60"
  src="https://avatars.githubusercontent.com/u/38486600"
  alt=""
/>

X: [@KK_sep_TT](https://twitter.com/KK_sep_TT)

GitHub: [@Kult0922](https://github.com/kult0922)

<!--
まずは簡単に自己紹介します。
栗崎一真です。
21卒のエンジニアで新卒のときは楽天に入社して、ゴルフ場予約サービスの開発をやっていました。
そこで2年開発をやって去年 AI-Shift に転職してきました。

趣味はポーカー、スプラトゥーン、個人開発、旅行とかです。

何か同じ趣味の人がいたらこの後の懇親会でも話しかけてください。

twitter, GitHub はこんな感じです。
-->

---

# 今日話すこと

1. AI-Shift のプロダクト紹介
2. フロントの泥の紹介
3. リファクタの戦略と歩み
4. やってよかった取り組み、ツールの紹介
5. まとめ

<!--
今日話すことはこんな感じです。
まずAI-Sfhit の開発しているプロダクトについて簡単に説明します。
その後に今の我々のフロントエンドの泥について紹介します。

続いて、その泥を解消するためのリファクタ戦略と歩みについて話します。

その中でやって良かった取り組みだったり、使っていたツールなどに付いても紹介できたらなと思っています。
-->

---

# つくっているもの

AI messanger シリーズ

<span class="absolute right-50 top-15">こんな管理画面を作っています</span>

<div class="absolute w-140 h-110 top-22 right-10 border-2 border-blue"></div>
<img width="300px" src="/image/service.png">
<img width="280px" class="absolute right-70 top-25" src="/image/callLog.png">
<img width="280px" class="absolute right-70 top-80" src="/image/scenario.png">
<img width="160px" class="absolute right-15 top-40" src="/image/widget.png">

<v-click>
<span class="absolute text-sm right-15 top-125">結構複雑です 🌝 </span>
</v-click>

<!--
まずは僕たちが作っているものについて簡単に紹介させてください。

AI-Shift では現在 AI-messager シリーズと呼ばれる、カスタマーサポート領域のDXを進めるSaaSを開発しています。

LINEのようなインターフェースでカスタマーサポートを行うChat bot, 電話応対をAIで自動化するvoice bot、それから、コールセンターの通話内容を要約してまとめる Summary の 3つのプロダクトを開発、運用しています。

右側にいくつか実際のプロダクトの写真をのせています。

見て分かるかもしれませんが、結構複雑です。

左下の画面は、chat bot の設計画面で、遷移図を書いてbotの挙動を管理画面からカスタマイズできるようになっています。

単なる管理画面というよりノーコードツールに近いアプリケーションを開発しているイメージです。
-->

---

# フロントエンドは一枚岩

Chat bot, Voice bot, Summary のすべてが同じレポジトリで管理されている

<img width="460px" class="absolute top-30 left-65" src="/image/rock.webp">
<img width="100px" class="absolute top-50 left-80" src="/image/voice_logo.png">
<img width="100px" class="absolute top-60 left-130" src="/image/chat_logo.png">
<img width="100px" class="absolute top-90 left-80" src="/image/summary_logo.png">

<!--
さきほど、3つのプロダクトがあると説明しましたが、これらの3つのプロダクトのフロントエンドは一枚岩です。
chat bot, voice bot summary はすべて同じレポジトリで管理されていて、1つのSPA上で動いています。

なので、同一管理画面上で、テナントのお客様がchat bot, voice bot, summary 3つのプロダクトを管理できるよういになっています。
-->

---

# 泥がたまってきている

- **🌪 ️ 古いバージョンのライブラリ**
  - React v16, Node v16, Webpack v4, React Router v5, Fireabasse SDK v7
- **🌝 巨大なコンポーネントファイル**
  - 1ファイル 3000 行
- **🤹 複雑なアーキテクチャ**
  - 関数propsバケツリレーによるロジックのDI
  - カスタム hooks 不使用
  - useEffect Chain
- **🪤 複雑な設定のwebpack**
  - ミドルウェアの設定
  - 大量の環境変数

<!--
こんな一枚岩の巨大なフロントエンドなんですが、結構泥が溜まってきています。

具体的にどんな問題があるが、少しここにリストアップしてみました。

<スライドの説明>
-->

---

# どこから手をつけようか

リスクとリターンのトレードオフ

- 💥 <span class="text-red font-bold underline">リスク</span>

  - 時間をかけても達成できず、時間を浪費する
  - 変更の結果、より状況を悪化させてしまう

- 💎 <span class="text-blue font-bold underline">リターン</span>
  - 今後の開発速度アップ
  - コード変更の心理的安全
  - 開発がより楽しくなる

<!--
このように課題が多くあり、改善していかないと今後の開発速度に支障がでると思っていました。

ですが、やるべきことが多くありどこから手を付けようか迷っていました。

そこでリファクタのタスクのリターンとリスクについて考えてみました。

リファクタリングをすると一時的に通常の開発に支障をきたすこともあるので、ここは戦略的にいかなければいけません。

<スライドの説明>
-->

---

<span class="absolute text-sm top-10 bottom-0 right-40"> webpack → vite </span>
<span class="absolute text-sm top-30 right-60"> firebase v9 update </span>
<span class="absolute text-sm top-40 right-60"> エラーログ改善</span>
<span class="absolute text-sm top-50 right-60"> emotion update</span>
<span class="absolute text-sm top-15 right-30"> リアーキテクト</span>
<span class="absolute text-sm top-20 right-60"> React v18 update </span>
<span class="absolute text-sm top-20 right-30"> Redux → SWR </span>
<span class="absolute text-sm top-20 right-160"> コンポーネント分割 </span>
<span class="absolute text-sm top-40 right-200"> ドキュメント整備 </span>
<span class="absolute text-sm top-30 right-160"> Atomic Design 廃止 </span>
<span class="absolute text-sm top-50 right-160"> ディレクトリ整理 </span>
<span class="absolute text-sm top-80 right-160"> Node update </span>
<span class="absolute text-sm top-90 right-160"> 日付ライブラリ導入 </span>
<span class="absolute text-sm top-90 right-200"> ローディングUI整備</span>
<span class="absolute text-sm top-90 right-20"> useEffect chain 解消</span>
<span class="absolute text-sm top-90 right-100"> yarn update</span>
<span class="absolute text-sm top-80 right-20"> React router v7 update</span>
<span class="absolute text-blue top-2 right-105"> リターン </span>
<span class="absolute text-red top-60  right-6"> リスク </span>

<arrow  x1="480" y1="530" x2="480" y2="30" color="#000" width="2" arrowSize="1" />
<arrow  x1="30" y1="270" x2="900" y2="270" color="#000" width="2" arrowSize="1" />

<v-click>
<span class="absolute top-4 left-10 text-red font-bold underline"> おとく！ </span>
<div class="absolute left-10 bottom-72 h-[40%] w-[40%] border-2 border-red"></div>
</v-click>

<v-click>
<span class="absolute top-4 right-20 text-red font-bold underline"> 大物 </span>
<div class="absolute right-20 bottom-72 h-[40%] w-[40%] border-2 border-red"></div>
<img class="absolute w-30 right-22 top-30"  src="/image/big.webp" />
</v-click>

<v-click>
<arrow  x1="400" y1="140" x2="600" y2="100" color="red" width="3" arrowSize="1" />
</v-click>

<!--
リスクとリターンについて既存の課題を分類してみました。

上に行くほど、改善したときのリターンが大きいです。
そして、右にいくほど、リスクが高い図となっています。

例えば、react v 18 へのアップデートや、webpack から vite への乗り換え、フロントエンドのリアーキテクトは達成したときのインパクトが大きいですが、リスクも大きく右上にいます。

左上のものはローリスク、ハイリターンでコスパが良いです。
なので最初は左上の改善に取り組むことにしした。

その後、自分のドメイン知識が付いてきた段階で右上の大物をかるという順番で改善タスクに取り組んできました。
-->

---

# リファクタリングの優先順位

- リターンが大きく、リスクが小さいものから
- ドメイン、コードへの理解が深まってから大物を狩りに行く
- 簡単なリファクタリングでも大きなリファクタリングの土台になる

<br>

<img width="600px" src="/image/levelup.webp">

<!--
リファクタの優先順位はリターンが大きくリスクが小さいものから取り組みました。

そして、ドメインやコードベースへの理解が深まってから大物を狩りにいきました。

このとき、最初にやった、ローリスクのリファクタによる改善が土台となり、大物改善タスクをやるにあたっての助けとなったので、簡単なタスクからやって良かったと思いましたし、そういった、ローリスクのタスクも効果は大きいものが多くあるなと感じました。

そんなこんなで、日々の開発の隙時間に一人でちまちまと改善タスクを取り組んでいまいた。

このときは大きな新規開発がなかったのでなんとか一人で回っていまいた。
-->

---

# 新規開発の波と新メンバー

新規開発が本格的にはじまった

- 同時並行で3つの新プロジェクトが走る
- 新規メンバー加入
  - <span class="text-red font-bold underline">正社員2人と内定者バイト1人</span>のチーム

<img width="400px" src="/image/member.png">

<!--
ただ、こんな状況は長く続かず、新規開発の波が押し寄せました。
同時並行で3つの新規開発プロジェクトが立ち上がり、一気にやることが多くなりました。

だた、今まで僕一人で開発していたところに新メンバーが入ってきて3人で開発できるようになりました。

中途の正社員の方と内定者バイトの方の3人チームで開発をすることになりました。
-->

---

# チームメンバーは別プロジェクトへ

メンバーそれぞれが、異なるプロジェクトに配置

フロントは一枚岩なのでコードベースは同じ

<img  width="500px" src="/image/team.png">

<!--
3つのプロジェクトを3人だったので、基本1人1プロジェクトという配置で開発を行いました。
なのでフロントエンドメンバーの3人は別プロジェクトで開発を進めていました。

ただし、フロントエンドは一枚岩なので同一アプリケーションににコミットしていまいた。

なので、各々が別プロジェクトですがお互いにレビューしあって開発を進めていきました。
-->

---

# 新規開発と並走したリファクタリング開始

チームメンバーで定例を週1で開催

- <span class="text-red font-bold underline">話すこと</span>

  - 保守、運用の目標を決める
  - 新規開発にあたり、優先して改善したいことを共有
  - コーディングガイドラインの策定
  - 遭遇したコードの問題の共有

- <span class="text-blue font-bold underline">話さないこと</span>
  - 各プロジェクトのタスクの内容
  - 各プロジェクトの進捗
  - 各プロジェクトの問題

<span class="absolute left-40 top-120">
こんな感じのページを作ってました
</span>

<arrow  x1="440" y1="460" x2="530" y2="400" color="#000" width="2" arrowSize="1" />

<img class="absolute right-10 top-20" width="390px" src="/image/notion.png" />

<!--
別プロジェクトですが、同じコードベース、同じアプリケーションを開発するので、チームとしての連携が重要でした。

なので週イチで現状の課題を共有する会を開催しました。

この定例では主にフロントエンドの現状の課題を共有して、優先して取り組む改善タスクを決めていまいた。

以前僕が一人でやっていた、リスク・リターンで改善タスクの優先付けをしていたようなことをチームでやっていた感じです。

また、それ以外にも改善チームとしての目標を決めたり、コーディングガイドラインの策定などをしていました。
-->

---

# 新規開発するときも改善を積極的に行う

新規開発で触る部分は可能であれば、リファクタリングする方針に

- 既存のコードの課題が多く、リファクタをした方が開発が早くなるケースも
- リファクタを行うかどうかは、<span class="text-red font-bold underline">実装者の判断</span> に任せて柔軟に対応

<!--
定例で話した結果、新規開発するときも改善を積極的に行う方針にするとに決めました。
-->

---

# レビューの戦略

1. 最初からリファクタと機能開発のコミットを分ける
2. 一旦、機能開発とリファクタをした後にコミットを綺麗に分ける
3. すべてをあきらめて <span class="text-red font-bold underline">同期的なコミュニケーションをとりながらレビュー</span>

<br/>

<span class="text-red">レビューの仕方を決めておくことで、安心してリファクタをPRに含めることができた</span>

そして、なにより

### リファクタリングをしてくれたメンバーに感謝 🙏

<!--
新規開発とリファクタを同時に進めるにあたってレビューの方針をチームで決めました。
リファクタと機能開発を同時にするとdiffが大きくなりレビュワーの負担が大きくなることが予想されたのでレビューの方針をみんなで決めました。
-->

---

# フロントエンドのリアーキテクト

- ディレクトリ構成
  - Atomic Desing → Feature ベース
- サーバーキャッシュ管理
  - Redux → SWR
- ローカル state 管理
  - コンポーネントにベタ書き → カスタムhookに分離
- 関数の多段バケツリレー
  - props から関数を受け取る → カスタムhookを通して関数を受け取る

かなりやることがある 😨

<v-click>
<span class="text-red font-bold underline">
すべての機能に対してリアーキテクトをするのは現実的ではない
</span>
</v-click>

<!--
ではリファクタ活動の一環として行ったフロントエンドのリアーキテクトについて紹介します。

具体的にどんなリアーキテクトかというとここに書いてあることを行いました。

<スライド読み上げ>
-->

---

# フロントエンドのリアーキテクト

一部の機能に対してリアーキテクトを実施

<span class="text-red font-bold underline">データの取得 → 画面に表示 → ユーザアクション → サーバリクエスト → 画面更新</span> の一連の流れをリファクタ

<img class="" width="500px" src="/image/arc1.png" />

<!--
全てに対してリアーキテクトを行うことは現実的ではないのでターゲットを絞ってリアーキテクトを行いました。


管理画面にはたくさんの機能が備わっていますが、それらの機能に対して部分的にリアーキテクトを行いました。

機能はたくさんありますが、おおよそどの機能も同じようなサイクルで動作しています。

なのでの一連の流れに対してリアーキテクトを行いました。

図にするとこんな感じで、たくさんの機能があるわけですが、その一部機能を新アーキテクチャに置き換えていきました。

同一機能ないで複数のアーキテクチャが混ざることはないようになっています。
-->

---

# フロントエンドのリアーキテクト

1つの指標ができたらそれに合わせてく

- 既存機能に変更を加えるときに可能ならリアーキテクトする
- <span class="text-red font-bold underline">新機能開発のときは新アーキテクチャで実装する</span>

<img class="" width="500px" src="/image/arc2.png" />

<!--
このようにターゲットを絞った機能を新アーキテクチャに置き換えることで指標ができます。

その指標をもとに他の機能に対してもリアーキテクトを行っていくことで段階的にリアーキテクトを進めていきました。

また、完全新機能については新アーキテクチャで組むようにしました。

実際このリアーキテクトはまだ全然完了していないので、これからも進めていきます。
-->

---

# 新規開発とリファクタのサイクル

- 変更を加えようと開発している時はそのドメインに対して真剣に考えているとき
- 新規開発で発生する<span class="text-red font-bold underline">新鮮でリアルな課題</span>をリファクタ活動で解消
- リファクタの結果、新規開発の生産性が上がる

<img class="absolute" width="350px" src="/image/cycle.png"></img>

---

# 改善活動を助けてくれたツール

- Notion
  - チームの活動ログ、定例まとめ
- Linear
  - 取り組むタスクの優先度, 進捗を管理
- Vite Press
  - コードのドキュメント管理

<!--
では次に改善活動で使って便利だったツールを紹介します。

まずはNotionですね。これはチームの活動ログ、定例のまとめに使っていました。Notionについてはみなさん、よく知っていると思うので深くは紹介しません。

タスク管理にはLiner, コードのドキュメントにはVite press を使用しました。
-->

---

<img class="" width="200px" src="/image/Linear_idpLeRmlXj_2.png" />

タスク管理ツール

特徴

- <span class="text-red font-bold underline">とにかく高速</span>
- 豊富なショートカット
- ベロシティの可視化

<img class="" width="400px" src="/image/linearProject.png" />
<img class="absolute top-51 right-5" width="480px" src="/image/linear1.png" />

<!--
Linear はタスク管理ツールです。

基本的にはJIRAと同じようなな感じで使えるんですが、UXがかなり　良くて使いやすかったです。
とにかくレスポンスが早く、ショートカットも豊富でかなりストレスフリーで操作することができます。
あと、ベロシティの可視化もかなり強いと思います。

Liner には粒度の大きいprojectという概念をつくってそこにタスクを起票していくんですが、この改善タスクをprojectとして起票して優先順位とか担当者を管理していました。
-->

---

# VitePress

# <img class="absolute left-50 top-5" width="70px" src="/image/vitepress-logo-large.webp" />

- 設計やコーディングガイドラインをドキュメント管理
- いい感じの見た目のドキュメントが簡単につくれる
- <span class="text-red font-bold underline">コードと一緒にGit管理</span>

<img class="absolute top-50" width="400px" src="/image/vitepressshift.png"></img>
<img class="absolute top-10 right-10" width="400px" src="/image/vitepressarc.png"></img>

<!--
コードのドキュメント管理にはvite pressを使い始めました。
これは内定者バイトの方がサクッといれてくれたんですが、結構いい感じです。

OSSのドキュメントのようなイケてるデザインのドキュメントを簡単に作ることができます。
AI-Shift ではコードの設計や、コーディングガイドラインのドキュメントをここで管理するようにしました。
コードと同じレポジトリでGit管理しているので、コードを変更したときに同時にアップデートしていけて良い感じです。

あと、見た目もカッコいいので気に入っています。
左の画像はトップ画面です。会社のおロゴやコミッター(開発メンバー)のアイコンを並べています。

検索機能が使えたり、Vueを書いて細かい挙動をカスタマイズできるなどの便利な機能もあります。
-->

---

# まとめ

新規開発と並走したリファクタリング戦略

- リファクタリングは <span class="text-red font-bold underline">ローリスク、ハイリターンなものから</span> 取り組んだ
- <span class="text-red font-bold underline">ターゲットを絞って</span>リアーキテクトを実施
- 機能変更、新規機能開発のときに可能なら新アーキテクチャで実装
- <span class="text-red font-bold underline">リファクタ込みのコード変更のレビューの方針</span>を決めた

## DXを改善しながら機能開発をするのは楽しい！

---

# ご清聴ありがとうございました！

---
