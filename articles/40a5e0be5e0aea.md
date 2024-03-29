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

- 基本的には画像の内容を alt 属性に設定する
- 意味をもたない画像の場合は空の文字列で OK（省略はしない）
  :::

alt 属性の内容についてはこちらの記事が参考になる。
<https://web-de-asobo.net/2023/03/14/html-img-alt/>

## WAI-ARIA

WAI-ARIA には、ロール（Role）、プロパティ（Property）、ステート（State）の 3 つの機能があり、MDN にわかりやすくまとめられている。
<https://developer.mozilla.org/ja/docs/Learn/Accessibility/WAI-ARIA_basics#wai-aria_%E3%81%A3%E3%81%A6%E4%BD%95%EF%BC%9F>

### `role`属性

`role`とは日本語で「役割」を意味する。
`role`属性を付与することで Web ページに「これはボタンです」など、要素の役割を認識してもらうようにすることができる。
ただ、すべての要素には元から対応している暗黙のロールというものがあり、暗黙的にもっているロールを敢えて role 属性で指定することは非推奨とされているので避ける。（`button`に`role="button"`は設定しない、など）
また、要素によって設定できるものとできないものもある。

次の記事が参考になる。

<https://zenn.dev/yusukehirao/articles/e3512a58df58fd>

<https://qiita.com/s-kato/items/fc5df9955698222f5436>

### `aria-label`属性

`aria-label`に指定した値がスクリーンリーダーにそのまま読み上げられる。
`aria-label`は次のような要素に指定する。

- **ページトップボタン**

ボタンを押すと Web サイトの最上部まで戻るボタンには`aria-label`属性をつけてスクリーンリーダーに適切に読み上げられるようにする。

```html:index.html
<a href="#" aria-label="ページ上部へ戻る">
 <!--- 省略 -->
</a>
```

- **ハンバーガーメニュー**

一般的にはグローバルナビゲーションの役割を持つハンバーガーメニューだが、スクリーンリーダーにはどのようなボタンかが伝わらない。

```html:index.html
<button type="button" aria-label="メニューを開く">
 <!--- 省略 -->
</a>
```

### `aria-hidden`属性

スクリーンリーダーに無視してもらいたい要素につける属性

`svg`や`span`タグで SNS シェアボタン等を実装するときは装飾画像として扱うため`aria-hidden="true"`にしたり、ハンバーガーボタンをクリックしないと表示されないメニューなど、初期状態で要素が非表示であることをブラウザに伝えるために`aria-hidden="true"`を付与して、メニューの開閉と連動して false と切り替える。

## モーダル

モーダルはユーザーの操作性に大きく関わる要素で、アクセシビリティに配慮した実装が重要。

Radix UI、Micromodal.js などの Web アクセビリティ(a11y)に対応した UI ライブラリやプラグインを使用する場合はあまり意識することはないのですが、1 からモーダルを自作するときはユーザーが Web サイト・アプリを快適に使用できるよう注意する必要がある。

※モーダルを実装する際は、基本的なアクセシビリティ面をクリアでき、制作・開発コストを下げるためにもプラグインやライブラリを積極的に取り入れる方が楽。

### esc キーでモーダルが閉じる

ハンバーガーメニューなども同じですが、モーダルは esc キーで閉じれるようにする。
`dialog`要素の登場により、従来の JavaScript での制御を省略することができる。

### フォーカストラップ

モーダルを開いているときに tab キーを押すと、モーダル内のコンテンツでフォーカスが循環するように実装する必要がある。
`dialog`要素で実装することでこの問題は解決する。

次の記事で詳しく解説されている。
<https://ics.media/entry/220620/>

### オーバーレイをクリックしてモーダルが閉じる

モーダルを開いている時にモーダル以外の要素（オーバーレイの部分）をクリックしたときはモーダルが閉じるよう挙動。
これは`dialog`要素の機能には無いので、自作する必要がある。

## スマホで`hover`を無効にする

通常の`hover`だと、スマホ（タッチデバイス）の場合はフォーカス状態のときにあたります。つまり、**タッチした後もホバー時のスタイルがそのまま残り続ける**ということになる。

これの改善策としてメディアクエリで hover の on・off を切り替えているのをみかけるが、幅の広いタブレットや逆に幅の狭い PC では思わぬ挙動をしてしまうため、やめた方がいい。

マウスかスマホのタッチ操作かの入力手段を区別するために`hover`はすべて次のように記述する。

```css:style.css
@media (hover: hover) {
  a:hover {
    opacity: 0.7;
    transition: ease 0.3s;
  }
}
```

上記の記述でマウス操作のときだけ hover のスタイルがあたることになる。

ちなみに Tailwind CSS の場合は下記で一括指定が可能

```js:tailwind.config.js
module.exports = {
  future: {
    hoverOnlyWhenSupported: true,
  },
};
```

<https://zenn.dev/kagan/articles/css-hover-style>

## 大文字英語のマークアップ

大文字の英字をそのままマークアップすると、スクリーンリーダーで読み上げ時に適切に発音されない。
下記のようにマークアップすると「エービーオーユーティー」と読み上げられる。

```html:
<p>ABOUT</p>
```

そのため、上記の場合は「About」と記述し CSS の`text-transform: uppercase`を指定して大文字表記にする。

## 参考記事

<https://lifull.github.io/accessibility-guidelines/>
