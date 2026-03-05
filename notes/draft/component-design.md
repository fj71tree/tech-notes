# フロントエンドにおけるコンポーネントの設計方法と個人的なアプローチ

## はじめに

この記事では、コンポーネントの設計方法に対する考察と、個人開発におけるアプローチをご紹介します。

個人のアプローチとして参考にしていただき、設計に対する知見を増やしていただけると嬉しいです！

## コンポーネント設計とは何か？なぜ必要なのか？

コンポーネント設計はAPI設計やデータベース設計と比べると、何をするのかが曖昧ですよね。
この記事では、

- コンポーネントをどの粒度で分割するのか？
- APIやStoreとの接続はどこで行うのか？
- 状態はどこで管理するのか？

の観点から考察をしています。

コンポーネント設計をすることで、

- コードの再利用がしやすくなる。
- ソースコードの可読性が向上する。
- チーム開発において迷いが減る。
- テストがしやすくなる。

といったメリットがあります。

## コンポーネントはどのように分割するとよいか？

コンポーネントの分割は設計の中心です。
分割方法によっては、状態管理やStore接続をする場所まで決まってきます。
まずは、コンポーネント分割の基本となるAtomic DesignとContainer/UIパターンを見ていきます。

### Atomic Design

Atomic Designは、UIデザインの考え方です。コンポーネントをAtoms、Molecules、Organisms、Templates、Pagesに分けて考えます。

- Atoms: ボタンなど、最小単位のコンポーネント。

- Molecules: Atomsを組み合わせて作られる入力フィールドなど。

- Organisms: MoleculesやAtomsを組み合わせた、より大きなUIのセクション単位。アカウント登録の入力フォームなど。

- Templates: ページのデザイン構造を表したコンポーネント。

- Pages: 完成したページ。APIのレスポンスも受け取り、実際のコンテンツが表示されるようにする。

Atomic Designの考え方でコンポーネントを分割するメリットとしては、

- コンポーネントの再利用性と一貫性を高めることができる。
- UIデザインの手法であるため、デザインチームと連携を取りやすくなる。
- デザインの変更がしやすい。

という点が考えられます。
他方で、注意点としては、

- MoleculesとOrganismsなどの認識がチーム内でずれる可能性がある。
- コンポーネントが細分化されるため、ファイル数が増大する。

という点が考えられます。
Atomsは明確ですが、MoleculesとOrganismsなどの違いはチーム内で認識がずれる可能性があるため、認識合わせが重要です。

Atomic Designは、あくまでUIデザインの考え方であるため、他の方式と組み合わせることが可能です。
次にご紹介する、Container/UIパターンと組み合わせることも可能です。
ルールというよりは考え方として、コンポーネントを設計する際に取り入れていくのがよいと思います。

### Container/UIパターン

Atomic DesignはUIの粒度を整理する考え方でした。
Container/UIパターンでは、ロジックやStore接続を行うContainerコンポーネントと、見た目だけを担当するUIコンポーネントに分割します。
このパターンを用いることで、ロジックの置き場所や状態管理の場所を整理することができます。

まずは、Containerコンポーネントの役割から確認をしていきます。
Containerコンポーネントは、

- Storeと接続する。
- APIと接続する。
- アプリケーションの状態を管理する。
- UIに必要なpropsを渡す。

という役割を持っています。Containerコンポーネントは子コンポーネントとしてUIコンポーネントを持っています。
UIコンポーネントでは、

- 渡されたpropsをもとに、画面に描画する。
- ボタンなどがクリックされたら、Containerの関数が発火するようにする。

という役割を持っています。このように分割をすることで、

- Containerはロジック、UIは描画というように、テストがしやすくなる。
- UIコンポーネントの再利用性を高めることができる。
- Containerで状態管理やStore接続をすると決めることで、データの流れがわかりやすくなる。

というメリットがあります。Atomic Designと組み合わせることも可能です。

## コンポーネント設計に対する個人開発のアプローチ

個人開発では、コンポーネントをPages、Container、UIという方法に分けています。また、StoreとAPI専用のファイルも用意しています。
ただし、完全なContainer/UIパターンを採用しているのではなく、その処理と深く結びつくローカルな状態は子コンポーネントで管理しています。
Storeとの接続は、Containerのみが行うようにして、データのフローがわかるようにしています。

Pagesの責務としては、ルーティングを行うのみです。Containerコンポーネントだけを保持しています。
LoginPage.vue

```Vue
<script setup lang="ts">
import LoginContainer from '../containers/LoginContainer.vue'
</script>

<template>
  <LoginContainer />
</template>
```

Containerでは、ロジックと状態の橋渡しを行っています。Storeとの接続もContainerが行うようにしています。

```Vue
<script setup lang="ts">
import { ref } from 'vue'
import { useAuthStore } from '@/stores/auth'
import LoginFormUI from '../components/LoginFormUI.vue'

const authStore = useAuthStore()

const isLoading = ref(false)
const error = ref<string | null>(null)

const handleSubmit = async (payload: { email: string; password: string }) => {
  error.value = null
  isLoading.value = true

  try {
    await authStore.login(payload.email, payload.password)
  } catch {
    error.value = 'ログインに失敗しました'
  } finally {
    isLoading.value = false
  }
}
</script>

<template>
  <LoginFormUI
    :isLoading="isLoading"
    :error="error"
    @submit="handleSubmit"
  />
</template>
```

UIでは、見た目の表示をしています。UI内ではStoreとの接続は行わないようにしています。

```Vue
<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps<{
  isLoading: boolean
  error: string | null
}>()

const emit = defineEmits<{
  (e: 'submit', payload: { email: string; password: string }): void
}>()

const email = ref('')
const password = ref('')

const onSubmit = () => {
  emit('submit', { email: email.value, password: password.value })
}
</script>

<template>
  <form @submit.prevent="onSubmit" class="space-y-4">
    <div>
      <label>メール</label>
      <input v-model="email" />
    </div>

    <div>
      <label>パスワード</label>
      <input type="password" v-model="password" />
    </div>

    <button type="submit" :disabled="props.isLoading">
      {{ props.isLoading ? 'ログイン中...' : 'ログイン' }}
    </button>

    <p v-if="props.error" class="text-red-500 text-sm">{{ props.error }}</p>
  </form>
</template>
```

一般的なContainer/UIパターンの場合は入力されたemailとpasswordの状態は、Containerが管理しますが、コンポーネントを分割した場合に、propsが増えすぎてしまうのを懸念し、UIで管理しています。

UIコンポーネントで状態管理をするものは、そのUIの内部で完結する状態のみにしています。

- inputの値
- モーダルの開閉
- タブ選択

などです。

他方で、全体の処理に関する

- ローディングをしているか
- error
- 送信処理

などは親で持つようにしています。

まとめると、全体に関係する処理やグローバルな状態管理はContainer、UIだけで完結するローカルな状態管理はUIにしています。

また、コンポーネントの分割としては、ヘッダー、フッター、モーダル、ナビなど、まずは意味のある単位ごと(organismsのイメージ)に分割をしています。
その後、そのコンポーネントの中でもコードの再利用が必要になりそうな部分や複数の責務を持つ部分は、さらに分割をしていきます(moleculesのイメージ)。
個人開発であるため仕様を変更することが多く、コンポーネントの細分化をするよりも、変更のしやすさを優先しています。

仕様が固まってきているため、今後の方針としては、

- 複数のところで再利用するコンポーネントか？
- 1ファイルが大きくなりすぎていないか？
- 複数の責務を持たせすぎていないか？
- 可読性が低下していないか？

という基準を持ちながらコンポーネントを分割していく予定です。

## 参考文献

- [Atomic Designの概念](https://zenn.dev/sunnyheee/articles/b5c8985af8407a)
- [StorybookでReactコンポーネント分割の粒度を見極めよう](https://buildersbox.corp-sansan.com/entry/2023/12/10/000000)
- [【React】コンポーネント設計パターン6選 - 小〜中規模開発](https://qiita.com/ktdatascience/items/58a38c0efc915651b2cc)
