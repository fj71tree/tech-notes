# TypeScript 基礎まとめ

## 概要

この記事では、TypeScript の基本的な使い方をご紹介します。JavaScript の基礎的な知識がある方を対象にしています。

## TypeScript とは何か

TypeScript とは、JavaScript に型を追加した言語です。JavaScript の構文をそのまま使いながら、変数・関数・オブジェクトに型情報を追加することができます。

## 型とは何か

型とは、変数にどのような値が入るかを決めるものです。変数を宣言する際に型を指定することで、異なるデータ型の値の代入を防ぐことができます。

JavaScript は動的型付け言語のため、実行されるときに型が動的に決まります。また、型が一致しない場合は、型変換が行われるため、型を意識せずに使えることができていました。

```javascript
let value = 10;
value = "hello"; //エラーにならない
```

上記の例で、変数 value は最初に数値が代入されており、その後文字列が代入されています。代入する値の型は違いますが、実行時に型変換が行われるため、エラーは発生しません。

このように便利な JavaScript ですが、動的型付けにはデメリットも存在します。具体的には、数値と文字列の結合や、テストコードの値比較で、予期せぬ動作をする可能性があります。
このようなデメリットを解消しつつ、他のメリットももたらしてくれる言語が TypeScript です。

## 型の基本や使い方

### プリミティブ型

変数名の後ろにデータ型を指定することにより、変数に型を指定できます。

```typescript
const count: number = 10;
const title: string = "TypeScript";
const isDone: boolean = false;
```

### 配列

配列では、各要素の型を指定することができます。

```typescript
const numbers: number[] = [1, 2, 3];
const names: string[] = ["taro", "bob"];
```

### オブジェクト

オブジェクトでは、プロパティの構造を型として指定することができます。

```typescript
const user: { name: string; age: number } = {
  name: "taro",
  age: 20,
};
```

### 関数

関数の引数と戻り値に型を付けることができます。

```typescript
function sum(a: number, b: number): number {
  return a + b;
}
```

### 型エイリアス

型を変数のように定義することができます。利用したい型に名前をつけておくことでコードの可読性向上や再利用のしやすさにつながります。

```typescript
type User = {
  name: string;
  age: number;
};

const user: User = {
  name: "taro",
  age: 20,
};
```

### Union 型

複数の型のうちどれか 1 つになりえることを表す型です。

```typescript
let id: number | string;

id = 1; // OK
id = "abc"; // OK
id = true; // エラー
```

型は一つのみが理想ではありますが、ユーザー入力や API など、外部からくる値のデータ型は複数あるため、この書き方をします。
関数では、

```typescript
function displayId(id: number | string) {
  console.log(id);
}
```

のように書きます。この場合も引数は、数値型の文字列型のどちらでも大丈夫です。

## TypeScript を使うと何がよくなるか

### バグを「実行前」に防ぐことができる

TypeScript を使うことで、TypeScript から JavaScript へコンパイルされる時点で、エラーに気づくことができます。

```typescript
function double(x: number) {
  return x * 2;
}

double("10"); //コンパイル時にエラー。
```

VSCode をエディタとして利用している場合、TypeScript と連携が行われているため、上記のコードを書いた瞬間にエラーを知ることができるようになります。このようにコードの実行前の開発段階でエラーを知ることで、バグの処理を早期にすることができます。

### コードが説明書になる

```typescript
function createUser(name: string, age: number): User;
```

このコードだけで戻り値として、`name`は文字列、`age`は数値で、戻り値として`User`型のデータを返すということがわかるようになります。

### リファクタリングが怖くなくなる

```typescript
type User = {
  id: number;
  name: string;
};
```

上記のコードは型を定義しています。この型は、

```typescript
const user: User = {
  id: 1,
  name: "Taro",
};
```

というように使うことができます。ここでリファクタリングとして、

```typescript
type User = {
  id: number;
  username: string;
};
```

と変更します。すると、他の User 型を使っているオブジェクトはすべてエラーになるため、リファクタリングする場所を明確にすることができます。

## 参考文献

- [TypeScript](https://www.typescriptlang.org/)
- [MDN 文法とデータ型](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Grammar_and_types)
- [MDN JavaScript のデータ型とデータ構造](https://developer.mozilla.org/ja/docs/Web/JavaScript/Guide/Data_structures)
