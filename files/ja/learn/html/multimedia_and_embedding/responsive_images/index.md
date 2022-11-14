---
title: レスポンシブ画像
slug: Learn/HTML/Multimedia_and_embedding/Responsive_images
l10n:
  sourceCommit: c482f34f48d447bfb9ee76b76013dae3b66f45c7
---

{{LearnSidebar}}{{PreviousMenuNext("Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web", "Learn/HTML/Multimedia_and_embedding/Mozilla_splash_page", "Learn/HTML/Multimedia_and_embedding")}}

この記事では、レスポンシブ画像の概念 — 画面サイズ、解像度などの機能が大きく異なる機器で適切に動作する画像 — について学び、 HTML がそれを実装する上でどのような道具を提供しているかを見てみます。 レスポンシブ画像は、レスポンシブウェブデザインの一部にすぎませんが(そしてそれを実現する上で良いステージになりますが)、 [CSS のトピック](/ja/docs/Learn/CSS)の将来のモジュールで多くのことを学ぶトピックです。

<table class="standard-table">
  <tbody>
    <tr>
      <th scope="row">前提条件:</th>
      <td>
        <a href="/ja/docs/Learn/HTML/Introduction_to_HTML">HTML の基本</a>および<a
          href="/ja/docs/Learn/HTML/Multimedia_and_embedding/Images_in_HTML"
          >ウェブページに静止画を追加する方法</a
        >をすでに知っているものとします。
      </td>
    </tr>
    <tr>
      <th scope="row">目的:</th>
      <td>
        {{htmlattrxref("srcset", "img")}} や
        {{htmlelement("picture")}}
        要素のような機能を使って、ウェブサイトにレスポンシブ画像を実装する方法を学ぶこと。
      </td>
    </tr>
  </tbody>
</table>

## なぜレスポンシブ画像なのか？

典型的なシナリオを見てみましょう。 典型的なウェブサイトには、おそらく訪問者の印象を良くするためのヘッダー画像と、おそらくその下にはコンテンツ画像がいくつかあります。 おそらく、ヘッダー画像はヘッダーの幅全体にまたがるようにして、コンテンツ画像はコンテンツ列のどこかに合わせたいと思うでしょう。この簡単な例を見てみましょう。

![広い画面で表示されているサイト例 - ここでは最初の画像はよく、中央で細部まで十分に見えます。](picture-element-wide.png)

これは、ノートパソコンやデスクトップなどの広い画面の端末ではうまく表示できます（[ライブを見て](https://mdn.github.io/learning-area/html/multimedia-and-embedding/responsive-images/not-responsive.html)、 GitHub で[ソースコード](https://github.com/mdn/learning-area/blob/main/html/multimedia-and-embedding/responsive-images/not-responsive.html)を見ることができます）。以下の点を除けば CSS については詳しく説明しません。

- 本文のコンテンツは、最大幅 1200 ピクセルに設定されています。 — その幅を超えるビューポートでは、本体は 1200 ピクセルのままで、利用可能な領域の中で中央に配置されます。それよりも狭いビューポートでは、本文はビューポートの幅の 100 ％になります。
- ヘッダー画像は、ヘッダーの幅がどのような幅であっても、その中心が常にヘッダーの中央に来るように設定されています。 ですから、サイトがより狭い画面で表示されている場合は、画像の中心にある重要なディテール（人物）が常に見え、両端の余分な部分が失われます。高さは 200 ピクセルです。
- コンテンツ画像は、 body 要素が画像より狭くなると、画像があふれるのではなく、常に本文内に収まるように縮小し始めるよう設定されています。

しかし、画面が狭い端末でサイトを見始めると、問題が発生します。下のヘッダーは問題ないように見えますが、モバイル端末では画面の内側へ大きく受け取られ始めています。また、このサイズでは、最初のコンテンツ画像内の 2 人の顔が見えにくくなっています。

![狭い画面で見たサイト例。最初の画像は細部が分かりにくいところまで縮小しています。](non-responsive-narrow.png)

改善策として、狭い画面の内側へ表示される場合は、画像の重要な部分を表示するトリミングした版を表示することが考えられます。タブレットのような画面の内側が中型の機器には、 2 つ目のトリミングされた画像を表示することができます。このように、さまざまなレイアウトで異なるトリミング画像を提供したい場合の一般的な問題は、一般に「**アートディレクション問題**」と呼ばれます。

さらに、小さなモバイル画面で見ている場合、このような大きな画像をページに埋め込む必要はありません。 これは**解像度切り替えの問題**と呼ばれています。 ラスター画像は、[ベクターグラフィック](/ja/docs/Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web)のところで見たように、幅のピクセルの集まりと、高さのピクセルの集まりです。 小さいラスター画像は、元のサイズよりも大きく表示されていると粒状に見え始めます（ベクターグラフィックではそうなりません）。

逆に、意図した大きさよりもはるかに小さい画面に大きな画像を表示する必要はありません。 そうすることで帯域幅を無駄にすることがあります。特に、モバイルユーザーは、小さな画像が端末に表示される時、デスクトップ向けの大きな画像をダウンロードすることによって帯域幅を無駄にしたくありません。 理想的な状況は、複数の解像度が利用可能で、ウェブサイト上のデータにアクセスする端末に応じて適切なサイズを提供することです。

状況をより複雑にしているのが、一部の端末は高い解像度の画面を持ち、きれいに表示するには、期待されるよりも大きい画像を必要としていることです。 これは本質的に同じ問題ですが、少し異なる状況のものです。

ベクター画像はある側面でこれらの問題を解決すると思うかもしれません。 — ファイルサイズが小さくて容易に縮尺変更でき、どこでも利用できるからです。 しかし、すべての種類の画像に適しているわけではありません。 — 単純な図形、パターン、インターフェイス要素などには適していますが、例えば写真のような、ディティールのものをベクターベースの画像で作成すると、とても複雑になります。 JPEG のようなラスター画像形式は、上記の例に見られるような種類の画像により適しています。

この種の問題は、ウェブが最初に登場したとき、90 年代前半から中期の頃には存在しませんでした。 — ウェブをブラウズする唯一の端末はデスクトップとラップトップであったため、ブラウザーの技術者や仕様書の著者は解決策を実装することを考えませんでした。 *レスポンシブ画像技術*は上記のような問題を解決するために最近になって実装され、ブラウザーに様々な画像ファイル、どれも表示するものは同じですが、ピクセル数が異なる様々な画像（解像度の切り替え）、異なる領域の取り方が異なる様々な画像(アートディレクション)を含めることができます。

> **メモ:** この記事で説明している新機能 — {{htmlattrxref("srcset", "img")}}/{{htmlattrxref("sizes", "img")}}/{{htmlelement("picture")}} — は、最近のデスクトップ及びモバイルのブラウザーのすべてが対応しています(Microsoft Edge ブラウザーも含みますが Internet Explorer は含みません)。

## レスポンシブ画像の作り方

この節では、上で説明した二つの問題を踏まえ、 HTML のレスポンシブ機能を使用してそれらを解決する方法を示します。 なお、上記の例のコンテンツエリアに見られるように、この節では HTML の {{htmlelement("img")}} に注目します。 — サイトヘッダーの画像は単なる装飾なので、 CSS 背景画像を使用して実装します。 [CSS はおそらく、 HTML よりもレスポンシブデザインのための優れたツールを持っています](https://cloudfour.com/thinks/responsive-images-101-part-8-css-images/)(英語)ので、これについては将来 CSS のモジュールで説明します。

### 解像度の切り替え: 様々な寸法

それでは、解像度切り替えで解決したい問題は何でしょうか？ 同じ画像コンテンツを、機器に応じて大きくしたり小さくしたりして表示することです。 — これは、例の中の 2 番目のコンテンツ画像の状況です。 標準的な {{htmlelement("img")}} 要素は、伝統的にブラウザーにソースファイルを一つだけしか指定できません。

```html
<img src="elva-fairy-800w.jpg" alt="妖精の衣装を着たエルバ">
```

しかし、複数の追加のソース画像と、ブラウザーが正しいものを選択する助けになるヒントを提供することができる、 2 つの属性 — {{htmlattrxref("srcset", "img")}} 及び {{htmlattrxref("sizes", "img")}} — を使用することができます。 この例は、 GitHub の [responsive.html](https://mdn.github.io/learning-area/html/multimedia-and-embedding/responsive-images/responsive.html) で見ることができます([ソースコード](https://github.com/mdn/learning-area/blob/main/html/multimedia-and-embedding/responsive-images/responsive.html)も参照してください)。

```html
<img srcset="elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
     sizes="(max-width: 600px) 480px,
            800px"
     src="elva-fairy-800w.jpg" alt="妖精の衣装を着たエルバ">
```

`srcset` および `sizes` 属性は複雑に見えますが、上記のように属性値のそれぞれの部分を別々の行に書けば、理解することは難しくありません。 それぞれの値にはコンマ区切りのリストが設定されており、それぞれのリストの部分は 3 つの部分からなっています。 それぞれの中身を見てみましょう。

**`srcset`** は、ブラウザーが選択することができる画像のセットと、それぞれの画像の寸法を定義します。 それぞれのコンマの前に書くものは以下の通りです。

1. 画像ファイル名(`elva-fairy-480w.jpg`)
2. 空白
3. 画像の**幅の内在的なピクセル数** (`480w`) — なお、これは単位に `px` ではなく `w` を使用します。画像の[内在サイズ](/ja/docs/Glossary/Intrinsic_Size)は実際の寸法で、これはコンピューターの画像ファイルを調べると分かります（例えば Mac では Finder で画像を選択して、

    <kbd>Cmd</kbd>

    \+

    <kbd>I</kbd>

    を押すと情報画面が出てきます）。

**`sizes`** は、一連のメディア条件(例えば画面の幅)であり、特定のメディア条件が成立したときに、どの寸法の画像を選択するのが最適かを示します。これらは以前に説明したヒントです。 この場合、それぞれのコンマの前には次のようなものを書きます。

1. **メディア条件** (`(max-width:600px)`) — これについては [CSS の記事](/ja/docs/Learn/CSS)で詳しく学びますが、今はメディア条件とは画面がなる可能性がある状態であるとだけ言っておきましょう。 この場合、「ビューポートの幅が 480 ピクセル以下であるとき」と言っています。
2. 空白
3. メディア条件が成立したときに埋める**スロットの幅**(`480px`)

> **メモ:** スロットの幅は、絶対幅（例: `480px`）ではなく、ビューポートからの相対幅（例: `50vw`）を提供してください - ただし、パーセントは指定できません。最後のスロットの幅にはメディア条件がないことにお気づきでしょうか （これは、どのメディア条件も真でない場合に選択される既定値です）。ブラウザーは最初の一致する条件以降を無視するので、メディア条件をどのように並べるか注意してください。

したがって、これらの属性を使用すると、ブラウザーは次のようになります。

1. その端末の幅を見る。
2. `sizes` リストの中のどのメディア条件が真であるかを確認する。
3. そのメディア照会で与えられたスロットサイズを見る。
4. 選択したスロットサイズに最も近い `srcset` リストで参照される画像を読み込みます。

以上です！ この時点で、480px のビューポート幅を持つブラウザーがページを読み込むと、 (`max-width: 480px`) メディア条件が真となるため、440px スロットが選択されるので、その固有幅 (`480w`) が `440px` に最も近い `elva-fairy-480w.jpg` が読み込まれます。 800px の画像は 128KB ですが、480px の画像は 63KB で 65KB の節約になります。 今、これが多くの写真を持っているページだったと想像してみてください。 この技術を使用することで、モバイルユーザーは多くの帯域幅を節約できます。

> **メモ:** デスクトップのブラウザーでテストする場合、ブラウザーでウィンドウ幅を最も狭く設定したときに狭い画像を読み込むことができない場合は、ビューポートの値を確認してください（ブラウザー の JavaScript コンソールに移動して `document.querySelector('html').clientWidth` と入力すると、おおよその値を確認することができます）。さまざまなブラウザーでのウィンドウ幅の最小値が保有されており、それは思ったよりも広いかもしれません。モバイルブラウザーにテストする場合、 Firefox の `about:debugging` ページのようなツールを使用して、デスクトップの開発者ツールを使用してモバイルに読み込まれたページを検査することができます。
>
> どの画像が読み込まれたかを確認するには、 Firefox の開発者ツールの[ネットワークモニター](https://firefox-source-docs.mozilla.org/devtools-user/network_monitor/index.html)タブを使用することができます。

これらの機能をサポートしていない古いブラウザーはそれを無視し、 {{htmlattrxref("src", "img")}} 属性で参照されている画像を通常の方法で読み込みます。

> **メモ:** ドキュメントの {{htmlelement("head")}} には、 `<meta name="viewport" content="width=device-width">` という行があります。 これは、モバイルブラウザーがウェブページを読み込むために実際のビューポート幅を使用するように強制します。 (一部のモバイルブラウザーでは、ビューポート幅について嘘をつき、大きなビューポート幅でページを読み込み、読み込んだページを縮小するため、レスポンシブ画像やデザインにはあまり役に立ちません。 これについては、今後のモジュールで詳しく説明します。)

### 解像度の切り替え: 同じサイズ、異なる解像度

複数の画面解像度をサポートしていても、誰もが画面上で同じ実世界サイズで画像を見る場合は、`sizes` を指定せずに x 記述子を指定した `srcset` を使用してブラウザーに適切な解像度の画像を選択させることができます — やや簡単な構文です！ これは [srcset-resolutions.html](https://mdn.github.io/learning-area/html/multimedia-and-embedding/responsive-images/srcset-resolutions.html) のようなものです([ソースコード](https://github.com/mdn/learning-area/blob/main/html/multimedia-and-embedding/responsive-images/srcset-resolutions.html)も参照してください)。

```html
<img srcset="elva-fairy-320w.jpg,
             elva-fairy-480w.jpg 1.5x,
             elva-fairy-640w.jpg 2x"
     src="elva-fairy-640w.jpg" alt="妖精の衣装を着たエルバ">
```

![妖精の衣装を着た小さな女の子の絵、古いカメラのフィルム効果が画像に適用される](resolution-example.png)この例では、次の CSS が画像に適用され、画面上に 320 ピクセルの幅が設定されます(CSS ピクセルとも呼ばれます)。

```css
img {
  width: 320px;
}
```

この場合、`sizes` は必要ありません。ブラウザーは、表示されている画面がどの解像度であるかを単純に調べ、`srcset` で参照される最も適切な画像を提供します。 したがって、ページにアクセスする端末が標準解像度や低解像度の画面を持っていて、1 つの端末ピクセルが各 CSS ピクセルを表している場合、`elva-fairy-320w.jpg` の画像が読み込まれます(1x は暗黙のため、含める必要はありません)。 端末が CSS ピクセルあたり 2 つの端末ピクセルの高解像度を持つ場合、`elva-fairy-640w.jpg` の画像が読み込まれます。 640px の画像は 93KB ですが、320px の画像は 39KB です。

### アートディレクション

要約すると、**アートディレクションの問題**は、表示される画像を異なる画像表示サイズに合うように変更したいことを含みます。 たとえば、デスクトップブラウザーで見たときに中央に人がいる大きな風景をウェブサイトに表示している場合、モバイルブラウザーでウェブサイトを表示すると縮小され、人がとても小さくて見えにくいので格好悪くなります。 モバイルでは、人にズームインしたより小型のポートレイト画像を表示するほうがよいでしょう。 {{htmlelement("picture")}} 要素は、この種の解決法を実装することを可能にします。

オリジナルの [not-responsive.html](https://mdn.github.io/learning-area/html/multimedia-and-embedding/responsive-images/not-responsive.html) の例に戻ると、アートディレクションがひどく必要な画像があります。

```html
<img src="elva-800w.jpg" alt="クリスは立って彼の娘エルバを抱えています">
```

{{htmlelement("picture")}} でこれを修正しましょう！ [`<video>`や`<audio>`](/ja/docs/Learn/HTML/Multimedia_and_embedding/Video_and_audio_content)と同様に、`<picture>` 要素はラッパーで、ブラウザーが選択できるいくつかの異なるソースを提供するいくつかの {{htmlelement("source")}} 要素を含み、次に極めて重要な {{htmlelement("img")}} 要素が続きます。 [responsive.html](https://mdn.github.io/learning-area/html/multimedia-and-embedding/responsive-images/responsive.html) のコードは次のようになります。

```html
<picture>
  <source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
  <source media="(min-width: 800px)" srcset="elva-800w.jpg">
  <img src="elva-800w.jpg" alt="クリスは立って彼の娘エルバを抱えています">
</picture>
```

- `<source>` 要素にはメディア条件が含まれている `media` 属性が含まれています。 最初の srcset の例のように、これらの条件はどの画像が表示されるかを決定するテストであり、最初に真を返すものが表示されます。 この場合、ビューポート幅が 799 ピクセル幅以下の場合、最初の `<source>` 要素の画像が表示されます。 ビューポート幅が 800 ピクセル以上であれば、2 番目のものになります。
- `srcset` 属性には、表示する画像へのパスが含まれます。 上記の `<img>` で見たように、`<source>` は複数の画像を参照する `srcset` 属性と `sizes` 属性をとることができます。 したがって、`<picture>` 要素で複数の画像を提供することもできますが、それぞれの画像も複数の解像度で提供することができます。 現実的には、この種のことを非常に頻繁にやりたいとは思わないでしょう。
- すべての場合で、`<img>` 要素に `src` と `alt` を付けて `</picture>` の直前に置く必要があります。 そうしないと画像は表示されません。 これはメディア条件のどれも true を返さない場合(この例では 2 番目の `<source>`要素を実際に削除できる)や、`<picture>` 要素をサポートしないブラウザーの代替に適用されるデフォルトのケースを提供します。

このコードでは、以下に示すように、広い画面と狭い画面の両方に適切な画像を表示することができます。

![広い画面で表示された私たちのサンプルサイト - ここでは最初の画像は大丈夫です。中央のディティールを見るのに十分です。](picture-element-wide.png)![最初の画像をポートレートに切り替えるために使用されるpicture要素を使用した狭い画面で表示されたサンプルサイトは、ディティールをクローズアップし、狭い画面でより便利になりました](picture-element-narrow.png)

> **メモ:** `media` 属性は、アートディレクションのシナリオでのみ使用してください。 `media` を使用する場合は、`sizes` 属性内でメディア条件も指定しないでください。

### なぜ CSS や JavaScript を使ってこれを行うことができないのですか？

ブラウザーがページの読み込みを開始すると、メインのパーサーがページの CSS と JavaScript を読み込んで解釈する前に、画像のダウンロード(先読み)を開始します。 これは便利なテクニックで、平均してページ読み込み時間の 20％ を削減します。 しかし、レスポンシブ画像では役に立ちません。 そのため、`srcset` のような解決法を実装する必要があります。 たとえば、 {{htmlelement("img")}} 要素を読み込んでから JavaScript でビューポートの幅を検出し、必要に応じて元の画像をより小さなものに動的に変更することはできません。 それまでには、元の画像が既に読み込まれていて、さらに小さい画像も読み込むことになります。 これは、レスポンシブ画像の条件ではさらに悪化します。

### 新しい像フォーマットを大胆に使用する

小さなファイルサイズと高品質を同時に維持できる、エキサイティングな新しい画像フォーマット（[WebP](/ja/docs/Web/Media/Formats/Image_types#webp_画像) や [AVIF](/ja/docs/Web/Media/Formats/Image_types#avif_画像) など）がいくつかあります。 しかし、ブラウザーの対応にはむらがあります。

`<picture>` は、古いブラウザーにも対応しています。 MIME タイプを `type` 属性内に指定すると、ブラウザーは対応していないファイルタイプを直ちに拒否できます。

```html
<picture>
  <source type="image/svg+xml" srcset="pyramid.svg">
  <source type="image/webp" srcset="pyramid.webp">
  <img src="pyramid.png" alt="4つの正三角形から構築された通常のピラミッド">
</picture>
```

- アートディレクションが必要な場合を除き、`media` 属性を使用しないでください。
- `<source>` 要素では、`type` で宣言された型の画像のみを参照できます。
- 前と同じように、必要に応じて、`srcset` と `sizes` をカンマで区切ったリストを使用することもできます。

## アクティブラーニング: あなた自身のレスポンシブ画像の実装

このアクティブラーニングでは、私たちはあなたが勇敢に(ほとんど)一人で行くことを期待しています。 `<picture>` を使って、あなた自身の適切なアートディレクションの狭い画面や広い画面と、`srcset` を使用する解像度切り替えの例を実装してください。

1. あなたのコードを格納するためのシンプルな HTML を書く(必要に応じて `not-responsive.html` を出発点として使用する)
2. どこかから細かいディテールが入った素晴らしい広い画面のランドスケープ画像を探してきましょう。 グラフィックスエディターを使用してウェブサイズの画像を作成し、それをトリミングしてディテールを拡大した小さな部分を表示し、2 番目の画像を作成します(約 480px の幅がこれに適しています)。
3. `<picture>` 要素を使用して、アートディレクション画像切り替えを実装します。
4. サイズの異なる複数の画像ファイルを作成し、それぞれが同じ画像を表示します。
5. `srcset` や `sizes` を使用して、異なる解像度で同じサイズの画像を提供するか、異なるビューポートの幅で異なる画像サイズを提供するかのいずれかの解像度切り替えの例を作成します。

## まとめ

それはレスポンシブ画像のラップです — あなたはこれらの新しいテクニックで遊ぶことを楽しんでください。 要約すると、私たちがここで議論してきた 2 つの明確な問題があります。

- **アートディレクション**: 異なるレイアウトのためにトリミングされた画像を提供したいという問題 — 例えば、デスクトップレイアウトのフルシーンを示すランドスケープ画像や、モバイルレイアウトのためにズームインした主な被写体を示すポートレート画像などがあります。 これは、 {{htmlelement("picture")}} 要素を使用して解決できます。
- **解像度切り替え**: デスクトップ画面のような巨大な画像を必要としないため、小さな画像ファイルを狭い画面の端末に配信したいという問題 — 必要に応じて異なる解像度の画像を高密度や低密度の画面に表示したい場合もあります。 これは、[ベクターグラフィックス](/ja/docs/Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web)(SVG 画像)と {{htmlattrxref("srcset", "img")}} と {{htmlattrxref("sizes", "img")}} 属性を使用して解決できます。

これはまた、[マルチメディアと埋め込み](/ja/docs/Learn/HTML/Multimedia_and_embedding)のモジュール全体を閉じます！ 先に進む前に行うべき唯一のことは、[マルチメディアと埋め込みの評価課題](/ja/docs/Learn/HTML/Multimedia_and_embedding/Mozilla_splash_page)に挑戦して、結果を見てみることです。楽しんでください。

## 関連情報

- [Jason Grigsby のレスポンシブ画像の優れた紹介](http://blog.cloudfour.com/responsive-images-101-definitions)(英語)
- [レスポンシブ画像: 解像度を変更するだけの場合は、srcset を使う](https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/) — ブラウザーがどの画像を使用するかの詳細な説明が含まれています(英語)
- {{htmlelement("img")}}
- {{htmlelement("picture")}}
- {{htmlelement("source")}}

{{PreviousMenuNext("Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web", "Learn/HTML/Multimedia_and_embedding/Mozilla_splash_page", "Learn/HTML/Multimedia_and_embedding")}}

## このモジュール

- [HTML の画像](/ja/docs/Learn/HTML/Multimedia_and_embedding/Images_in_HTML)
- [動画と音声のコンテンツ](/ja/docs/Learn/HTML/Multimedia_and_embedding/Video_and_audio_content)
- [\<object> から \<iframe> まで — その他の埋め込み技術](/ja/docs/Learn/HTML/Multimedia_and_embedding/Other_embedding_technologies)
- [ウェブにベクターグラフィックを追加する](/ja/docs/Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web)
- [レスポンシブ画像](/ja/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)
- [Mozilla のスプラッシュページ](/ja/docs/Learn/HTML/Multimedia_and_embedding/Mozilla_splash_page)