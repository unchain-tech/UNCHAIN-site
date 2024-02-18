### 👋 dApp 開発プロジェクトへようこそ!

![](./../../img/section-0/learn-banner.png)

このプロジェクトでは、 `Avalanche`上にスマートコントラクトを実装し、 スマートコントラクトとやりとりできる独自の`Web`アプリケーションを構築します。

このプロジェクトでは以下の技術が必要です。

- `Terminal`基本操作
- Solidity
- HTML/CSS
- [TypeScript](https://typescriptbook.jp/overview/features)
- [React.js](https://ja.reactjs.org/)

いますべてを理解している必要はありません。  
わからないことがあったらインターネットで検索したり、 コミュニティで質問しながらプロジェクトを進めていきましょう!

`Avalanche`での開発が初めての方や、 `hardhat`でスマートコントラクトのテストを書いたご経験の無い方は [AVAX-Messenger](https://app.unchain.tech/learn/AVAX-Messenger) により詳しく解説がありますので先にそちらを進めるとスムーズかと思います。

また`NFT`に関して実装が初めての方は`ETH NFT Collection`により詳しく解説があります。

### 🛠 何を構築するのか？

**分散型Webアプリケーション（dApp）** を構築します。

資産のトークン化と、 それらトークンを購入することができるdappを作成します。

資産のトークン化とは、 ここでは現実世界の資産をトークンで表現することを指します。

本プロジェクトでは農家の方を対象に、 資産を収穫物（農作物）、 トークンをNFTとしてdappを作成します。  
農家の方は独自の期限付きNFTを作成し、 そのNFTを所持している人には定期的に収穫物を届けるというような農家のサブスクリプションサービスを想定しています。

トークン化には以下のようなメリットがあります。

- 資産を柔軟に分割して価格設定をすることや、 スマートコントラクトに取引仲介を任せることで、 より多くの購入者にアクセスする機会を与えることができます。
- 規格に沿ったトークンにより、 ブロックチェーン上に展開されるあらゆるサービスでトークンの利用・取引が簡単に可能です。
- その他に取引の自動化や透明性などのスマートコントラクトのメリットがあります。

スマートコントラクトに`Solidity`、
フロントエンドに`TypeScript` + `React.js` + `Next.js`を使用します。

今回は作成したスマートコントラクトを、 [FUJI C-Chain](https://docs.avax.network/quickstart/fuji-workflow)へデプロイします。

AvalancheとC-Chainに関する概要は[こちら](https://app.unchain.tech/learn/AVAX-Messenger/ja/0/1/)をご覧ください。 

### 🚀 Avalanche と Tokenization

Avalancheはそのコンセンサスアルゴリズムやサブネット（独自のブロックチェーンを作成）技術により、 トランザクションの速さとスケーラビリティに大きな強みを持っています。

また、Avalancheの開発チームである[Ava Labs](https://www.avalabs.org/)は全ての資産のデジタル化を掲げており、 資産のトークナイズについても推進しております。
[こちら](https://www.youtube.com/watch?v=MdYH53B0Kvg)や[こちら](https://twitter.com/avaxholic/status/1572412941928116224?s=21&t=tge2lrhuOgz0zB7B1GxZwA)を参照してください。

これからあらゆる現実世界の資産のトークン化が進むとすると、 トランザクションの数も増えていくことになります。  
スケーラビリティを持ったAvalancheはその土台として大きな可能性を持っています。

### 🌍 プロジェクトをアップグレードする

[UNCHAIN](https://unchain.tech/) のプロジェクトは [UNCHAIN License](https://github.com/unchain-dev/UNCHAIN-projects/blob/main/LICENSE) により運用されています。

プロジェクトに参加していて、「こうすればもっと分かりやすいのに!」「これは間違っている!」と思ったら、ぜひ`pull request`を送ってください。

`GitHub`から直接コードを編集して直接`pull request`を送る方法は、[こちら](https://docs.github.com/ja/repositories/working-with-files/managing-files/editing-files#editing-files-in-another-users-repository)を参照してください。

どんなリクエストでも大歓迎です 🎉

また、 プロジェクトを自分の`GitHub`アカウントに`Fork`して、中身を編集してから`pull request`を送ることもできます。

- プロジェクトを`Fork`する方法は、[こちら](https://docs.github.com/ja/get-started/quickstart/fork-a-repo) を参照してください。
- `Fork`から`pull request`を作成する方法は、[こちら](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork) です。

**👋 `UNCHAIN-projects`に`pull request`を送る! ⏩ [UNCHAIN の GitHub](https://github.com/unchain-tech/UNCHAIN-projects) にアクセス!**

### ⚡️ `Issue`を作成する

`pull request`送るほどでもないけど、提案を残したい!　と思ったら、[こちら](https://github.com/unchain-tech/UNCHAIN-projects/issues) に`Issue`を作成してみましょう。

`Issue`の作成方法に関しては、[こちら](https://docs.github.com/ja/issues/tracking-your-work-with-issues/creating-an-issue)を参照してください。

`pull request`や`issue`の作成は実際にチームで開発する際、重要な作業になるので、ぜひトライしてみてください。

`UNCHAIN`のプロジェクトをみんなでより良いものにしていきましょう ✨

> Windows を使用している方へ  
> Windows をお使いの場合は、[Git for Windows](https://gitforwindows.org/) をダウンロードし、それに付属する Git Bash を使うことをお勧めします。  
> 本手順では UNIX 固有のコマンドをサポートしています。  
> [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/install) も選択肢の一つです。

---

次のレッスンに進んでプログラミングの環境構築しましょう 🎉

---

Documentation created by [ryojiroakiyama](https://github.com/ryojiroakiyama)（UNCHAIN discord ID: rakiyama#8051）
