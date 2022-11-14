---
title: CSS カスケード入門
slug: Web/CSS/Cascade
---

{{CSSRef}}

**カスケード**は、異なるソースから来るプロパティ値を組み合わせる方法を定義するアルゴリズムです。カスケードでは、複数の[オリジン](#オリジンの種類)や[カスケードレイヤー](/ja/docs/Web/CSS/@layer)の宣言が要素のプロパティに値を設定する場合に、何が優先されるかを定義します。

これは*カスケーディング*スタイルシートという名前で強調されているように、 CSS の中心を占めるものです。[セレクター](/ja/docs/Web/CSS/CSS_Selectors)が要素に一致する場合、優先順位の低いオリジンやレイヤーのセレクターがより高い[詳細度](/ja/docs/Web/CSS/Specificity)を持っていたとしても、優先順位の最も高いオリジンのプロパティ値が適用されます。

この記事では、カスケードとは何か、 {{Glossary("CSS")}} の[宣言](/ja/docs/Web/API/CSSStyleDeclaration)をカスケードする順番、そしてウェブ開発者にどのように影響するかを説明します。

## オリジンの種類

CSS カスケードアルゴリズムの役割は、 CSS プロパティの正しい値を決定するために CSS 宣言を選択することです。 CSS 宣言は、**[ユーザーエージェントスタイルシート](#ユーザーエージェントスタイルシート)**、**[作成者スタイルシート](#作成者スタイルシート)**、**[ユーザースタイルシート](#ユーザースタイルシート)** など、さまざまなものに由来します。

スタイルシートはこれらの異なるオリジンから生まれ、これらのオリジンのそれぞれで異なる[レイヤー](/ja/docs/Web/CSS/@layer)にあることがありますが、スコープでは重なり合っています。これを機能させるために、カスケードアルゴリズムはそれらがどのように相互に作用するかを定義しています。相互作用に取り組む前に、いくつかの用語を定義しておきましょう。

### ユーザーエージェントスタイルシート

ブラウザーには、任意の文書に既定のスタイルを提供する基本的なスタイルシートがあります。これらのスタイルシートには**ユーザーエージェントスタイルシート**と呼ばれています。実際のスタイルシートを使用するブラウザーもあれば、コードでシミュレートするブラウザーもありますが、最終的な結果は同じです。

ブラウザーによっては、ユーザーがユーザーエージェントスタイルシートを変更することができます。

ユーザーエージェントスタイルシートに関するいくつかの制約は HTML 仕様書によって設定されていますが、ブラウザーにはまだ多くの自由度があります。つまり、ブラウザーごとに大きな違いがあります。開発プロセスを簡素化するために、ウェブ開発者は多くの場合、 CSS リセットスタイルシート（例えば [normalize.css](https://github.com/necolas/normalize.css)）を使用して、あらゆるブラウザーが特定のニーズに合わせて変更を開始する前に、共通のプロパティ値を既知の状態にします。

ユーザーエージェントスタイルシートがプロパティの隣に [`!important`](/ja/docs/Web/CSS/Specificity#the_!important_exception) を含み、それを "important" にしない限り、リセットスタイルシートを含む作成者スタイルによって宣言されたスタイルは、関連するセレクターの詳細度にかかわらず、ユーザーエージェントスタイルより優先さ れます。

### 作成者スタイルシート

**作成者スタイルシート**はスタイルシートの最も一般的な種類です。これらはウェブ開発者によって書かれたスタイルです。これらのスタイルは、上に述べたように、ユーザーエージェントスタイルをリセットすることができ、与えられたウェブページやアプリケーションのデザインのためのスタイルを定義します。作成者やウェブ開発者は、一つ以上のリンクされた、あるいはインポートされたスタイルシート、 {{HTMLElement('style')}} ブロック、 {{htmlattrxref('style')}} 属性で定義されたインラインスタイルを使って文書用のスタイルを定義しています。これらの作成者スタイルは、ウェブサイトのルック＆フィール（テーマ）を定義します。

### ユーザースタイルシート

ほとんどのブラウザーでは、ウェブサイトのユーザー（または読者）は、ユーザーの希望に合わせた使い勝手を提供するために設計された独自の**ユーザースタイルシート**を使用して、スタイルを上書きすることを選択することができます。ユーザーエージェントによっては、[ユーザースタイルを直接設定する](https://www.thoughtco.com/user-style-sheet-3469931)か、ブラウザー拡張機能を介して追加することができます。

### カスケードレイヤー

カスケード順序は、オリジンの種類に基づきます。オリジンタイプ内のカスケードは、その種類の中での[カスケードレイヤー](/ja/docs/Web/CSS/@layer)の宣言順序に基づきます。ユーザーエージェント、作成者、ユーザーのすべてのオリジンについて、スタイルは名前付きまたは無名のレイヤーの内部または外部で宣言することができます。 [`layer`, `layer()`](/ja/docs/Web/CSS/@import) または [`@layer`](/ja/docs/Web/CSS/@layer) を使って宣言した場合、スタイルは指定された名前のレイヤーに、名前が提供されていない場合は無名のレイヤーに配置されます。レイヤーの外側で宣言されたスタイルは、最後に宣言された匿名のレイヤーに属するものとして扱われます。

各オリジンの種類内のカスケードレイヤーの説明に入る前に、カスケードのオリジンの種類について見てみましょう。

## カスケード順

カスケーディングアルゴリズムは、それぞれの文書要素のそれぞれのプロパティに適用する値を見つける方法を決定する。カスケードアルゴリズムには、以下の手順が適用されます。

1. **関連性**: 最初に、さまざまなオリジンからすべてのルールをフィルタリングして、特定の要素に適用されるルールのみを保持します。つまり、セレクターが指定された要素と一致し、適切な `media` アットルールの一部であるルールを意味します。

2. **オリジンと重要度**: 次に、これらのルールをその重要度、つまり、 `!important` が後に続くかどうか、およびそのオリジンに従って並べ替えます。ひとまずレイヤーを無視すると、カスケード順は次のようになります。

    | 順序（低い順）    | オリジン                 | 重要度       |
    | --- | -------------------- | ------------ |
    | 1   | ユーザーエージェント（ブラウザー） | 通常         |
    | 2   | ユーザー        | 通常       |
    | 3   | 作成者（開発者）   | 通常       |
    | 4   | CSS @keyframe アニメーション  |              |
    | 5   | 作成者（開発者）   | `!important` |
    | 6   | ユーザー        | `!important` |
    | 7   | ユーザーエージェント（ブラウザー） | `!important` |
    | 8   | CSS トランジション |              |

3. **詳細度:** あるオリジンと等しい場合、ルールの[詳細度](/ja/docs/Web/CSS/Specificity)を考慮して、ある値を選択することができる。セレクターの詳細度を比較し、最も高い詳細度を持つ宣言が勝利します。
4. **出現順**: 優先順位を持つオリジンでは、ある特性に対して、同等の詳細度を持つセレクターに一致するスタイルブロックに競合する値がある場合、スタイルの順序の最後の宣言が適用されます。

カスケードは昇順で、アニメーションはユーザー、作成者、ユーザーエージェントのいずれのスタイルで宣言されていても通常の値より優先され、重要な値はアニメーションより優先され、トランジションは重要な値より優先されることを意味しています。

> **メモ:** **トランジションとアニメーション**
>
> アニメーションで設定されたプロパティ値 {{cssxref('@keyframes')}} は、すべての通常のスタイル（[`!important`](/ja/docs/Web/CSS/Specificity#the_!important_exception)を設定していないもの）よりも優先されます。
>
> {{cssxref('transition')}} で設定されているプロパティ値は、たとえ `!important` でマークされているものであっても、他のすべての設定値より優先されます。

カスケードアルゴリズムは、詳細度アルゴリズムの前に適用されます。つまり、ユーザースタイルシート（2 行目）で `:root p { color: red;}` が宣言され、作成者スタイルシート（3 行目）でより詳細度の低い `p {color: blue;}` が宣言されると、その段落は青色になるということです。

## 基本的な例

さまざまなオリジンにまたがる CSS の複数のソースを含む例を見てみましょう。カスケードレイヤーがカスケードにどのような影響を与えるかを詳しく見る前に、さまざまなオリジンの CSS の複数のソースを含む例を見て、カスケードアルゴリズムのステップを通して作業してみましょう。

ここには、ユーザーエージェントスタイルシート、 2 つの作成者スタイルシート、ユーザースタイルシート、および HTML 内のインラインスタイルがあります。

**ユーザーエージェント CSS:**

```css
li { margin-left: 10px }
```

**作成者 CSS 1:**

```css
li { margin-left: 0 } /* これは初期化 */
```

**作成者 CSS 2:**

```css
@media screen {
  li { margin-left: 3px }
}

@media print {
  li { margin-left: 1px }
}

@layer namedLayer {
  li {
    margin-left: 5px ;
  }
}
```

**ユーザー CSS:**

```css
.specific { margin-left: 1em }
```

**HTML:**

```html
<ul>
<li class="specific">1<sup>st</sup></li>
<li>2<sup>nd</sup></li>
</ul>
```

この場合、 `li` および `.specific` ルール内の宣言を適用する必要があります。

繰り返しになりますが、カスケードアルゴリズムには 4 つのステップがあり、順に次のようになります。

  1. 関連性
  2. オリジンと重要度
  3. 詳細度
  4. 出現順序

この `1px` は印刷媒体用です。そのメディア種別に基づく_関連性がないため、検討対象から外されています。

`!important`として宣言されている宣言はないため、優先順位は、ユーザースタイルシートまたはユーザーエージェントスタイルシートの前の作成者スタイルシートです。オリジンと重要度_に基づいて、ユーザースタイルシートの `1em` とユーザーエージェントスタイルシートの `10px` は考慮から除外されます。

`1em` の `.specific` にあるユーザースタイルがより高い詳細度をを持っていても、それはユーザースタイルシートの通常の宣言であることに注意してください。そのため、作成者スタイルよりも優先順位が低く、詳細度が考慮される前に、アルゴリズムのオリジンと重要性のステップで削除されます。

ここでは 3 つの宣言が競合しています。

```css
li { margin-left: 0 } /* from author css 1 */
```

```css
@media screen {
  li { margin-left: 3px }
}
```

```css
@layer namedLayer {
  li {
    margin-left: 5px ;
  }
}
```

最後の `5px` はカスケードレイヤーの一部です。レイヤー内の通常の宣言は、同じオリジン種別内のレイヤーにない通常のスタイルよりも優先順位が低くなります。これはアルゴリズムのステップ 2、*オリジンと重要度*によっても取り除かれます。

このため、`0`と`3px`が残り、どちらも同じセレクターを持つので、同じ*詳細度*となります。

次に、*出現順序*を見ます。レイヤーがない 2 つの作成者スタイルのうち、2 番目のスタイルが勝ります。

```css
margin-left: 3px
```

> **メモ:** ユーザー CSS で定義された宣言は、より高い詳細度を持つかもしれませんが、カスケードアルゴリズムのオリジンと重要性が詳細度アルゴリズムの前に適用されるため、選択されることはありません。カスケードレイヤーで定義された宣言は、コードの後半に来るかもしれませんが、カスケードレイヤーの通常のスタイルが通常の非レイヤーのスタイルより優先順位が低いため、優先順位もありません。 出現の順序_は、オリジン、重要度、および詳細度の両方が等しい場合にのみ重要である。

## 作成者スタイル: インラインスタイル、レイヤー、優先度

[カスケード順の表](#カスケード順の表)は、優先順位の概要を 提供していました。この表では、ユーザーエージェント用、ユーザー用、作成者用のオリジン型スタイルをそれぞれ「オリジン型 - normal」と「オリジン型 - !important」で2行にまとめていました。各オリジン種別内の優先順位はもっと細部があります。スタイルはそのオリジン種別のレイヤーに含まれることがあり、作成者スタイルでは、インラインスタイルがカスケード順序のどこに位置するかという問題もあります。

レイヤーを宣言する順序は、優先順位を決定する上で重要です。レイヤー内の通常のスタイルは、前のレイヤーで宣言されたスタイルよりも優先され、どのレイヤーの外側で宣言された通常のスタイルも、仕様書に関係なく通常のレイヤーのスタイルよりも優先されます。

この例では、作成者は CSS の {{CSSXref('@import')}} ルールを使用して、 {{HTMLElement('style')}} 情報要素内に五つの外部スタイルシートをインポートしています。

```html
<style>
  @import unlayeredStyles.css;
  @import AStyles.css layer(A);
  @import moreUnlayeredStyles.css;
  @import BStyles.css layer(B);
  @import CStyles.css layer(C);
  p {
    color: red;
    padding: 1em !important;
  }
</style>
```

そして、文書の本体にはインラインスタイルがあります。

```html
Hello`
```

上記の CSS コードブロックでは、 "A", "B", "C" という名前の 3 つのカスケードレイヤーが順に作成されました。 3 つのスタイルシートはレイヤーに直接インポートされ、 2 つはレイヤーを作成またはレイヤーに割り当てずにインポートされました。
以下の一覧（通常の作成者スタイルの優先順位 - 4 番目）の「すべての非レイヤー化されたスタイル」には、これら 2 つのスタイルシートのスタイルと、追加の非レイヤー化された CSS スタイルブロックのスタイルが含まれています。さらに、 2 つのインラインスタイル、通常の `line-height` 宣言、および重要な `text-decoration` 宣言があります。

|  順序（低い順）    | 作成者スタイル      | 重要度   |
| --- | ----------- | ------------ |
| 1   | A - 最初のレイヤー | 通常       |
| 2   | B - 二番目のレイヤー | 通常       |
| 3  | C - 最後のレイヤー      | 通常       |
| 4   | すべてのレイヤー化されていないスタイル       | 通常       |
| 5   | インラインスタイル `style`        | 通常       |
| 6   | アニメーション  |              |
| 7   | すべてのレイヤー化されていないスタイル      | `!important` |
| 8   | C - 最後のレイヤー   | `!important` |
| 9   |  B - 二番目のレイヤー | `!important`       |
| 10   | A - 最初のレイヤー    | `!important` |
| 11   | インラインスタイル `style`      | `!important` |
| 12   | トランジション |              |

すべてのオリジン種別において、レイヤーに含まれる重要でないスタイルが最も低い優先順位をになります。この例では、最初に宣言されたレイヤー (A) に関連する通常のスタイルは、 2 番目に宣言されたレイヤー (B) の通常のスタイルよりも優先度が低く、それは 3 番目に宣言されたレイヤー (C) の通常のスタイルよりも優先度が低くなっています。これらのレイヤー化されたスタイルは、通常のレイヤー化されていないスタイル（`unlayeredStyles.css`, `moreUnlayeredStyles.css`, および `<style>` 自体の `p` の `color` から得られる通常のスタイル）よりも優先度が低くなります。

A、B、C のレイヤー化されたスタイルのいずれかが、`:root body p { color: black;}` のような、要素に一致する、より高い詳細度を持つセレクターを保有している場合、それは問題ではありません。これらの宣言は_オリジン_のために考慮から外されます。通常のレイヤー化されたスタイルは通常のレイヤー化されていないスタイルよりも優先順位が低くなっています。しかし、より具体的なセレクター `:root body p { color: black;}` が `unlayeredStyles.css` に得られた場合、_origin と importance_ はどちらも同じ優先順位なので、_詳細度_ はより具体的で黒い宣言が勝ると言う意味になります。

レイヤーの優先順位は、`!important`として宣言されたスタイルでは逆転します。あるレイヤーで宣言された重要なスタイルは、レイヤーの外側で宣言された重要なスタイルよりも見なされます。最初に宣言されたレイヤー (A) の重要なスタイルは、レイヤー B で得られる重要な宣言に優先し、レイヤー C に優先し、レイヤー外のスタイルにある重要な宣言に優先します。

### インラインスタイル

作成者スタイルに関係するのは、`style`属性で宣言されたインラインスタイルだけです。通常のインラインスタイルは、セレクターの仕様書に関係なく、他のどの通常の作成者スタイルよりも優先されます。もし、インポートされた 5 つのスタイルシートのどれかの `:root body p` セレクターブロックで `line-height: 2;` が宣言されていたとしても、行の高さは `1.6` のままでしょう。

通常のインラインスタイルは、プロパティが CSS アニメーションによって変更されない限り、他のどの通常の作成者スタイルよりも優先されます。

全ての重要なインラインスタイルは、重要か否か、インラインか否か、レイヤーか否かを問わず、全ての作成者スタイルに優先して見なされます。重要なスタイルはアニメーションするプロパティにも見なされますが、トランジションするプロパティには適用されません。重要なインラインスタイルを上書きできるのは、 1) 重要なユーザースタイル、 2) 重要なユーザーエージェントスタイル、 3) 遷移するプロパティ値の 3 点です。

### 重要度とレイヤー

重要なスタイルでは、オリジン種別の優先順位が逆転します。カスケードレイヤーの外側で宣言された重要なスタイルは、レイヤーの一部として宣言されたスタイルよりも優先順位が低くなります。初期のレイヤーに来る重要な値は、後続のカスケードレイヤーで宣言された重要なスタイルよりも優先されます。

以下の CSS を例に取ってみましょう。

```css
p {
  color: red;
}
@layer B {
  :root p {
    color: blue;
  }
}
```

たとえ赤が最初に宣言され、あまり詳細でないセレクターを持っていても、レイヤーのない CSS は レイヤー化された CSS より優先されるため、段落は赤になります。もし、段落に `<p style="color: black">` のような別の色を設定するインラインスタイルを保有していたら、段落は黒になります。

このCSSの一部に `!important` を追加すると、スタイルシートの優先順位が逆転します。

```css
p {
  color: red !important;
}
@layer B {
  :root p {
    color: blue !important;
  }
}
```

これで段落は青色になります。一番最初に宣言されたレイヤーの `!important` は、それ以降のレイヤーやレイヤーにないimportantの宣言に優先して適用されます。もし、インラインスタイルが、`<p style="color: black !important">`のように !important を含んでいたら、再び、パラグラフは黒になります。インラインのimportantは、仕様書に関係なく、作者が宣言した他のすべての `!important` 宣言より優先されます。

> **メモ:** `!important` は、カスケードレイヤーの優先順位を逆転させます。このため、外部スタイルを上書きするために `!important` を使用するのではなく、フレームワーク、サードパーティーのスタイル、ウィジェットのスタイルシートなどをレイヤーにインポートして、優先順位を下げます。important` は、最初の宣言されたレイヤーで、必要なスタイルを後の上書きから守るために、もし使用することがあるならば、控えめに使用すべきです。

トランジションしているスタイルは、誰が、どのように宣言しても、すべての重要なスタイルに優先して見なされます。

## 完全なカスケード順序

オリジン型とカスケードレイヤーの優先順位をより深く理解した今、[カスケード順](#カスケード順)の表は以下のように表現した方がより正確だったことに気づきました。

<table>
<thead>
  <tr><th>優先順位 <br/>（低い順）</th><th>スタイルのオリジン</th><th>重要性</th></tr>
</thead>
<tbody>
  <tr><td rowspan="3">1</td><td>ユーザーエージェント - 先に宣言されたレイヤー</td><td rowspan="3">通常</td></tr>
  <tr><td>ユーザーエージェント - 後に宣言されたレイヤー</td></tr>
  <tr><td>ユーザーエージェント - レイヤー化されていないスタイル</td></tr>
  <tr><td rowspan="3">2</td><td>ユーザー - 先に宣言されたレイヤー</td><td rowspan="3">通常</td></tr>
  <tr><td>ユーザー - 後に宣言されたレイヤー</td></tr>
  <tr><td>ユーザー - レイヤー化されていないスタイル</td></tr>
  <tr><td rowspan="4">3</td><td>作成者 - 先に宣言されたレイヤー</td><td rowspan="4">通常</td></tr>
  <tr><td>作成者 - 後に宣言されたレイヤー</td></tr>
  <tr><td>作成者 - レイヤー化されていないスタイル</td></tr>
  <tr><td>`style` によるインラインスタイル</td></tr>
  <tr><td>4</td><td>アニメーション</td><td></td></tr>
  <tr><td rowspan="4">5</td><td>作成者 - レイヤー化されていないスタイル</td><td rowspan="4">`!important`</td></tr>
  <tr><td>作成者 - 後に宣言されたレイヤー</td></tr>
  <tr><td>作成者 - 先に宣言されたレイヤー</td></tr>
  <tr><td>`style` によるインラインスタイル</td></tr>
  <tr><td rowspan="3">6</td><td>ユーザー - レイヤー化されていないスタイル</td><td rowspan="3"><code>!important</td></tr>
  <tr><td>ユーザー - 後に宣言されたレイヤー</td></tr>
  <tr><td>ユーザー - 先に宣言されたスタイル</td></tr>
  <tr><td rowspan="3">7</td><td>user-agent  - レイヤー化されていないスタイル</td><td rowspan="3">`!important`</td></tr>
  <tr><td>ユーザーエージェント - 後に宣言されたレイヤー</td></tr>
  <tr><td>ユーザーエージェント - 先に宣言されたスタイル</td></tr>
  <tr><td>8</td><td>トランジション</td><td></td></tr>
</tbody>
</table>

## どの CSS エンティティがカスケードに関係するか

CSS 宣言のみが、つまりプロパティ/値の組だけが、カスケードに加わります。つまり、宣言以外のエンティティを含む[アットルール](/ja/docs/Web/CSS/At-rule)、例えば*記述子*を含む {{ cssxref("@font-face")}} などは、カスケードには加わりません。

ほとんどの場合、アットルールで定義されたプロパティと記述子はカスケードには参加しません。カスケードには、アットルールだけが全体として参加します。例えば、`@font-face`ルールの中では、フォント名は [`font-family`](/ja/docs/Web/CSS/@font-face/font-family) 記述子によって識別されます。同じ記述子を持つ複数の `@font-face` ルールが定義されている場合、全体として最も適切な `@font-face` のみが考慮されます。同じように適切なものが複数ある場合は、アルゴリズムのステップ 1、2、および 4 を使用して `@font-face` 宣言全体が比較されます（アットルールに関しては仕様がありません）。

大部分のアットルール — {{cssxref("@media")}}、{{cssxref("@document")}}、{{cssxref("@supports")}} の中など — に含まれる宣言はカスケードに加わりますが、 {{cssxref("@keyframes")}} に含まれる宣言は加わりません。 `@font-face` と同様に、アットルール全体のみがカスケードアルゴリズムによって選択されます。

{{cssxref("@keyframes")}} の中の宣言は、カスケードには参加しません。 `font-face` と同様に、カスケードアルゴリズムによって選択されるのは全体としての `@keyframes` だけです。[アニメーションの優先順位は以下に記述します](#css_アニメーションとカスケード)。

{{cssxref("@import")}} に関しては、 `@import` はカスケードにそれ自身は参加しませんが、インポートされたスタイルは全てに参加します。もし `@import` が[名前付きまたは無名のレイヤー](/ja/docs/Web/CSS/@layer)を定義していれば、インポートされたスタイルシートのコンテンツはその指定されたレイヤーに所有されます。 `@import` でインポートされた他のすべての CSS は、最後に宣言されたレイヤーとして扱われるものです。これは上で説明したとおりです。

最後に、 {{cssxref("@charset")}} は特定のアルゴリズムに従っているため、カスケードアルゴリズムには影響を与えないことに注意してください。

## CSS アニメーションとカスケード

[CSS アニメーション](/ja/docs/Web/CSS/CSS_Animations)は、 {{cssxref("@keyframes")}} アットルールを使用して、状態間のアニメーションを定義します。キーフレームはカスケードされません。つまり、 CSS は常に単一の {{cssxref("@keyframes")}} から値を取得し、複数の値を混在させることはありません。

複数のキーフレームアニメーションが同じアニメーション名で定義されている場合、最後に定義された `@keyframes` がオリジンとレイヤーで最も優先されます。たとえ、 `@keyframes` が異なるプロパティをアニメーションさせる場合でも、 1 つの `@keyframes` 定義だけが使用されます。同じ名前の `@keyframes` は決して結合されません。

```css
p {
  animation: infinite 5s alternate repeatedName;
}
@keyframes repeatedName {
  from {font-size: 1rem;}
  to {font-size: 3rem;}
}

@layer A {
  @keyframes repeatedName {
    from {background-color: yellow;}
    to {background-color: orange;}
  }
}
@layer B {
  @keyframes repeatedName {
    from {color: white;}
    to {color: black;}
  }
}
```

この例では、 `repeatedName` という名前の別個のアニメーション宣言が 3 つ存在します。 `animation: infinite 5s alternate repeatedName` が段落に適用されると、 1 つのアニメーションだけが適用されます。レイヤー化されていない CSS で定義されたキーフレームアニメーションは、オリジンとカスケードレイヤーの優先順位に基づいて、レイヤーのキーフレームアニメーション宣言より優先されます。この例では、要素のフォントサイズのみがアニメーション化されます。

> **メモ:** 重要なアニメーションはありません。 {{cssxref('@keyframes')}} ブロック内のプロパティ宣言で、値の一部として `!important` を含むものは無視されるからです。

## スタイルの初期化

コンテンツがスタイルの変更を終えた後、スタイルを既知の状態に戻す必要が出てくることがあります。これは、アニメーションやテーマの変更などの場合に起こります。 CSS プロパティの {{cssxref("all")}} を使うと、 CSS 内の (ほぼ) すべてのものを既知の状態に素早く戻すことができます。

`all` では、すべてのプロパティを、初期状態 (既定)、カスケードの前のレベルから引き継いだ状態、特定のオリジン (ユーザーエージェントスタイルシート、作成者スタイルシート、ユーザースタイルシート)、さらにはプロパティの値を完全にクリアする状態のいずれかにすぐに戻すことができます。

## 仕様書

{{Specifications}}

## 関連情報

- [とても簡単な CSS カスケードの紹介](/ja/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance)
- CSS の主要概念:
  - [CSS の構文](/ja/docs/Web/CSS/Syntax)
  - [アットルール](/ja/docs/Web/CSS/At-rule)
  - [コメント](/ja/docs/Web/CSS/Comments)
  - [詳細度](/ja/docs/Web/CSS/Specificity)
  - [継承](/ja/docs/Web/CSS/inheritance)
  - [ボックスモデル](/ja/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)
  - [レイアウトモード](/ja/docs/Web/CSS/Layout_mode)
  - [視覚整形モデル](/ja/docs/Web/CSS/Visual_formatting_model)
  - [マージンの相殺](/ja/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
  - 値
    - [初期値](/ja/docs/Web/CSS/initial_value)
    - [計算値](/ja/docs/Web/CSS/computed_value)
    - [使用値](/ja/docs/Web/CSS/used_value)
    - [実効値](/ja/docs/Web/CSS/actual_value)
  - [値の定義構文](/ja/docs/Web/CSS/Value_definition_syntax)
  - [一括指定プロパティ](/ja/docs/Web/CSS/Shorthand_properties)
  - [置換要素](/ja/docs/Web/CSS/Replaced_element)