---
title: "アクセシビリティを考慮したHTMLコーディング"
emoji: "🙆‍♀️"
type: "tech"
topics:
  - "css"
  - "html"
  - "javascript"
published: true
published_at: "2023-08-21 15:06"
---

## 概要

Web アクセシビリティを考慮したコーディングをするための個人的備忘録。

## `img`タグ

### alt 属性

`img`タグには alt 属性を設定する。

:::message

基本的には画像の内容を alt 属性に設定する
意味をもたない画像の場合は空の文字列で OK（省略はしない）

:::

alt 属性の内容についてはこちらの記事が参考になる。
https://web-de-asobo.net/2023/03/14/html-img-alt/

## WAI-ARIA

WAI-ARIA には、ロール（Role）、プロパティ（Property）、ステート（State）の 3 つの機能があり、MDN にわかりやすくまとめられている。
https://developer.mozilla.org/ja/docs/Learn/Accessibility/WAI-ARIA_basics#wai-aria_%E3%81%A3%E3%81%A6%E4%BD%95%EF%BC%9F

### `role`属性

`role`とは日本語で「役割」を意味する。
`role`属性を付与することで Web ページに「ボタン」など、要素の役割を認識してもらうようにすることができる。

ただ、すべての要素には元から対応している暗黙のロールというものがあり、**暗黙的にもっているロールを敢えて `role 属性`で指定することは非推奨**とされている。（`button`に`role="button"`は設定しない、など）

また、要素によって設定できるものとできないものもある。詳細については、次の記事が参考になる。

https://zenn.dev/yusukehirao/articles/e3512a58df58fd

https://qiita.com/s-kato/items/fc5df9955698222f5436

### `aria-label`属性

`aria-label`に指定した値がスクリーンリーダーにそのまま読み上げられる。
`aria-label`は次のような要素に指定する。

- ページトップボタン
ボタンを押すと Web サイトの最上部まで戻るボタンには`aria-label`属性をつけてスクリーンリーダーに適切に読み上げられるようにする。

```html:index.html
<a href="#" aria-label="ページ上部へ戻る">
 <!--- 省略 -->
</a>
```

- ハンバーガーメニュー
一般的にはグローバルナビゲーションの役割を持つハンバーガーメニューだが、スクリーンリーダーにはどのようなボタンかが伝わらない。

```html:index.html
<button type="button" aria-label="メニューを開く">
 <!--- 省略 -->
</a>
```

### `aria-hidden`属性

スクリーンリーダーに無視してもらいたい要素につける属性。

例えば、インライン SVG で SNS アイコンを実装するときは装飾画像として扱うため、`aria-hidden="true"`にして、スクリーンリーダ用のテキストを設置する。

```html
<svg aria-hidden="true">
  <!--- 省略 -->
</svg>
<span class="sr-only">画像の説明</span>
```

### `aria-current`属性

スクリーンリーダーなどの支援技術で、現在の位置や状態を把握しやすくするために使用される。

#### `aria-current="page"`

ナビゲーションメニューの現在の項目を示す。
次の例は、`aria-current="page"`が設定された About リンクが現在の位置を示している。
`aria-current="page"`を指定した要素は「現在のページ」と読み上げられる。

```html
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about" aria-current="page">About</a></li>
    <li><a href="/contact">Contact</a></li>
  </ul>
</nav>
```

#### `aria-current="step"`

マルチステップフォームなどで使用する。
次の例では、aria-current="step"が設定された最初の`<li>`要素がプロセス内の現在のステップを表す。

```html
<ul>
  <li aria-current="step">Step 1: Fill out your personal information</li>
  <li>Step 2: Choose a payment method</li>
  <li>Step 3: Review and submit</li>
</ul>
```

#### `aria-current="true"`

コンテンツ内で重要な項目やアイテムを示す。
次の例では、タブインターフェイスにおける現在のタブを示しており、`aria-current="true"`が設定された Tab 2 ボタンが現在のタブを示している。

```html
<div role="tablist">
  <button role="tab" aria-selected="true" aria-controls="tab1" id="tab1">Tab 1</button>
  <button
    role="tab"
    aria-selected="false"
    aria-controls="tab2"
    id="tab2"
    aria-current="true"
  >
    Tab 2
  </button>
  <button role="tab" aria-selected="false" aria-controls="tab3" id="tab3">Tab 3</button>
</div>
```

## モーダル

モーダルはユーザーの操作性に大きく関わる要素で、アクセシビリティに配慮した実装が重要。

Radix UI、Micromodal.js などの Web アクセビリティ(a11y)に対応した UI ライブラリやプラグインを使用する場合はあまり意識することはないが、1 からモーダルを自作するときはユーザーが Web サイト・アプリを快適に使用できるよう注意する必要がある。

:::message
モーダルを実装する際は、基本的なアクセシビリティ面をクリアでき、制作・開発コストを下げるためにもプラグインやライブラリを積極的に取り入れる方が楽。
:::

モーダルを自作するときに気を付ける点は次のとおり。

### esc キーでモーダルが閉じる

ハンバーガーメニューなども同じだが、モーダルは `esc` キーで閉じる事ができるようにする。
`dialog`要素の登場により、従来の JavaScript での制御を省略することができる。

### フォーカストラップ

モーダルを開いているときに `tab` キーを押すと、モーダル内のコンテンツでフォーカスが循環するように実装する必要がある。
`dialog`要素で実装することでこの問題は解決する。

次の記事で詳しく解説されている。
https://ics.media/entry/220620/

### オーバーレイをクリックしてモーダルが閉じる

モーダルを開いている時にモーダル以外の要素（オーバーレイの部分）をクリックしたときはモーダルが閉じるよう挙動。
これは`dialog`要素の機能には無いので、自作する必要がある。

## タッチデバイスとクリックできない要素は`hover`を無効にする

結論から、`:hover`は次のようにマークアップする。

```css
@media (any-hover: hover) {
  .hover-element:where(:any-link, :enabled, summary):hover {
    background-color: blue;
  }
}
```

https://zenn.dev/kagan/articles/css-hover-style

### `:hover` だけでは何が問題なのか

通常の`hover`ではスマホやタブレット等のタッチデバイスでもタッチした際も hover 時のスタイルが適用されて、再度タッチするまで hover スタイルが残り続けてしまう。

上記を回避するために下記のように CSS を設定する。

```css
@media (hover: hover) {
  a:hover {
    opacity: 0.7;
    transition: ease 0.3s;
  }
}
```

### `any-hover`で `hover` に対応しているか判別する

ただ、これではタブレットでキーボード等を接続して使用している場合に`hover`が適用されなくなる。
`any-hover`を使用することで端末が`hover`に対応しているか（タブレットでキーボードが接続されているか）を判別できるようになる。基本は`any-hover`でホバーのスタイルを設定する。

```css
@media (any-hover: hover) {
  a:hover {
    opacity: 0.7;
    transition: ease 0.3s;
  }
}
```

### クリック可能な要素にだけ `hover` スタイルを適用する

- :any-link
  カレントページに該当するパンくずリストなどは`href`属性が空になるためこのような要素は hover スタイルを適用させない

- :enabled
  カルーセルの最後のスライドに達した時、「次へ」のボタンが`<button disabled>`になっているとき、hover スタイルを適用させない

- :where(summary)
  `summary`要素は hover スタイルを適用させる

### 全部まとめる

下記のように CSS を設定すると、**デバイスが hover をサポートしている**、**a タグにリンクがある**、**`disabled`属性が設定されていない`<button>`や`<input>`タグ**、**`summary`要素**に対して `hover` スタイルが適用されるようになる。

```scss
@media (any-hover: hover) {
  .hover-element:where(:any-link, :enabled, summary):hover {
    background-color: blue;
  }
}
```

### Sass で mixin にしておく

毎回書くのは大変なので、mixin で変数にする。

```scss:_variables.scss
@mixin hover {
  @media (any-hover: hover) {
    &:where(:any-link, :enabled, summary):hover {
      @content;
    }
  }
}
```

**使い方 ↓**

```scss:style.scss
@use "../mixin/_variables" as *;

.link {
  color: #000;

  @include hover {
    color: #3ea8ff;
  }
}
```

ちなみに Tailwind CSS の場合は下記で一括指定が可能

```js:tailwind.config.js
module.exports = {
  future: {
    hoverOnlyWhenSupported: true,
  },
};
```

## 大文字英語のマークアップ

大文字の英字をそのままマークアップすると、スクリーンリーダーで読み上げ時に適切に発音されない。
下記のようにマークアップすると「エービーオーユーティー」と読み上げられる。

```html:
<p>ABOUT</p>
```

そのため、上記の場合は「About」と記述し CSS の`text-transform: uppercase`を指定して大文字表記にする。

## `<span>`タグでテキストを 1 文字ずつアニメーションさせる時は翻訳させないようにする

1 文字ごとテキストをアニメーションさせるときに`<sapn>`タグで囲むが、これだと適切な翻訳がされないので、親要素に`translate="no"`属性を付与して翻訳対象から外しておく。

```html
<div tranlate="no"><span>テ</span><span>キ</span><span>ス</span><span>ト</span></div>
<span class="sr-only">テキスト</span>
```

## 参考記事

https://lifull.github.io/accessibility-guidelines/
