[⇒README in English](README-en.md)

# gs2-news-sample

GS2-News でのお知らせ配信に使用するコンテンツのサンプルです。

## はじめに

GS2-News で配信する記事は [Hugo](https://gohugo.io/) を使用して管理します。
Hugo によるコンテンツ作成には HTML による記述と Markdown による記述の2種類の方法があります。

装飾やマークアップはテンプレートに任せて、記事のコンテンツ内容は Markdown を使用して記述することをお勧めします。
そうすることで、デザインの変更が容易になり、記事データが本質的な内容のみになることで管理が容易になります。

Hugo は非常に高速にページをビルドできます。マシンスペックにもよりますが本サンプルの内容であれば 10～100ms でビルドできます。
この高速性を利用して、GS2-News は GS2-Schedule のイベント期間と連動して記事を表示非表示するようなコンテンツがあったとしても
事前にすべてのパターンをビルドし、デプロイしておきます。
あとは GS2-Schedule のイベント開催状況に応じて適切なURLへのアクセス権をクライアントに付与することで高速かつ安全にコンテンツを配信します。

## 動作確認方法

Hugo のインストールや実行バイナリのダウンロードは  [Hugo公式サイト](https://gohugo.io/) を参考にしてください。

ダウンロードしたテンプレートディレクトリのルートで

```shell script
hugo server
```

を実行することで、ローカルサーバが起動します。
コマンドを実行した実行結果にアクセスするためのURLが表示されますので、ブラウザからアクセスしてください。
コンテンツが表示されればローカルサーバの起動は成功です。

Hugo はホットリロードに対応しており、ローカルサーバを起動した状態で記事データを更新すると
ローカルサーバを再起動したりすることなくコンテンツの再ビルドが実行され、ブラウザに更新結果が反映されます。

## ディレクトリ構成

Hugo は様々な機能を有していますが、ここでは GS2-News でお知らせ記事を配信するために必要となる最低限の知識を得るための解説にとどめます。
詳しく知りたい場合は、[Hugo公式サイト](https://gohugo.io/) のドキュメントを参照してください。

### 記事データ

`/content` にすべての記事データが格納されています。
`/content` の直下には記事のセクション（カテゴリ）を表す階層が存在します。

本サンプルでは
- news
- events
- maintenance
の3種類のセクションを用意しています。

そのさらに下の階層にはコンテンツ（記事データ）を配置するディレクトリがあります。
Hugo 本来の仕様ではセクション直下にコンテンツを配置することもできますが、
GS2-News は公開条件を満たさない記事を削除する際にディレクトリを削除するだけでよくなるよう
コンテンツごとに1階層ディレクトリを掘り下げることを `仕様` としています。

コンテンツディレクトリ以下には複数のページデータを配置することもできますし、
画像などのデータを配置して、ページから参照することもできます。

サンプル内では `news/released` で記事内に画像を配置したり、ページ専用のカスタムCSSを適用する実装例があります。

Markdown の記法については [Supported Content Formats | Hugo](https://gohugo.io/content-management/formats/) を参照してください。

### Front Matter

Hugo でビルドするコンテンツはデータの先頭に Front Matter というメタデータを記述する必要があります。
本来、Front Matter は TOML/YAML/JSON の3種類のフォーマットで記述できますが、GS2-News は YAML で記述することを `仕様` としています。

```yaml
title: "記事の見出し"
date: 2019-11-07T15:20:29+09:00
```

GS2-News は title と date の指定を必須項目としています。
これらは API を用いて記事データを取得する際にも取得できるデータとなっています。

```yaml
x_gs2_scheduleEventId: grn:gs2:{region}:{ownerId}:schedule:schedule-0001:event:event-0001
```

GS2-News は GS2-Schedule でイベント期間のみお知らせを表示することができます。
Front Matter で上記書式で GS2-Schedule のイベントGRNを指定することで、連動するイベントを指定できます。

### お知らせの表示方法

GS2-News はゲーム内でお知らせを表示する方法として2種類の方法を提供しています。
どちらもブラウザを用いて表示することは変わりありませんが、ローカルのデータを参照する方法と、都度サーバから取得する方法の2種類です。

ローカルのデータを表示する場合は、GS2-News から記事全体の ZIPファイルをダウンロードして利用できます。
この方法を使うと、記事データやテンプレートに変更がなければ、前回ダウンロードしたデータをキャッシュとして利用できます。
これは最終的にデータの配信容量を削減する効果が期待でき、それは GS2 の利用料金削減につながります。
現在公開条件を満たしている記事データで構成されたZIPファイルのダウンロードURLは API で取得できます。

都度サーバのデータから取得する場合はローカルデータの生存期間などを考える必要はありません。
そのかわり、GS2-News が配信するお知らせデータのアクセス管理をするのがZIPダウンロード時だけでなく
お知らせページの遷移するたびに行う必要が出てきます。
これに伴う追加費用などはありませんが、これを実現するためにアプリ内で使用するブラウザに Cookie を設定する必要があります。
Cookie で設定するべき内容や、ページのURLは API で取得できます。

### 記事リスト表示

Hugo でコンテンツをビルドすると自動的にコンテンツのリストページが作成されます。

例えば
```yaml
http://localhost:1313/
```
というルートディレクトリにはすべてのセクションのコンテンツのリストが表示されます。
表示順番はコンテンツの Front Matter に設定された時間でソートされており新しい記事が上に表示されます。

この順番を恣意的に操作したい場合は、Front Matter に weight パラメータを設定することで順番を変更できます。
実際、このサンプルでは `news/released` に古い記事ではあるものの絶対に一番上に表示するよう設定しています。

さらに、各セクションに絞り込んだコンテンツのリストページも

```yaml
http://localhost:1313/news/
http://localhost:1313/events/
http://localhost:1313/maintenance/
```

にそれぞれ作成されます。

### レイアウトやスタイルの変更

記事データのテンプレートは `/layouts` にあります。

#### baseof.html

すべてのページに共通して使用するデザインを定義します。
ヘッダをつけたり、下部にコピーライトを挿入したりするのに使用できます。

#### index.html

すべてのセクションの記事一覧を表示するのに使用します。

#### list.html

記事一覧を表示するのに使用します。

#### single.html

記事の詳細ページを表示するのに使用します。

### 注意

`config.toml` で `baseURL` を指定しないでください。
記事データをビルドする際に GS2-News が自動的に設定します。