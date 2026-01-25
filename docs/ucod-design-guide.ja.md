# UCOD Design Guide

## 概要
- PlantUMLで表現するUCOD(UI Class Operation Diagram)の構文・設計・運用方針を説明するドキュメント

---

## 1. クラス
### クラスの概要
- 各クラスを三層構造で記述する
    1. {ステレオタイプ} {クラス名}
    2. {UI要素のタイプ} {UI要素名}
    3. {ユーザーのアクション}
- 複数のクラスをひとまとめにして扱いたい場合は、適宜パッケージを定義する
```plantuml
class "Login Page" <<Page>> {
  (TextBox) Password
  (Button) Log In
  ---
  Perform login
}
```

### ステレオタイプ
- `<<Page>>`, `<<Modal>>`, `<<Component>>`, `<<Overlay>>`, など
    - 必要に応じて適宜追加・統合してよい

### UI要素のタイプ
- `<<Component>>`, `(Button)`, `(TextBox)`, `(Link)`, `(CheckBox)`, `(RadioButton)`, `(Text)`, `(Img)`, など
    - 必要に応じて適宜追加・統合してよい

### UI要素
- あるUI要素の子要素の表現方法には、下記の2通りがある
    1. インデントを用いる方法
        - 子要素の数が少ないときなどには、子要素を親要素の直下に記述し、子要素はインデント(`\t`)を用いて親要素よりも一段字下げする
        - インデントは階層構造(親子関係)を表し、同一レベルのインデントは兄弟要素として扱う
    2. 別クラスに切り出す方法
        - 子要素の数が多いときなどには、親要素をクラス名とする別クラスを作成し、そのUI要素に子要素を記述する
        - 切り出し元のクラスから切り出した別クラスへ矢印`-->`を引き、親子関係を示す
        - 切り出し元クラスと切り出した別クラスとに対し、`package`によるグルーピングを適宜施す。このとき、必要に応じてクラス名のエイリアスを設定してもよい
- あるUI要素の子要素が特定ページでのみ若干異なる場合は、子要素のUI要素タイプの前に`+`や`-`を付けてその差分を表現する
```plantuml
package "Header" as HeaderPackage {
  class "Header" as HeaderClass <<Component>> {
    (Button) Share Button
      \t(Button) Twitter
      \t(Button) Copy Link
    (Button) Hamburger Menu
    ---
  }

  class "Hamburger Menu" <<Overlay>> {
    (Button) Log Out
    ---
    Log out from the system
  }
}

class "Settings Page" <<Page>> {
  <<Component>> Header
    \t+ (Text) Percentage of configured items
  ---
}

"HeaderClass" --> "Hamburger Menu"
```

### ユーザーのアクション
- その画面でできることやユースケースを記述する
- 単一UI操作で完結するアクション(クリック・入力など)は省略してよい
    - 例えば「ログインする」は、ID入力・パスワード入力・ログインボタンクリックなど複数のアクションをまとめた表現なので、UCODの三層目に記述した方がよい
    - 例えば「IDを入力する」は、ID入力だけで完結するアクションでありUI要素`(TextBox) ID入力欄`などから自明であるので、省略してよい

---

## 2. 矢印
- `A --> B : (Button) exampleButton`
    - 画面`A`での `exampleButton`のクリックをトリガとして 画面`B`に遷移できる ことを表す
    - トリガを表すラベル部(`(Button) exampleButton`)は、自明な場合は省略してよい (設定ボタンをクリックして設定ページに遷移する場合など)
- `A --> B : (Button) exampleButton if {condition}`
    - ある条件`condition`のもと 画面`A`での `exampleButton`のクリックをトリガとして 画面`B`に遷移できる ことを表す
- `A <-> B`
    - 双方向に遷移可能でトリガが自明な場合は、双方向矢印を用いてもよい
- (省略)
    - モーダルを閉じたら元のページに戻るなど、自明な場合は適宜省略してよい

---

## 3. POMによる自動テストコードの設計モデルとしてのUCOD
### 概要
- UCODはPOM(Page Object Model)による自動テスト実装のための設計モデルとしての用途も想定している
- 特に、生成AIエージェントなどを用いた、自動テストの自動実装における設計モデルとして有用である

### 実装例
- TypeScriptによるPlaywrightでのPOM(Page Object Model)実装を想定する

#### Page Objectの実装
- UCODのクラスとPOMのクラスとは、基本的に下記の通り対応させて実装できる
    - `<<Page>>` → PageObject
    - `<<Component>>` → 別クラス化してPageObjectに適宜importする
        - `<<Component>>`を持つ任意の`<<Page>>`等を起点としてテストする
    - `<<Modal>>` / `<<Overlay>>` → 遷移元のPageObject(またはComponent)に内包させる
- 各UI要素 `(Button)`, `(TextBox)`, etc. は`Locator`として実装できる
    - 必要に応じて専用の操作メソッドを用意する
- UCOD三層目の「ユーザーのアクション」は、複数UI操作を束ねた(内部で個々の操作メソッドを呼び出す)メソッドとしてPageObjectに実装できる
    - e.g. `login({ email, password })`, `changePassword({ old, new })`

#### アサーションファイルの実装
- 各UI要素をもとにアサーションを実装できる

#### テストコードの実装
- あるクラスから別のクラスへの矢印の経路自体がテストケースとなる
    - UCODの矢印は「(ある条件下で)あるユーザーのアクションをトリガとして 次の画面/モーダルに遷移できる or 子要素を表示できる」ことを表すため