---
title: グループと範囲
slug: Web/JavaScript/Guide/Regular_Expressions/Groups_and_Backreferences
original_slug: Web/JavaScript/Guide/Regular_Expressions/Groups_and_Ranges
---

{{jsSidebar("JavaScript Guide")}}

グループと範囲は、式にある文字のグループと範囲を示します。

## 種類

<table class="standard-table">
  <thead>
    <tr>
      <th scope="col">文字</th>
      <th scope="col">意味</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <code><em>x</em>|<em>y</em></code>
      </td>
      <td>
        <p>
          "x" または "y" にマッチします。例えば <code>/green|red/</code> は
          "green apple" の "green" や "red apple" の "red" にマッチします。
        </p>
      </td>
    </tr>
    <tr>
      <td>
        <code>[xyz]<br />[a-c]</code>
      </td>
      <td>
        <p>
          文字集合です。角括弧で囲まれた文字のいずれか 1
          個にマッチします。ハイフンを用いて文字の範囲を指定することも可能ですが、ハイフンが括弧に囲まれた最初または最後の文字に現れている場合、それはハイフンリテラルとみなされて、通常文字として文字集合に含まれます。文字集合には文字クラスを含めることができます。
        </p>
        <p>
          例えば <code>[abcd]</code> は <code>[a-d]</code> と同じです。これは
          "brisket" の "b" や "chop" の "c" にマッチします。
        </p>
        <p>
          例えば、<code>[abcd-]</code> と <code>[-abcd]</code> は "brisket" の
          "b" と "chop" の "c" と "non-profit" の "-"
          （ハイフン）とマッチします。
        </p>
        <p>
          例えば、<code>[\w-]</code> は
          <code>[A-Za-z0-9_-]</code> と同じです。これは "brisket" の "b" や
          "chop" の "c" 、"non-profit" の "n" にマッチします。
        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p>
          <code>[^xyz]<br />[^a-c]</code>
        </p>
      </td>
      <td>
        <p>
          文字集合の否定または補集合です。角括弧で囲まれた文字ではない文字にマッチします。ハイフンを用いて文字の範囲を指定することも可能ですが、ハイフンが括弧に囲まれた最初または最後の文字に現れている場合、それはハイフンリテラルとみなされて、通常文字として文字集合に含まれます。例えば
          <code>[^abc]</code> は <code>[^a-c]</code> と同じです。これは "bacon"
          の 'o' や "chop" の 'h' にマッチします。
        </p>
        <div class="blockIndicator note">
          <p>
            ^ 文字は<a
              href="/ja/docs/Web/JavaScript/Guide/Regular_Expressions/Boundaries"
              >入力の先頭</a
            >を示すこともできます。
          </p>
        </div>
      </td>
    </tr>
    <tr>
      <td><code>(<em>x</em>)</code></td>
      <td>
        <p>
          <strong>キャプチャグループ：</strong>
          <code><em>x</em></code> にマッチし、マッチした内容を記憶します。例えば
          <code>/(foo)/</code> は "foo bar" の "foo" にマッチし、記憶します。
        </p>
        <p>
          正規表現は複数のキャプチャグループを持つことができます。結果、一般的にキャプチャグループ内の左括弧と同じ順にある、配列の要素のキャプチャグループに一致しています。たいていの場合、これはキャプチャグループ自身の順番です。これはキャプチャグループがネストしている場合に重要です。マッチは結果の要素のインデックス
          (<code>[1], ..., [n]</code>) や、あらかじめ定義されている
          <code>RegExp</code> オブジェクトのプロパティ (<code>$1, ..., $9</code
          >) を使ってアクセスできます。
        </p>
        <p>
          キャプチャグループはパフォーマンスペナルティがあります。マッチした部分文字列を使わない場合はキャプチャしない括弧（後述）を使ったほうがいいでしょう。
        </p>
        <p>
          全体マッチ (<code>/.../g</code>)
          がある場合、{{JSxRef("String.match()")}}
          はグループを返せません。ですが、
          {{JSxRef("String.matchAll()")}}
          によってすべてのマッチを取得できます。
        </p>
      </td>
    </tr>
    <tr>
      <td>
        <code>\<em>n</em></code>
      </td>
      <td>
        <p>
          <code><em>n</em></code> に正の整数が入ります。正規表現内において n
          番目の括弧の部分にマッチした最新の部分文字列への後方参照となります（括弧の数は左からカウントします）。例えば
          <code>/apple(,)\sorange\1/</code> は "apple, orange, cherry, peach" の
          "apple, orange," にマッチします。A complete example follows this
          table.
        </p>
      </td>
    </tr>
    <tr>
      <td><code>(?&#x3C;Name>x)</code></td>
      <td>
        <p>
          <strong>名前付きキャプチャグループ：</strong>
          <code>x</code> にマッチし、<code>&#x3C;Name></code>
          で指定された名前に従い、返されるマッチの
          <code>groups</code> プロパティに記憶されます。三角括弧
          ('<code>&#x3C;</code>' と '<code>></code>') にはグループ名が必須です。
        </p>
        <p>
          例えば、電話番号からアメリカのエリアコードを取り出す際、<code
            >/\((?&#x3C;area>\d\d\d)\)/</code
          >
          を使うことができます。 結果の番号は
          <code>matches.groups.area</code> に表示されます。
        </p>
      </td>
    </tr>
    <tr>
      <td><code>(?:<em>x</em>)</code></td>
      <td>
        <p>
          <strong>非キャプチャグループ：</strong>
          <em><code>x</code></em>
          にマッチしますが、マッチした内容は記憶しません。マッチの部分文字列は、結果の配列の要素
          (<code>[1], ..., [n]</code>) や、あらかじめ定義されている
          <code>RegExp</code> オブジェクトのプロパティ (<code>$1, ..., $9</code
          >) から呼び出すことはできません。.
        </p>
      </td>
    </tr>
  </tbody>
</table>

## 例

### 母音を数える

```js
var aliceExcerpt = "There was a long silence after this, and Alice could only hear whispers now and then.";
var regexpVowels = /[aeiouy]/g;

console.log("母音の数:", aliceExcerpt.match(regexpVowels).length);
// 母音の数: 25
```

### グループの使い方

```js
let personList = `First_Name: John, Last_Name: Doe
First_Name: Jane, Last_Name: Smith`;

let regexpNames =  /First_Name: (\w+), Last_Name: (\w+)/mg;
let match = regexpNames.exec(personList);
do {
  console.log(`Hello ${match[1]} ${match[2]}`);
} while((match = regexpNames.exec(personList)) !== null);
```

### 名前付きグループの使い方

```js
let personList = `First_Name: John, Last_Name: Doe
First_Name: Jane, Last_Name: Smith`;

let regexpNames =  /First_Name: (?<firstname>\w+), Last_Name: (?<lastname>\w+)/mg;
let match = regexpNames.exec(personList);
do {
  console.log(`Hello ${match.groups.firstname} ${match.groups.lastname}`);
} while((match = regexpNames.exec(personList)) !== null);
```

> **メモ:** すべてのブラウザがこの機能をサポートしているわけではありません。以下の互換性の表を参照してください。

## 仕様

| 仕様                                                                             | 策定状況                     | コメント |
| -------------------------------------------------------------------------------- | ---------------------------- | -------- |
| {{SpecName('ESDraft', '#sec-classranges', 'RegExp: Ranges')}} | {{Spec2('ESDraft')}} |          |

## ブラウザサポート

{{Compat("javascript.builtins.RegExp.groups_ranges")}}

## 関連情報

- [正規表現](/ja/docs/Web/JavaScript/Guide/Regular_Expressions)
- [`RegExp()` コンストラクタ](/ja/docs/Web/JavaScript/Reference/Global_Objects/RegExp)