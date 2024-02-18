### ✅ 環境構築を行う

このプロジェクトの全体像は次のとおりです。

1 \. **スマートコントラクトを作成します。**

- ユーザーが「👋（wave）」をスマートコントラクト上であなたに送るための処理方法に関するすべてのロジックを実装します。
- スマートコントラクトは**サーバーコード**のようなものです。

2 \. **スマートコントラクトをブロックチェーン上にデプロイします。**

- 世界中の誰もがあなたのスマートコントラクトにアクセスできます。
- **ブロックチェーンは、サーバーの役割を果たします。**

3 \. **Web アプリケーション（dApp）を構築します**。

- ユーザーは Web サイトを介して、ブロックチェーン上に展開されているあなたのスマートコントラクトと簡単にやりとりできます。
- スマートコントラクトの実装 + フロントエンドユーザー・インタフェースの作成 👉 dApp の完成を目指しましょう 🎉

まず、`Node.js`を取得する必要があります。お持ちでない場合は、[こちら](https://hardhat.org/tutorial/setting-up-the-environment#installing-node.js)にアクセスをしてインストールしてください。このプロジェクトで推奨するバージョンは`v20`です。

インストールが完了したら、ターミナルで以下のコマンドを実行し、バージョンを確認してください。

```bash
$ node -v
v20.5.0
```

### 🍽 Git リポジトリをあなたの GitHub にフォークする

まだ GitHub のアカウントをお持ちでない方は、[こちら](https://qiita.com/okumurakengo/items/848f7177765cf25fcde0) の手順に沿ってアカウントを作成してください。

GitHub のアカウントをお持ちの方は、下記の手順に沿ってプロジェクトの基盤となるリポジトリをあなたの GitHub に[フォーク](https://denno-sekai.com/github-fork/)しましょう。

1. [こちら](https://github.com/unchain-tech/ETH-dApp)から ETH-dApp リポジトリにアクセスをして、ページ右上の`Fork`ボタンをクリックします。

![](./../../img/section-1/1_1_1.png)

2. Create a new fork ページが開くので、「Copy the `main` branch only」という項目に**チェックが入っていることを確認します**。

![](./../../img/section-1/1_1_2.png)

設定が完了したら`Create fork`ボタンをクリックします。あなたの GitHub アカウントに`ETH-dApp`リポジトリのフォークが作成されたことを確認してください。

それでは、フォークしたリポジトリをローカル環境にクローンしましょう。

まず、下図のように、`Code`ボタンをクリックして`SSH`を選択し、Git リンクをコピーしましょう。

![](./../../img/section-1/1_1_3.png)

ターミナル上で作業を行う任意のディレクトリに移動し、先ほどコピーしたリンクを用いて下記を実行してください。

```bash
git clone コピーした_github_リンク
```

無事に複製されたらローカル開発環境の準備は完了です。

### 🔍 フォルダ構成を確認する

実装に入る前に、フォルダ構成を確認しておきましょう。クローンしたスタータープロジェクトは下記のようになっているはずです。

```bash
ETH-dApp
├── .git/
├── .gitignore
├── .yarnrc.yml
├── LICENSE
├── README.md
├── package.json
├── packages/
│   ├── client/
│   └── contract/
└── yarn.lock
```

スタータープロジェクトは、モノレポ構成となっています。モノレポとは、コントラクトとクライアント（またはその他構成要素）の全コードをまとめて 1 つのリポジトリで管理する方法です。

packages ディレクトリの中には、`client`と`contract`という 2 つのディレクトリがあります。

`package.json`ファイルの内容を確認してみましょう。

モノレポを作成するにあたり、パッケージマネージャーの機能である[Workspaces](https://classic.yarnpkg.com/lang/en/docs/workspaces/)を利用しています。

**workspaces**の定義をしている部分は以下になります。

```json
// package.json
  "workspaces": [
    "packages/*"
  ],
```

この機能により、yarn install を一度だけ実行すれば、すべてのパッケージ（今回はコントラクトのパッケージとクライアントのパッケージ）を一度にインストールできるようになります。

ではターミナル上で`ETH-dApp`ディレクトリ下に移動して下記を実行しましょう。

```bash
yarn install
```

⚠️ `command not found: yarn`が発生した場合は、以下のコマンドを実行後、再度`yarn install`を実行してください（[参照](https://yarnpkg.com/getting-started/install)）。

```bash
corepack enable
```

`yarn`コマンドを実行することで、プロジェクトの構築に必要なパッケージのインストールが行われます。

### 📺 フロントエンドの動きを確認する

次に、下記を実行してみましょう。

```bash
yarn client start
```

あなたのローカル環境で、Web サイトのフロントエンドが立ち上がりましたか？

例)ローカル環境で表示されている Web サイト
![](./../../img/section-1/1_1_4.png)

上記のような形でフロントエンドが確認できれば成功です。

これからフロントエンドの表示を確認したい時は、ターミナルに向かい、`ETH-dApp`ディレクトリ上で、`yarn client start`を実行します。

これからも必要となる作業ですので、よく覚えておいてください。
ターミナルを閉じるときは、以下のコマンドが使えます ✍️

- Mac: `ctrl + c`
- Windows: `ctrl + shift + w`

### 👏 コントラクトを作成する準備をする

本プロジェクトではコントラクトを作成する際に`Hardhat`というフレームワークを使用します。

`packages/contract`ディレクトリにいることを確認し、次のコマンドを実行します。

```bash
npx hardhat init
```

`hardhat`がターミナル上で立ち上がったら、それぞれの質問を以下のように答えていきます。

```txt
・What do you want to do? →「Create a JavaScript project」を選択
・Hardhat project root: →「'Enter'を押す」 (自動で現在いるディレクトリが設定されます。)
・Do you want to add a .gitignore? (Y/n) → 「y」
```

（例）

```bash
$ npx hardhat init

888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.18.1 👷‍

✔ What do you want to do? · Create a JavaScript project
✔ Hardhat project root: · /ETH-dApp/packages/contract
✔ Do you want to add a .gitignore? (Y/n) · y

✨ Project created ✨

See the README.md file for some example tasks you can run

Give Hardhat a star on Github if you're enjoying it! ⭐️✨

     https://github.com/NomicFoundation/hardhat
```

> ⚠️: 注意
>
> Windows で Git Bash を使用してハードハットをインストールしている場合、このステップ (HH1) でエラーが発生する可能性があります。問題が発生した場合は、WindowsCMD（コマンドプロンプト）を使用して HardHat のインストールを実行してみてください。

この段階で、フォルダー構造は下記のようになっていることを確認してください。

```diff
ETH-dApp
 ├── .gitignore
 ├── package.json
 └── packages/
     ├── client/
     └── contract/
+        ├── .gitignore
+        ├── README.md
+        ├── contracts/
+        ├── hardhat.config.js
         ├── package.json
+        ├── scripts/
+        └── test/
```

それでは、`contract`ディレクトリ内の`package.json`ファイルに、`"scripts"`を追加しましょう。下のように更新してください。

```json
{
  "name": "contract",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "test": "npx hardhat test"
  },
  "devDependencies": {
    ...
```

hardhat の自動テストを実行するためのコマンドを追加しました。

次に、test/Lock.ts ファイルを修正しましょう。ethers v6 ベースの Toolbox で生成された初期コードを、ethers v5 ベースのコードに置き換えます。このプロジェクトでは ethers v5 を使用するためです。

修正箇所は 2 箇所です。まず初めに、ファイルの先頭でインポートしている`@nomicfoundation/hardhat-toolbox/network-helpers`を`@nomicfoundation/hardhat-network-helpers`に変更します。

**（変更前）**

```javascript
const {
  time,
  loadFixture,
} = require("@nomicfoundation/hardhat-toolbox/network-helpers");
```

**（変更後）**

```javascript
const {
  time,
  loadFixture,
} = require("@nomicfoundation/hardhat-network-helpers");
```

次に、46 行目の`lock.target`を`lock.address`に変更します。

**（変更前）**

```javascript
expect(await ethers.provider.getBalance(lock.target)).to.equal(lockedAmount);
```

**（変更後）**

```javascript
expect(await ethers.provider.getBalance(lock.address)).to.equal(lockedAmount);
```

### ⭐️ 実行する

すべてが機能していることを確認するには、以下を実行します。

```bash
yarn test
```

次のように表示されます。

```bash
  Lock
    Deployment
      ✔ Should set the right unlockTime (1514ms)
      ✔ Should set the right owner
      ✔ Should receive and store the funds to lock
      ✔ Should fail if the unlockTime is not in the future
    Withdrawals
      Validations
        ✔ Should revert with the right error if called too soon
        ✔ Should revert with the right error if called from another account
        ✔ Shouldn't fail if the unlockTime has arrived and the owner calls it
      Events
        ✔ Should emit an event on withdrawals
      Transfers
        ✔ Should transfer the funds to the owner


  9 passing (2s)
```

ターミナル上で`ls`と入力してみて、下記のフォルダーとファイルが表示されていたら成功です。

```bash
README.md         artifacts         cache             contracts         hardhat.config.js package.json      scripts           test
```

ここまできたら、フォルダーの中身を整理しましょう。

まず、`test`の下のファイル`Lock.js`を削除します。

1. `test`フォルダーに移動: `cd test`

2. `Lock.js`を削除: `rm Lock.js`

次に、上記の手順を参考にして`contracts`の下の`Lock.sol`を削除してください。実際のフォルダは削除しないように注意しましょう。

### ☀️ Hardhat の機能について

Hardhat は段階的に下記を実行しています。

1\. **Hardhat は、スマートコントラクトを Solidity からバイトコードにコンパイルしています。**

- バイトコードとは、コンピュータが読み取れるコードの形式のことです。

2\. **Hardhat は、あなたのコンピュータ上でテスト用の「ローカルイーサリアムネットワーク」を起動しています。**

3\. **Hardhat は、コンパイルされたスマートコントラクトをローカルイーサリアムネットワークに「デプロイ」します。**

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discord の`#ethereum`で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の 3 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

環境設定が完了したら、次のレッスンに進んでください 🎉
