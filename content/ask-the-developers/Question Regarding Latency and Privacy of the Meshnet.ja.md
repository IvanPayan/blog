+++
title = "Meshnetのレイテンシーとプライバシーに関する質問"
tags = [
    "Ask the Developers",
    "Skywire",
    "Meshnet",
    "Privacy",
]
bounty = 8
date = "2017-08-18"
categories = [
    "Skywire",
    "Ask the Developers",
]
+++

*この記事はSkywire開発初期の、まだSkywireと名付けられる以前の記事です。 2014年8月9日に公開されたビットコイントークの投稿から改稿しました。*

引用 : CraigM on 2014年8月9日, 午前07:20:24

>プライバシー性の高いツールであることを意図して作られており、一方レイテンシーは低いようですが、これは正しいですか？

>Meshnet は、skycoinのマイクロペイメントによるノードへの資金提供により、帯域幅のコストを正確にカバーすることを目的としていますか？
 これは、ノードを介してデータを送信するすべての人に本質的に対応する、すべてのトランザクションを詳細に記録して公開されたログ（個人のブロックチェーン上の）を、すべてのノード演算子に強制的に残しませんか？ 
 これは、コネクションへアクセスを必要としない時以外に、Torで行われるものとよく似ている、サードパーティがトラフィックの相関攻撃を行うことを可能にするようにみえます。
たとえ彼らが最後には表向き検査可能にならなくても、すべてのロギングには具体的な問題があるように思えます（法の執行機関から要求されることができ、そして多くの領域を取り上げる）。

>初期バージョンでは、集中ルーティング化検索サーバーは正しく搭載される予定でしょうか？ 
つまり、誰かに接続したい場合はそれについてサードパーティに伝えなければなりません、正しいですか？
それは固定されるまでは、Torのプライバシーサービスのようではないようです。
すぐに、この解決策を見つけられると信じる理由はありますか（それともこれは難しいですか）？

>どのようにルート検索を行う、信頼できるサードパーティへのルートを見つけるのですか？
私はそれが特別なケース（送信側ルート選択を使用しない）と仮定しますが、あなたが別の計画を持っていれば興味があります。

## 回答

はい。それは実際にはTCP / IPより高速です。 ISPは「ホットポテト」ルーティングを行います。
レイテンシーはTCP / IPより遅くするべきではなく、理論的にはより速くなる可能性があります。

**プライバシーの保証は**

- 各ノードは前のホップと次のホップしか知りません。
- ノード間のトランスミッションは暗号化されます。
- トランスミッションはエンドツーエンドで暗号化されます。
- あなたのトランスミッションは、ノードのエンドポイント（ネットワークアドレスはネットワーク上の公開鍵）の公開鍵を使用して中間者攻撃から保護されます。
- すべての暗号化は否認可能で、一時的です。
- このプロトコルは、プロトコルを実行しているユーザーを識別するための、詳細なパケット検査の試みを妨ぐように設計されています（固定ポートなし、ワイヤ形式の平文は知らされない、バックボーン用の固定ノード接続など）

したがって、これは帯域幅のためのマイクロペイメントを備えた、非常に低いレイテンシーを持つTorのようなものです。

- HTTPSよりも安全でプライバシーが高いです。
- それはTorよりも速くスケールしますが、まだそれに対する攻撃があります。
- コードはTorよりはるかに簡単なので、バックドアや隠された脆弱性のための領域が少なくなります。実装全体には1つの外部依存関係しかありません。
- タイミングチャネル攻撃に対して絶対的なセキュリティが必要な場合は、ミキシングサービスを使用するか、ダークネットの上にBitmessageを実行する必要があります。

## ルートサーバー

はい、ルートサーバーは弱いところがあります。
プライバシーを最大限に高めるには、あなた自身の内部ルートサーバーを実行する必要があります。

ただし、公共のルートサーバーを使用している場合は、あなたはいくつかのホップでそれに接続されているので、あなたを識別することはできません。 
あなた自身でそれを実行することはまだ安全です。

## 帯域幅のマイクロペイメントの処理

マイクロペイメントの取り扱いは、サードパーティを通じて行われます。 
ノードは「**ゲートウェイ**」に接続し、クレジットを得るためにゲートウェイにコインを入金します。
ノードは、ゲートウェイで匿名64ビット「アドレス」を生成できるようになりました。
ゲートウェイは接続ノードのIDを知りません。
それは接続が完了する前のホップだけを知っています。

したがって、ゲートウェイへの12の接続を確立すると、それらはゲートウェイに対して12人の異なるユーザーのように見えます。
最終的にゲートウェイへの通信は、非同期のメッセージングチャネルを介して行われます。

帯域幅を転送するノードは、ゲートウェイにも接続します。
ゲートウェイは、2つのノードのどちらかのIDを知ることなく、2つのノードがゲートウェイを介してお互いに支払うことができます。
ノードはクレジットに十分なコイン（フルコイン）があると、新しいSkycoinアドレスを生成し、そのアドレスにコインを引き出すことができます。
ゲートウェイは小額のコインのみを処理しています。

Skycoinプロトコルのゲートウェイは、サードパーティのためにコインやアカウント残高を保持する任意のサーバーです。
ゲートウェイはデポジット機関であり、独自のプロトコルとAPIを備えています。

**最終的に、**

- 複数のゲートウェイとクロスゲートウェイのコイン転送があります。
これらの取引はプライベートで発生し、コインをゲートウェイから引き出すまではブロックチェーンに表示されません。
- ゲートウェイを使用したメッセージングは、非同期通信チャネルを介して発生します（各メッセージスチームは新しい匿名識別情報を取得します）。
- ゲートウェイプロトコルの一部はOT実装であり、特定のゲートウェイがコインを盗んでいるかどうかを証明することができます。
あなたはゲートウェイへの各APIコールに署名し、そして、ゲートウェイが実行してアウトプットに署名します。
署名と取引のリンクのチェーンがあるので、あなたの署名を偽造することができなければ、ゲートウェイは硬貨を消滅させることはできません。
コインをどこかに預けて、それが消えた場合、トランザクションログを公開することができます。そして、告発されたノードの所有者は、コインがどこかに送信されることを許可したことを示すログを生成する必要があります。
彼らが署名されたAPI呼び出しを生成できない場合、それは彼らがうそをついているか、不誠実であることを証明します。
- 最終的にやり取りはゲートウェイプロトコルの下で動作します。

ゲートウェイ内の内部残高を公的なブロックチェーンにするというあなたの提案は面白いです。
内部取引を公開された個人的なブロックチェーンに置くことは、ユーザーのプライバシーを維持しながら、やり取りを正直に保つことができると私は思います。
