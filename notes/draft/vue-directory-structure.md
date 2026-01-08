# Vue.js ディレクトリ構成を比較

## 概要

この記事では Vue のディレクトリ構成を、作業のしやすさや拡張性、チーム開発の観点から考察しています。
Vue を個人開発に利用しており、そこで悩んだ経験をもとにこの記事を執筆しています。

## ディレクトリ構成の種類

Vue のディレクトリ構成には、主に次の 2 つがあると考えられます。

- 技術的な分類
- 機能的な分類

今回はカレンダー機能をもつ Todo アプリを例に考えます。
技術的な分類では、コンポーネントを components、store、api というフォルダで分けます。
他方で、機能的な分類では、コンポーネントをアプリの機能である todo や calendar、auth(ログイン、ログアウト処理など)というフォルダで分けます。

それぞれの分類の詳細と比較をしていきます。

## 技術的な分類

技術的な分類では、カレンダー機能つき Todo アプリのディレクトリは次のようになります。技術的分類では components や composables 自体が
共有される前提であるため、あえて共通化ディレクトリを設けていません。

```
src
├─ pages
│  ├─ HomePage.vue
│  ├─ TodoPage.vue
│  ├─ CalendarPage.vue
│  └─ LoginPage.vue
│
├─ components
│  ├─ todo
│  │  ├─ TodoList.vue
│  │  ├─ TodoItem.vue
│  │  └─ TodoForm.vue
│  ├─ calendar
│  │  ├─ CalendarView.vue
│  │  └─ CalendarCell.vue
│  ├─ auth
│  │  └─ LoginForm.vue
│  ├─ layout
│  │  ├─ AppHeader.vue
│  │  └─ AppSidebar.vue
│  └─ ui
│     ├─ BaseButton.vue
│     ├─ BaseInput.vue
│     ├─ BaseModal.vue
│     └─ LoadingSpinner.vue
│
├─ store
│  ├─ todo.ts
│  ├─ calendar.ts
│  └─ auth.ts
│
├─ api
│  ├─ todo.ts
│  ├─ calendar.ts
│  └─ auth.ts
│
├─ composables
│  ├─ useTodo.ts
│  ├─ useCalendar.ts
│  └─ useAuth.ts
│
├─ router
│  └─ index.ts
│
├─ App.vue
└─ main.ts

```

components や api というファイル別にフォルダ分けがされています。この分類のメリットとしては、

- ファイルを配置する場所が明確で、チーム開発で配置場所の認識ずれが発生しにくい。
- Vue の初期設定ディレクトリ構成に近い(2026 年 1 月時点)。
- ディレクトリ構成が深くなりにくい。

の 3 点が考えられます。個人開発や、開発初期のスピードが求められる場面において、Vue の初期設定ディレクトリ構成に近いことや、ファイルの配置場所が明確なことは非常に大きなメリットです。機能別分類では、機能の切り出し方や複数機能にまたがるファイルの配置を考える必要があり、開発者の負荷が比較的高くなります。技術的分類を採用することで、内容の開発に集中することができるようになります。シンプルなディレクトリ構成であることが強みになると考えられます。

他方で、デメリットとしては、

- 規模が拡大した場合、同じ todo 機能などのファイルがそれぞれのフォルダに分散されて配置されることにより、ファイルを探すのが大変になる。
- 分散されてファイルが配置されることにより、認知負荷が大きくなる。
- vscode などのエディタにおいて、各ディレクトリを開いて作業することになり見づらい。

の 3 点が考えられます。同じ todo 機能で使われるファイルが分散されて配置されることは、フォルダ構成をパッと見たときに、アプリの動きがわかりづらくなる懸念があります。特に、チーム開発では新規参画者の認知負荷が大きくなります。また、3 点目に挙げている各ディレクトリを開く必要があるという点も、個人開発で気づいた点でした。個人差はあると思いますが、私は frontend フォルダと backend フォルダを同一ディレクトリにおいて開発しているため、できるかぎりディレクトリの展開は最小にしたいと思っています。

## 機能的な分類

続いては、機能的な分類をご紹介します。カレンダー機能つき Todo アプリの構成は次のようになります。

```
src
├─ pages
│  ├─ HomePage.vue
│  ├─ TodoPage.vue
│  ├─ CalendarPage.vue
│  └─ LoginPage.vue
│
├─ features
│  ├─ todo
│  │  ├─ components
│  │  │  ├─ TodoList.vue
│  │  │  ├─ TodoItem.vue
│  │  │  └─ TodoForm.vue
│  │  ├─ store
│  │  │  └─ todo.ts
│  │  ├─ api
│  │  │  └─ todo.ts
│  │  └─ composables
│  │     └─ useTodo.ts
│  │
│  ├─ calendar
│  │  ├─ components
│  │  │  ├─ CalendarView.vue
│  │  │  └─ CalendarCell.vue
│  │  ├─ store
│  │  │  └─ calendar.ts
│  │  ├─ api
│  │  │  └─ calendar.ts
│  │  └─ composables
│  │     └─ useCalendar.ts
│  │
│  └─ auth
│     ├─ components
│     │  └─ LoginForm.vue
│     ├─ store
│     │  └─ auth.ts
│     ├─ api
│     │  └─ auth.ts
│     └─ composables
│        └─ useAuth.ts
│
├─ shared
│  ├─ components
│  │  ├─ BaseButton.vue
│  │  ├─ BaseInput.vue
│  │  ├─ BaseModal.vue
│  │  └─ LoadingSpinner.vue
│  ├─ composables
│  │  ├─ useModal.ts
│  │  └─ useLoading.ts
│  ├─ utils
│  │  └─ date.ts
│  └─ types
│     └─ api.ts
│
├─ router
│  └─ index.ts
│
├─ App.vue
└─ main.ts

```

todo 機能、カレンダー機能、認証機能ごとに components、api を配置しています。各機能ごとに技術的分類をしていますが、全体は機能的な分類です。

機能的な分類のメリットとしては、

- 各機能ごとに components や api が配置されることで、同じ機能を担当するファイルが近くなり、内容を理解しやすくなる。
- プロダクトが成長して、機能やファイル数が増加しても、ファイルが見づらくなりにくい。
- 利用したい api や store を機能からすばやく発見することができる。

プロダクトが成長しても対応しやすいディレクトリ構成です。機能ごとにファイルが分類されるので、利用したいファイルを素早く見つけることができます。
他方で、デメリットとしては、

- ディレクトリ構成が深くなりやすい。
- 機能の切り出し方が難しい場合があり、フォルダ設計の観点から検討が必要になる。
- ファイルを配置する場所がわかりづらくなる可能性がある。

個人開発ではこちらを採用することが多いのですが、機能の切り出し方は悩むことが多いです。これがチーム開発になると、どの機能で分けるかというフォルダ設計がとても重要になると思います。また、配置する場所についてまとめているドキュメント等も必要になる可能性があります。

## 最後に

Vue のディレクトリ構成について機能的な分類と技術的な分類の二つに分けて考察してきました。実際のプロジェクトでは上記二つを組み合わせてディレクトリ構成を設計することが多いと思います。また、上記以外にも、アトミックデザイン的に、コンポーネントの大きさにより分類する方法もあります。ディレクトリ構成に正解はありませんが、個人開発や初期フェーズでは技術的分類、機能が増えた段階で機能的分類へ移行する、という考え方は一つの良い選択肢だと思います。

ディレクトリ構成検討の際に参考にしていただければ幸いです。最後まで読んでいただきありがとうございました！

## 参考文献

- [ファイル構成 ― React](https://ja.legacy.reactjs.org/docs/faq-structure.html)
- [【React】フォルダ構成の考え方](https://zenn.dev/bln/articles/986b709f4df0c1)
- [React フォルダ構造の最適解。コンポーネントの数に合わせて選ぶ基本方針](https://levtech.jp/media/article/column/detail_711/)
