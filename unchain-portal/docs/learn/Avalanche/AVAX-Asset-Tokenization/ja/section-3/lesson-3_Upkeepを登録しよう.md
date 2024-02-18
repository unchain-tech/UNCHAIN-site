### 🔥 Upkeep を登録しましょう

chainlinkのUIを使用することで、Upkeepを簡単に登録することができます。

### 🪙 LINK を手に入れましょう

まずUpkeepを実行してくれるノードに対して`LINK`トークンを支払う必要があるため、 `LINK`を手に入れます。

[こちら](https://docs.chain.link/resources/link-token-contracts/)のリンク先の`Fuji testnet`の部分を参照してください。

![](./../../img/section-3/2_1_5.png)

Metamaskで特定の(`LINK`を取得するつもりの)アカウントが表示された状態で、 `Add to wallet`をクリックすると、 そのアカウントに`LINK`が表示されるようになります。

![](./../../img/section-3/2_1_6.png)

続いて、 [こちら](https://faucets.chain.link/fuji)からLINKを取得します。

![](./../../img/section-3/2_1_7.png)

20LINKを取得できているはずです。

![](./../../img/section-3/2_1_8.png)

### 🦆 コントラクトを再デプロイしましょう。

[section-2/lesson-3](./../section-2/lesson-3_コントラクトと接続しよう.md)で行なったコントラクトのデプロイとフロントエンドへの反映の流れを参考に、
新しく実装したコントラクトの再デプロイと、 ついでにフロントエンドへの反映もここで行いましょう。

まず`AVAX-Asset-Tokenization/`直下で下記のコマンドを実行してデプロイします！

```
yarn contract deploy
```

その後`assetTokenization address:`に続くコントラクトのアドレスを`client`ディレクトリ内、 `hooks/useContract.ts`の中の以下の部分に貼り付けてください。

```javascript
export const AssetTokenizationAddress =
  "コントラクトのデプロイ先アドレス";
```

次にABIファイルを取得していきましょう。
ターミナルから取得する場合は`contract`直下に移動し次のようなコマンドを使用します。

```
cp artifacts/contracts/AssetTokenization.sol/AssetTokenization.json ../client/artifacts/
```

そして最後に型定義ファイルの取得を行います。
ターミナルから取得する場合は`contract`直下に移動し次のようなコマンドを使用します。

```
cp -r typechain-types/* ../client/types/
```

以上でコントラクトの情報を反映することができました。

再デプロイする際はコントラクトの情報の更新を忘れないように気をつけましょう。

デプロイしたアドレスは次の`Upkeepの登録`でも使用します。

### 👨‍💻 Upkeep を登録しましょう

[こちら](https://automation.chain.link/fuji)より`Chainlink Automation`のページに移動します。

`Connect wallet`をクリックし、LINKを取得したアカウントを選択します。

その後`Register new Upkeep`をクリックします。

![](./../../img/section-3/2_1_1.png)

`Custom Logic`をクリックします。

![](./../../img/section-3/2_1_2.png)

デプロイしたコントラクトのアドレスを貼り付け、 `Next`をクリックします。

![](./../../img/section-3/2_1_3.png)

次のページにおいて、 以下のように詳細を入力し、 `Register Upkeep`をクリックします。
※ admin addressは先ほど**LINK を取得したアカウントのアドレス**を貼り付けてください。
その他は任意の値を入れて頂いて構いません。

![](./../../img/section-3/2_1_4.png)

しばらくするとトランザクションが完了します。

![](./../../img/section-3/2_1_9.png)

`View Upkeep`をクリックすると、 登録したUpkeepの詳細が表示されます。
(登録後は[Chainlink Automation ホームページ](https://automation.chain.link/fuji)からでもupkeepを確認できます)

後ほどこちらのページの`History`の欄で実際に`Upkeep`関数が実行されたかを確認できます。

![](./../../img/section-3/2_1_10.png)

### 🎍 挙動の確認方法

ここで`Upkeep`が動くことを簡単に確認します。

既にフロントエンドに新コントラクトの情報は反映してあるので、 `AVAX-Asset-Tokenization`ディレクトリ直下で以下のコマンドを実行してください！

```
yarn client dev
```

そしてブラウザで`http://localhost:3000`へアクセスしてください。

はじめに`For Farmer`ボタンをクリックしページを移動します。

`Tokenize`タブにてフォームを記入してNFTを作成しますが、 ここで消費期限を現在より前に設定してみてください。

例)

![](./../../img/section-3/2_1_13.png)

`generate NFT`でコントラクトを作成後、
[Chainlink Automation ホームページ](https://automation.chain.link/fuji)の`My upkeeps`から作成したUpkeepをクリックします。
Upkeepのページを開くと、 ページ下部にHistoryという欄があります。
ここに`Perform Upkeep`が動いた履歴があるはずです。

![](./../../img/section-3/2_1_11.png)

`Perform Upkeep`によって期限切れのNFTは削除されたので、
ブラウザ上で`For Buyers`のページへ移動し、 デプロイされたNFTの情報を見ようとしても表示されないはずです。

![](./../../img/section-3/2_1_14.png)

### 🌔 参考リンク

> [こちら](https://github.com/unchain-dev/AVAX-Asset-Tokenization)に本プロジェクトの完成形のレポジトリがあります。
> 期待通り動かない場合は参考にしてみてください。

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discordの`#avalanche`で質問をしてください。

ヘルプをするときのフローが円滑になるので、 エラーレポートには下記の3点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

おめでとうございます!
コントラクトの自動実行を実装することができました！

次のセクションではdappをデプロイします 🏌️‍♀️
