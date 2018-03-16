---

copyright:
  years: 2015, 2017
lastupdated: "2017-01-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 拡大するようにデータをモデル化する際のトップ 5 のヒント

この記事では、大きな規模で効率よく機能するようにアプリケーションのデータをモデル化することの細かい点について検討します。
{:shortdesc}

_(このガイドは、2013 年 12 月 17 日に公開された Mike Rhodes の[「My top 5 tips for modelling your data to scale」![外部リンク・アイコン](../images/launch-glyph.svg "外部リンク・アイコン")](https://cloudant.com/blog/my-top-5-tips-for-modelling-your-data-to-scale/){:new_window}というブログ記事に基づいています。)_

Cloudant でのデータのモデリング方法は、どのようにアプリケーションを拡大できるかということに大きな影響を与えます。基礎となるデータ・モデルは、リレーショナル・モデルと大きく異なっています。そして、この特徴を無視すると、いずれパフォーマンスの問題が発生する可能性があります。

常に、成功したモデリングには、使いやすさと達成したいパフォーマンス特性との間のバランスの実現が伴います。

では、早速本題に入りましょう。

## 不変データについて考える

同じ 1 つの状態を 1 秒間に 1 回以上の速さで変更している場合は、文書を不変にすることを検討してください。こうすることにより、競合文書を作成する可能性が大幅に減少します。

逆に、ある特定の文書を 10 秒間に 1 回未満の速さで更新している場合は、「インプレース更新」データ・モデル (つまり既存の文書の更新) によりアプリケーション・コードは大幅に単純化されます。

通常、不変データに基づいたデータ・モデルでは、現在の状態を構成する文書を要約するためにビューの使用が必要になります。ビューは事前計算済みのため、これによってアプリケーションのパフォーマンスに悪影響が及ぶことはありません。

## なぜこれが役に立つか

`https://<account>.cloudant.com/` インターフェースの背後には分散データベースがあります。クラスター内では、文書は多数のシャードに入れられ、それらのシャードが集合的にデータベースを形成します。次にそれらのシャードは、クラスター内の複数のノードに分散されます。これにより、何テラバイトものサイズのデータベースをサポートすることが可能になります。

デフォルトで、データベースを複数のシャードに分割することに加え、すべてのシャードは 3 つのコピー (すなわちシャード・レプリカ) を持っています。それらはそれぞれ、データベース・クラスターの別々のノード上に常駐しています。これにより、1 台のノードで障害が発生しても、データベースは要求に応答し続けることができます。したがって、文書の保存には、3 台のノードへの書き込みが伴います。つまり、同一文書に対して 2 つの更新が同時に行われた場合、あるノード・サブセットが最初の更新を受け入れ、別のサブセットが 2 番目の更新を受け入れるということが可能になります。クラスターはこの矛盾を検出すると、競合が作成された時に、通常の複製が並行更新に対して行うのと同じ方法でそれらの文書を結合します。

競合する文書はパフォーマンスを損ないます。これが起こる理由について以下で詳しく説明します。並行性が高い「インプレース更新」パターンでは、`_rev` パラメーターが予期したものでないために書き込みが拒否される可能性も高くなります。それにより、アプリケーションは再試行を強制され、処理が遅延します。

私たちは、この競合する文書シナリオが、1 秒間に 1 回を超える更新で発生する可能性が大幅に高くなることを発見しましたが、念のために、10 秒間に 1 回を超える更新に対して不変文書を推奨します。

## 検索索引としてではなく結果の事前計算にビューを使用する

見せ掛けの検索索引 (「すべての `person` 文書の取得」) としてビューを使用するのではなく、自分の代わりにデータベースに仕事をさせるようにします。例えば、1 万個の個人文書をすべて検索し、それらの結合した作業時間を計算するのではなく、`_sum` 組み込み reduce 関数を使用して、年、月、日、半日、および時間ごとにこれを事前計算するための複合キーを持つビューを使用します。アプリケーションでの作業を省くことができ、データベースには、1 つの大きな要求に応答するためにディスクから大量のデータを読み取るのではなく、多くの小さい要求を処理することに集中させることができます。

## なぜこれが役に立つか

非常に簡単なことです。まず、map と reduce は両方とも事前計算されることに注目します。つまり、特にディスク上のストレージから何百または何千という膨大な数の文書をストリーミングするために必要な膨大な量の入出力と比べた場合、reduce 関数の結果を要求することは安価な操作です。

さらに詳しく見れば、ノードはビュー要求を受信すると、ビューのデータベースのシャード・レプリカを保持するノードに、各シャード内の文書のビュー要求の結果を要求します。答えを受信する (各シャード・レプリカの最初のものを使用する) と、ビュー要求に応答しているノードは結果を結合し、最終結果をクライアントにストリーム配信します。関連する文書が増えると、各レプリカが、ディスクからネットワークを介して結果をストリーミングするのにかかる時間も長くなります。それに加え、要求に応答しているノードでは、各データベース・シャードからの結果の結合にさらに多くの作業が発生します。

全体としての目標は、ビュー要求に必要な各シャードからのデータ量を最小化し、データの転送中の時間、および最終結果を形成するためにデータを結合する時間を最小化することです。ビューの能力を使用して集約データを事前計算することは、この目標を達成するための 1 つの方法です。これによって明らかに、アプリケーションが要求の完了を待機する時間が削減されます。

## データの非正規化

リレーショナル・データベースで、データの正規化は、多くの場合、最も有効なデータ保管方法です。JOIN を使用して複数のテーブルのデータを容易に結合できる場合、この方法は妥当です。Cloudant では、データごとに HTTP GET 要求が必要な可能性が高くなります。したがって、モデル化されたエンティティーの全体像を構築するために必要な要求の数を減らせば、より早くユーザーに情報を提示できるようになります。

ビューを使用すると、効率性のために非正規化バージョンを維持しつつ、正規化されたデータの利点の多くを活用することができます。

1 つの例として、リレーショナル・スキーマでは、通常、別個のテーブルにタグを示し、接続テーブルを使用して、関連付けられた文書にタグを結合することにより、指定されたタグを持つすべての文書を素早く検索することができます。

Cloudant では、各文書内のリストにタグを保管します。それから、[ビューのマップ関数のキーとして各タグを排出する](../api/creating_views.html)ことにより、ビューを使用して、指定されたタグを持つ文書を取得します。指定したキーをビューで照会すると、そのタグを持つすべての文書が提供されます。

## なぜこれが役に立つか

結局は、アプリケーションが行う HTTP 要求の数ということになります。HTTP 接続、特に HTTPS 接続をオープンするにはコストがかかります。そして、接続の再使用は役に立ちますが、全体として実行する要求の数を減らせば、アプリケーションのデータ処理速度がスピードアップされます。

副効果として、非正規化された文書および事前計算済みビューでは、多くの場合、アプリケーションに必要な値を、照会時に急いで作成するのではなく、事前に生成しておくことができます。

## より細分化された文書を使用して競合を回避する

データを非正規化するというアドバイスと相反しますが、次のアドバイスは、細分化された文書を使用して、同時変更によって競合が生み出される可能性を減らすというものです。これは、ある意味、データの正規化に似ています。HTTP 要求の数を削減することと、競合を回避することの間には、取るべきバランスがあります。

例えば、以下に示す、手術リストを含む医療レコードで考えてみましょう。

```json
{
    "_id": "Joe McIllness",
    "operations": [
        { "surgery": "heart bypass" },
        { "surgery": "lumbar puncture" }
    ]
}
```
{:codeblock}

Joe さんが不運にも多くの手術を同時に受ける場合、前述のように、文書への多くの並行更新により競合文書が作成される可能性が高くなります。この場合は、Joe さんの個人文書を参照する別々の文書にそれらの手術を分割し、ビューを使用して結び付けた方がうまく機能します。それぞれの手術を表すために、以下の 2 つの例のような文書をアップロードします。

```json
{
    "type": "operation",
    "patient": "Joe McIllness",
    "surgery": "heart bypass"
}
```
{:codeblock}

```json
{
    "type": "operation",
    "patient": "Joe McIllness",
    "surgery": "lumbar puncture"
}
```
{:codeblock}

`"patient"` フィールドをビュー内のキーとして排出すると、指定した患者のすべての手術を照会することが可能になります。この場合も、別々の文書からある特定のエンティティーの全体像の作成に役立てるためにビューが使用され、1 つのモデル化されたエンティティーのためにデータを分割したにも関わらず、HTTP 要求の数の削減に役立っています。

## なぜこれが役に立つか

競合文書を回避することは、Cloudant データベースでの多くの操作のスピードアップに役立ちます。この理由は、文書が読み取られるたびに使用される現行の勝利リビジョンを導き出すプロセス (単一文書の検索、`include_docs=true` を指定した呼び出し、ビューの構築など) があるからです。

勝利リビジョンとは、文書のツリー全体からの特定のリビジョンです。Cloudant 上の文書が実際にはリビジョンのツリーであることを思い出してください。任意だが決定論的なアルゴリズムは、文書が要求されると、このツリーの、削除されていないリーフの 1 つを選択して返します。より高位の分岐因子を持つ、より大きいツリーは、ブランチがないか、あっても数が少ない文書ツリーよりも、処理に時間がかかります。勝利リビジョンになる候補かどうかを確認するために、各ブランチをたどる必要があります。それから、潜在的勝利者を互いに比較して最終的な選択を行う必要があります。

明らかに、Cloudant は、ブランチの数が少ない方がうまく処理できます (結局、複製は、データの廃棄を回避するために文書を分岐できるという事実に依存しています)。しかし、異常なレベルに達すると、特に競合が解決されない場合は、文書ツリーの確認に非常に時間がかかり、メモリーが集中することになります。

## 競合解決を組み込む

Cloudant のような結果整合システムでは、最終的に競合は発生します。既に述べたように、これはスケーラビリティーおよびデータ回復力の対価です。

素早く競合を解決し、オペレーター支援を必要としないような方法でデータを構造化することは、データベースを円滑に作動させるうえで役立ちます。ユーザーを巻き込む必要なしに競合を自動的に解決できれば、ユーザーのエクスペリエンスも大幅に向上し、さらにうまくいけば組織のサポート負担の削減にもなります。

これを実現する方法は、非常にアプリケーション固有なものになりますが、以下にいくつかのヒントを示します。

-   可能であれば、複数の文書フィールドにわたる不変式を避ける。これにより、それぞれの競合する文書リビジョンから、変更されたフィールドを取得する単純なマージ操作を使用できる可能性が高くなります。このことは、より単純で堅固なアプリケーション・コードの作成に役立ちます。
-   文書をスタンドアロンになるようにする。正しい解決策を導き出すために他の文書を取得する必要があると、競合解決での待ち時間が増加します。さらに、解決している文書と整合していない他の文書のバージョンを取得し、解決が困難になる可能性もあります。そして、それらの文書が競合していたらとしたら?　

## なぜこれが役に立つか

前述のように、競合が激しい文書では、データベースの負担が大きくなります。競合を解決する機能を最初から組み込んでおくと、異常に競合した文書を回避するうえで非常に役立ちます。

## サマリー

これらのヒントは、モデリング・データがアプリケーションのパフォーマンスに影響を与える方法のいくつかを示しています。Cloudant のデータ・ストアには、アプリケーションの成長につれてデータベースのパフォーマンスも拡大するようにするためのいくつかの固有の特性 (注意すべき特性と利用すべき特性の両方) があります。変化に混乱はつきものです。アドバイスが必要な場合は、いつでもご相談ください。

さらなる情報は、[「data model for Foundbite」![外部リンク・アイコン](../images/launch-glyph.svg "外部リンク・アイコン")](https://cloudant.com/blog/foundbites-data-model-relational-db-vs-nosql-on-cloudant/){:new_window}の議論、または[「example from our friends at Twilio」![外部リンク・アイコン](../images/launch-glyph.svg "外部リンク・アイコン")](https://www.twilio.com/blog/2013/01/building-a-real-time-sms-voting-app-part-3-scaling-node-js-and-couchdb.html){:new_window}を参照してください。
