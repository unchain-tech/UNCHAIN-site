### 📒 Web アプリケーションからスマートコントラクトを呼び出す

このレッスンでは、MetaMask の認証機能を使用して、Web アプリケーションから実際にあなたのコントラクトを呼び出す機能を実装します。

`WavePortal.sol`に実装した`getTotalWaves`関数を覚えていますか？

```solidity
  function getTotalWaves() public view returns (uint256) {
      console.log("We have %d total waves!", totalWaves);
      return totalWaves;
  }
```

`App.js`を以下のように更新して、フロントエンドから`getTotalWaves`関数へアクセスできるようにします。

```javascript
/* ethers 変数を使えるようにする*/
import { ethers } from "ethers";
import React, { useEffect, useState } from "react";

import "./App.css";

const App = () => {
  // ユーザーのパブリックウォレットを保存するために使用する状態変数を定義します。
  const [currentAccount, setCurrentAccount] = useState("");
  console.log("currentAccount: ", currentAccount);

  // window.ethereumにアクセスできることを確認します。
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        console.log("Make sure you have MetaMask!");
        return;
      } else {
        console.log("We have the ethereum object", ethereum);
      }
      // ユーザーのウォレットへのアクセスが許可されているかどうかを確認します。
      const accounts = await ethereum.request({ method: "eth_accounts" });
      if (accounts.length !== 0) {
        const account = accounts[0];
        console.log("Found an authorized account:", account);
        setCurrentAccount(account);
      } else {
        console.log("No authorized account found");
      }
    } catch (error) {
      console.log(error);
    }
  };

  // connectWalletメソッドを実装
  const connectWallet = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });
      console.log("Connected: ", accounts[0]);
      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.log(error);
    }
  };

  // waveの回数をカウントする関数を実装
  const wave = async () => {
    try {
      const { ethereum } = window;
      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const wavePortalContract = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        );
        let count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
        console.log("Signer:", signer);
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
  };

  // WEBページがロードされたときに下記の関数を実行します。
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);
  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          <span role="img" aria-label="hand-wave">
            👋
          </span>{" "}
          WELCOME!
        </div>
        <div className="bio">
          イーサリアムウォレットを接続して、「
          <span role="img" aria-label="hand-wave">
            👋
          </span>
          (wave)」を送ってください
          <span role="img" aria-label="shine">
            ✨
          </span>
        </div>
        {/* waveボタンにwave関数を連動させる。*/}
        <button className="waveButton" onClick={wave}>
          Wave at Me
        </button>
        {/* ウォレットコネクトのボタンを実装 */}
        {!currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Connect Wallet
          </button>
        )}
        {currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Wallet Connected
          </button>
        )}
      </div>
    </div>
  );
};

export default App;
```

ここで実装した新しい機能は下記の 3 つです。

**1 \. ethers 変数を使えるようにする**

```javascript
import { ethers } from "ethers";
```

`ethers`のさまざまなクラスや関数は、[ethersproject](https://docs.ethers.io/v5/getting-started/) が提供するサブパッケージからインポートできます。これは、Web アプリケーションからコントラクトを呼び出す際に必須となるので、覚えておきましょう。

**2 \. wave の回数をカウントする関数を実装する**

```javascript
const wave = async () => {
  try {
    // ユーザーがMetaMaskを持っているか確認
    const { ethereum } = window;
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum);
      const signer = provider.getSigner();
      const wavePortalContract = new ethers.Contract(
        contractAddress,
        contractABI,
        signer
      );
      let count = await wavePortalContract.getTotalWaves();
      console.log("Retrieved total wave count...", count.toNumber());
      console.log("Signer:", signer);
    } else {
      console.log("Ethereum object doesn't exist!");
    }
  } catch (error) {
    console.log(error);
  }
};
```

追加されたコードを見ながら、新しい概念について学びましょう。

**I\. `provider`**

> ```javascript
> const provider = new ethers.providers.Web3Provider(ethereum);
> ```
>
> ここでは、`provider` (= MetaMask) を設定しています。
> `provider`を介して、ユーザーはブロックチェーン上に存在するイーサリアムノードに接続することができます。
> MetaMask が提供するイーサリアムノードを使用して、デプロイされたコントラクトからデータを送受信するために上記の実装を行いました。
>
> `ethers`のライブラリにより`provider`のインスタンスを新規作成しています。

**II\. `signer`**

> ```javascript
> const signer = provider.getSigner();
> ```
>
> `signer`は、ユーザーのウォレットアドレスを抽象化したものです。
>
> `provider`を作成し、`provider.getSigner()`を呼び出すだけで、ユーザーはウォレットアドレスを使用してトランザクションに署名し、そのデータをイーサリアムネットワークに送信することができます。
>
> `provider.getSigner()`は新しい`signer`インスタンスを返すので、それを使って署名付きトランザクションを送信することができます。

**III\. コントラクトインスタンス**

> ```javascript
> const wavePortalContract = new ethers.Contract(
>   contractAddress,
>   contractABI,
>   signer
> );
> ```
>
> ここで、**コントラクトへの接続を行っています。**
>
> コントラクトの新しいインスタンスを作成するには、以下 3 つの変数を`ethers.Contract`関数に渡す必要があります。
>
> 1. コントラクトのデプロイ先のアドレス（ローカル、テストネット、またはイーサリアムメインネット）
> 2. コントラクトの ABI
> 3. `provider`、もしくは`signer`
>
> コントラクトインスタンスでは、コントラクトに格納されているすべての関数を呼び出すことができます。
>
> もしこのコントラクトインスタンスに`provider`を渡すと、そのインスタンスは**読み取り専用の機能しか実行できなくなります**。
>
> 一方、`signer`を渡すと、そのインスタンスは**読み取りと書き込みの両方の機能を実行できるようになります**。
>
> ※ ABI についてはこのレッスンの終盤にて詳しく説明します。

**3 \. wave ボタンに wave 関数を連動させる**

```html
<button className="waveButton" onClick="{wave}">Wave at Me</button>
```

`onClick`プロップを`null`から`wave`に更新して、`wave()`関数を`waveButton`に接続しています。

### 🧪 テストを実行する

今回のレッスンでは実装する機能が多いので、追加する機能 3 つに対してテストを行います。

`App.js`を更新したら、ターミナル上で`yarn client start`を実行してください。

ローカルサーバーを介して表示されている Web アプリケーションから右クリック → `Inspect`を選択し、Console の出力結果を確認してみましょう。

下記のようなエラーが表示されていれば、テストは成功です。
![](./../../img/section-2/2_3_1.png)

これから`contractAddress`と`contractABI`を設定していきます。

### 🏠 `contractAddress`の設定

Sepolia Test Network にコントラクトをデプロイしたとき、下記がターミナルに出力されていたことを覚えてますか？

```
Deploying contracts with account:  0x821d451FB0D9c5de6F818d700B801a29587C3dCa
Account balance:  324443375262705541
Contract deployed to:  0x3610145E4c6C801bBf2F926DFd8FDd2cE1103493
```

`App.js`に`contractAddress`を設定するために、`Contract deployed to`の出力結果(`0x..`)が必要です。

`Contract deployed to`に続く出力結果をどこかにメモしていた場合は、このままレッスンを進めましょう。

再度この結果を出力する場合は、ルートディレクトリにいることを確認してターミナル上で下記を実行してください。

```
yarn contract deploy
```

コントラクトのデプロイ先のアドレスを取得できたら、`App.js`に`contractAddress`という新規の変数を追加しましょう。`Contract deployed to`の出力結果(`0x..`)を設定していきます。

`const [currentAccount, setCurrentAccount] = useState('')`の直下に`contractAddress`を作成しましょう。以下のようになります。

```javascript
const [currentAccount, setCurrentAccount] = useState("");
/*
 * デプロイされたコントラクトのアドレスを保持する変数を作成
 */
const contractAddress = "あなたの WavePortal の address を貼り付けてください";
```

`App.js`を更新したら、ローカルサーバーにホストされている Web アプリケーションから Console を確認してみましょう。

`contractAddress`に関するエラーが消えていれば、成功です。
![](./../../img/section-2/2_3_2.png)

### 📂 ABI ファイルを取得する

ABI（Application Binary Interface）はコントラクトの取り扱い説明書のようなものです。

Web アプリケーションがコントラクトと通信するために必要な情報が、ABI ファイルに含まれています。

コントラクト一つ一つにユニークな ABI ファイルが紐づいており、その中には下記の情報が含まれています。

1. そのコントラクトに使用されている関数の名前
2. それぞれの関数にアクセスするため必要なパラメータとその型
3. 関数の実行結果に対して返るデータ型の種類

ABI ファイルは、コントラクトがコンパイルされた時に生成され、`artifacts`ディレクトリに自動的に格納されます。

ターミナルで`packages/contract`ディレクトリに移動し、`ls`を実行しましょう。

`artifacts`ディレクトリの存在を確認してください。

ABI ファイルの中身は、`WavePortal.json`というファイルに格納されています。

下記を実行して、ABI ファイルをコピーしましょう。

1. ターミナル上で`packages/contract`にいることを確認する（もしくは移動する）。

2. ターミナル上で下記を実行し、`WavePortal.json`を開きましょう。※ ファインダーから直接開くことも可能です。

   > ```
   > code artifacts/contracts/WavePortal.sol/WavePortal.json
   > ```

3. VS Code で`WavePortal.json`ファイルが開かれるので、中身をすべてコピーしましょう。※ VS Code のファインダーを使って、直接`WavePortal.json`を開くことも可能です。

次に、下記を実行して、ABI ファイルを Web アプリケーションから呼び出せるようにしましょう。

1. ターミナル上で`client/src`に移動する。

2. src ディレクトリの中に`utils`ディレクトリを作成して、その中に`WavePortal.json`ファイルを作成する。

   > ```
   > mkdir utils
   > touch utils/WavePortal.json
   > ```

3. 下記を実行して、`WavePortal.json`ファイルを VS Code で開く。

   > ```
   > code client/src/utils/WavePortal.json
   > ```

4. **先ほどコピーした`contract/artifacts/contracts/WavePortal.sol/WavePortal.json`の中身を新しく作成した`client/src/utils/WavePortal.json`の中に貼り付けてください。**

ABI ファイルの準備ができたので、`App.js`にインポートしましょう。

下記のように`App.js`を更新します。

```javascript
/* ethers 変数を使えるようにする*/
import { ethers } from "ethers";
import React, { useEffect, useState } from "react";

import "./App.css";

/* ABIファイルを含むWavePortal.jsonファイルをインポートする*/
import abi from "./utils/WavePortal.json";

const App = () => {
  /*
   * ユーザーのパブリックウォレットを保存するために使用する状態変数を定義します。
   */
  const [currentAccount, setCurrentAccount] = useState("");
  console.log("currentAccount: ", currentAccount);
  /*
   * デプロイされたコントラクトのアドレスを保持する変数を作成
   */
  const contractAddress = "あなたのコントラクトアドレスを貼り付けてください";
  /*
   * ABIの内容を参照する変数を作成
   */
  const contractABI = abi.abi;

  /*
   * window.ethereumにアクセスできることを確認します。
   */
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        console.log("Make sure you have MetaMask!");
        return;
      } else {
        console.log("We have the ethereum object", ethereum);
      }
      /*
       * ユーザーのウォレットへのアクセスが許可されているかどうかを確認します。
       */
      const accounts = await ethereum.request({ method: "eth_accounts" });
      if (accounts.length !== 0) {
        const account = accounts[0];
        console.log("Found an authorized account:", account);
        setCurrentAccount(account);
      } else {
        console.log("No authorized account found");
      }
    } catch (error) {
      console.log(error);
    }
  };

  /*
   * connectWalletメソッドを実装
   */
  const connectWallet = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });
      console.log("Connected: ", accounts[0]);
      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.log(error);
    }
  };

  /*
   * waveの回数をカウントする関数を実装
   */
  const wave = async () => {
    try {
      const { ethereum } = window;
      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        /*
         * ABIを参照
         */
        const wavePortalContract = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        );
        let count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
        /*
         * コントラクトに👋（wave）を書き込む。
         */
        const waveTxn = await wavePortalContract.wave();
        console.log("Mining...", waveTxn.hash);
        await waveTxn.wait();
        console.log("Mined -- ", waveTxn.hash);
        count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
  };

  /*
   * WEBページがロードされたときに下記の関数を実行します。
   */
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);

  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          <span role="img" aria-label="hand-wave">
            👋
          </span>{" "}
          WELCOME!
        </div>
        <div className="bio">
          イーサリアムウォレットを接続して、「
          <span role="img" aria-label="hand-wave">
            👋
          </span>
          (wave)」を送ってください
          <span role="img" aria-label="shine">
            ✨
          </span>
        </div>
        {/*
         * waveボタンにwave関数を連動させる。
         */}
        <button className="waveButton" onClick={wave}>
          Wave at Me
        </button>
        {/*
         * ウォレットコネクトのボタンを実装
         */}
        {!currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Connect Wallet
          </button>
        )}
        {currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Wallet Connected
          </button>
        )}
      </div>
    </div>
  );
};

export default App;
```

コントラクトアドレスをご自身のものに更新するのをお忘れなく!

```javascript
const contractAddress = "あなたのコントラクトアドレスを貼り付けてください";
```

新しく実装されいる機能は下記の 3 つです。

**1 \. ABI ファイルを含む WavePortal.json ファイルをインポートする**

```javascript
import abi from "./utils/WavePortal.json";
```

**2 \. ABI の内容を参照する変数を作成**

```javascript
const contractABI = abi.abi;
```

ABI の参照先を確認しましょう。`wave`関数の中に実装されています。

```javascript
const wave = async () => {
  try {
    const { ethereum } = window;
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum);
      const signer = provider.getSigner();
      /*
       * ABIをここで参照
       */
      const wavePortalContract = new ethers.Contract(
        contractAddress,
        contractABI,
        signer
      );
      let count = await wavePortalContract.getTotalWaves();
      console.log("Retrieved total wave count...", count.toNumber());
    } else {
      console.log("Ethereum object doesn't exist!");
    }
  } catch (error) {
    console.log(error);
  }
};
```

ABI ファイルを`App.js`に追加すると、フロントエンドで`Wave`ボタンがクリックされたとき、**ブロックチェーン上のコントラクトから正式にデータを読み取ることができます**。

**3 \. データをブロックチェーンに書き込む**

コントラクトにデータを書き込むためのコードを実装しました。

```javascript
const wave = async () => {
  try {
    const { ethereum } = window;
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum);
      const signer = provider.getSigner();
      const wavePortalContract = new ethers.Contract(
        contractAddress,
        contractABI,
        signer
      );
      let count = await wavePortalContract.getTotalWaves();
      console.log("Retrieved total wave count...", count.toNumber());
      /*
       * コントラクトに👋（wave）を書き込む。ここから...
       */
      const waveTxn = await wavePortalContract.wave();
      console.log("Mining...", waveTxn.hash);
      await waveTxn.wait();
      console.log("Mined -- ", waveTxn.hash);
      count = await wavePortalContract.getTotalWaves();
      console.log("Retrieved total wave count...", count.toNumber());
      /*-- ここまで --*/
    } else {
      console.log("Ethereum object doesn't exist!");
    }
  } catch (error) {
    console.log(error);
  }
};
```

コントラクトにデータを書き込むコードは、データを読み込むコードに似ています。

主な違いは、コントラクトに新しいデータを書き込むときは、マイナーに通知が送られ、そのトランザクションの承認が求められることです。

データを読み込むときは、そのようなことをする必要はありません。
よって、ブロックチェーンからのデータの読み取りは無料です。

### 🚀 テストを実行する

ターミナル上で、下記を実行しましょう。

```
yarn client start
```

ローカルサーバー上で表示されている Web アプリケーションで`Inspect`を実行し、以下を試してみましょう。

1 \. `Connect Wallet`をボタンを押して、Web アプリケーションにあなたの MetaMask のウォレットアドレスを接続する。

2 \. `Wave at Me`ボタンを押して、実際にブロックチェーン上にあなたの「👋（wave）」が反映されているか確認する。

いつものようにローカルサーバーにホストされている Web アプリケーションを`Inspect`し、Console を確認しましょう。

例)`Wave at Me`ボタンを 2 回押した際に出力された Console の結果。

![](./../../img/section-2/2_3_3.png)

それぞれの`Wave`がカウントされ、承認されていることが確認できたら、次のステップに進みましょう。

ターミナルを閉じるときは、以下のコマンドが使えます ✍️

- Mac: `ctrl + c`
- Windows: `ctrl + shift + w`

### 🌱 Etherscan でトランザクションを確認する

あなたの Console に出力されている以下のアドレスをそれぞれコピーして、[Etherscan](https://sepolia.etherscan.io/) に貼り付けてみましょう。

- Connected: `0x..` ← これをコピーして Etherscan に貼り付ける

  🎉 あなたの Sepolia Test Network 上のトランザクションの履歴が参照できます。

- Mined -- `0x..` ← これをコピーして Etherscan に貼り付ける

  🎉 あなたの Web アプリケーションを介して Sepolia Test Network 上に書き込まれた「👋（wave）」に対するトランザクションの履歴が参照できます。

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

おめでとうございます!　セクション 2 が終了しました! `#ethereum`にあなたの Etherscan のリンクを貼り付けて、コミュニティで進捗を祝いましょう 🎉
Etherscan でトランザクションの確認をしたら、次のレッスンに進んでください 😊
