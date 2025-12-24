# DOM の基礎から理解する Vue の仕組み

## 概要

この記事では、DOM の基礎から出発して、Vue の仕組みへの理解を深めます。

## DOM とは何か

DOM とは、HTML の文書構造をツリー形式で表したものです。ブラウザが HTML ファイルを解析して作成します。
ツリー形式で文書構造を表すことにより、JavaScript から文字やスタイルの操作を行うことができるようになります。

```html
<body>
  <p id="greetElement">Hello World</p>
</body>
```

上記 HTML の DOM は下記のようなイメージです。

```
document
  └─ html
       └─ body
            └─ p
               └─ "Hello World"
```

DOM 操作で内容を変更する場合は、

```javascript
const greet = document.getElementById("greetElement");
greet.textContent = "Hi";
```

のようにします。

## Vue を使わずに DOM 操作をするとどうなるか？

この章では、状態管理が必要な題材について、Vue を用いずに実装します。DOM 操作を実際に行うことにより、Vue の特徴やレンタリングの仕組みに対する理解へとつなげていきます。

画面上の＋ボタンをクリックするたびに、下の数字が増えていく画面を考えます。

```html
<button id="btn">+</button>
<p id="count">0</p>
```

```javascript
let count = 0;

const countEl = document.getElementById("count");
const btn = document.getElementById("btn");

btn.addEventListener("click", () => {
  count++;
  countEl.textContent = count;
});
```

上記のコードでは、count という状態を持っています。ボタンがクリックされると、count を増やし、自分で DOM の更新を行っています。このコードは、状態管理と DOM 操作を両方書く必要があるため、どの DOM を更新するか人間が管理する必要があります。

Vue を使うことで、

```typescript
import { ref } from "vue";

const count = ref(0);
```

```Vue
<template>
  <button @click="count++">+</button>
  <p>{{ count }}</p>
</template>
```

DOM 操作をすることなく、状態の変更から表示をすることができました。以上のように、Vue は DOM 更新を判断/実行してくれています。このようなことが可能になるのはなぜでしょうか？ここからは。仮想 DOM を中心に Vue のレンタリングの仕組みを考えます。

## Vue の仮想 DOM とレンタリング

仮想 DOM は、実際の DOM 構造を JavaScript のオブジェクトとして表現しています。Vue を利用しない場合、私たちは、実 DOM を操作する必要がありました。Vue を利用する場合、この仮想 DOM をもとにレンダラーが実 DOM を作成・更新してくれます。

### Vue はなぜ仮想 DOM を使うのか？

DOM 更新の効率化を行うためです。実 DOM の操作は、ブラウザにとってコストの高い処理です。仮想 DOM は差分を計算し、必要な部分だけを実 DOM に反映しています。Vue では、次のような流れで UI を更新しています。

1. 状態が変更される
2. 新しい仮想 DOM が生成される
3. 以前の仮想 DOM と比較して、差分を検出する
4. 変更があった部分だけを実 DOM に反映する

この仕組みにより、Vue は最小限の DOM 操作で画面を更新することができます。

## まとめ

- DOM はブラウザが HTML を扱うための仕組み
- 素の JavaScript では状態と DOM 更新を自分で管理する必要がある
- Vue は仮想 DOM を使い、DOM 更新を任せている

## 参考文献

- [Vue.js レンタリングの仕組み](https://ja.vuejs.org/guide/extras/rendering-mechanism)
- [MDN ドキュメントオブジェクトモデル (DOM)](https://developer.mozilla.org/ja/docs/Web/API/Document_Object_Model)
