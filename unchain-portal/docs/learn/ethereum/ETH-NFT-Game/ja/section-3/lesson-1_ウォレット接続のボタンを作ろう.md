### 🌅 `window.ethereum`を設定する

Webアプリケーション上で、ユーザーがイーサリアムネットワークと通信するためには、Webアプリケーションはユーザーのウォレット情報を取得する必要があります。

これから、あなたのWebアプリケーションにウォレットを接続したユーザーに、スマートコントラクトを呼び出す権限を付与する機能を実装していきます。

- これは、Webサイトへの認証機能です。

ターミナル上で、`packages/client/src`に移動し、その中にある`App.js`をVS Codeで開きましょう。

下記のように、`App.js`の中身を更新します。

- `App.js`はあなたのWebアプリケーションのフロントエンド機能を果たします。

```javascript
import React, { useEffect, useState } from "react";
import twitterLogo from "./assets/twitter-logo.svg";
import "./App.css";

// Constantsを宣言する: constとは値書き換えを禁止した変数を宣言する方法です。
const TWITTER_HANDLE = "あなたのTwitterハンドル";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  // ユーザーがMetaMaskを持っているか確認します。
  const checkIfWalletIsConnected = () => {
    const { ethereum } = window;
    if (!ethereum) {
      console.log("Make sure you have MetaMask!");
      return;
    } else {
      console.log("We have the ethereum object", ethereum);
    }
  };

  // ページがロードされたときに useEffect()内の関数が呼び出されます。
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);
  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">⚡️ METAVERSE GAME ⚡️</p>
          <p className="sub-text">プレイヤーと協力してボスを倒そう!</p>
          <div className="connect-wallet-container">
            <img src="https://i.imgur.com/TXBQ4cC.png" alt="LUFFY" />
          </div>
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built with @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

### 🦊 ユーザーアカウントにアクセスできるか確認する

`window.ethereum`は、あなたのWebアプリケーションに参加するユーザーがMetaMaskを持っているか確認し、結果を`Console log`に出力します。

ターミナルで下記を実行してみましょう。

```
yarn client start
```

ローカルサーバーでWebサイトを立ち上げたら、サイトの上で右クリックを行い、`Inspect`を選択します。
![](./../../img/section-3/3_2_1.png)
次に、`Console`を選択し、出力結果を確認してみましょう。
![](./../../img/section-3/3_2_2.png)

Consoleに`We have the ethereum object`と表示されているでしょうか？

- これは、`window.ethereum`が、このWebサイトを訪問したユーザー（ここでいうあなた）がMetaMaskを持っていることを確認したことを示しています。

次に、Webサイトがユーザーのウォレットにアクセスする権限があるか確認します。

- アクセスできたら、スマートコントラクトを呼び出すことができます。

これから、ユーザー自身が承認したWebサイトにウォレットのアクセス権限を与えるコードを書いていきます。

- これは、ユーザーのログイン機能です。

以下のコードを確認してください。

```javascript
import React, { useEffect, useState } from "react";
import twitterLogo from "./assets/twitter-logo.svg";
import "./App.css";

// Constantsを宣言する: constとは値書き換えを禁止した変数を宣言する方法です。
const TWITTER_HANDLE = "あなたのTwitterハンドル";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  // ユーザーのウォレットアドレスを格納するために使用する状態変数を定義します。
  const [currentAccount, setCurrentAccount] = useState(null);

  // ユーザーがMetaMaskを持っているか確認します。
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        console.log("Make sure you have MetaMask!");
        return;
      } else {
        console.log("We have the ethereum object", ethereum);
        // accountsにWEBサイトを訪れたユーザーのウォレットアカウントを格納します。
        // （複数持っている場合も加味、よって account's' と変数を定義している）
        const accounts = await ethereum.request({ method: "eth_accounts" });
        // もしアカウントが一つでも存在したら、以下を実行。
        if (accounts.length !== 0) {
          // accountという変数にユーザーの1つ目（=Javascriptでいう0番目）のアドレスを格納
          const account = accounts[0];
          console.log("Found an authorized account:", account);
          // currentAccountにユーザーのアカウントアドレスを格納
          setCurrentAccount(account);
        } else {
          console.log("No authorized account found");
        }
      }
    } catch (error) {
      console.log(error);
    }
  };

  // ページがロードされたときに useEffect()内の関数が呼び出されます。
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);
  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">⚡️ METAVERSE GAME ⚡️</p>
          <p className="sub-text">プレイヤーと協力してボスを倒そう!</p>
          <div className="connect-wallet-container">
            <img src="https://i.imgur.com/TXBQ4cC.png" alt="LUFFY" />
          </div>
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built with @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

新しく追加したコードを見ていきましょう。

```javascript
// ユーザーのウォレットアドレスを格納するために使用する状態変数を定義します。
const [currentAccount, setCurrentAccount] = useState(null);
```

アクセス可能なアカウントを検出した後、`currentAccount`にユーザーのウォレットアカウント(`0x...`)の値が入ります。

以下で`currentAccount`を更新しています。

```javascript
// accountsにWEBサイトを訪れたユーザーのウォレットアカウントを格納する（複数持っている場合も加味、よって account's' と変数を定義している）
const accounts = await ethereum.request({ method: "eth_accounts" });
// もしアカウントが一つでも存在したら、以下を実行。
if (accounts.length !== 0) {
  // accountという変数にユーザーの1つ目（=Javascriptでいう0番目）のアドレスを格納
  const account = accounts[0];
  console.log("Found an authorized account:", account);
  // currentAccountにユーザーのアカウントアドレスを格納
  setCurrentAccount(account);
} else {
  console.log("No authorized account found");
}
```

この処理のおかげで、ユーザーがウォレットに複数のアカウントを持っている場合でも、プログラムはユーザーの1番目のアカウントアドレスを取得できます。

### 👛 ウォレット接続ボタンを作成する

次に、`connectWallet`ボタンを作成していきます。

下記の通り`App.js`を更新していきましょう。

```javascript
import React, { useEffect, useState } from "react";
import twitterLogo from "./assets/twitter-logo.svg";
import "./App.css";

// Constantsを宣言する: constとは値書き換えを禁止した変数を宣言する方法です。
const TWITTER_HANDLE = "あなたのTwitterハンドル";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  // ユーザーのウォレットアドレスを格納するために使用する状態変数を定義します。
  const [currentAccount, setCurrentAccount] = useState(null);

  // ユーザーがMetaMaskを持っているか確認します。
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        console.log("Make sure you have MetaMask!");
        return;
      } else {
        console.log("We have the ethereum object", ethereum);
        // accountsにWEBサイトを訪れたユーザーのウォレットアカウントを格納します。
        // （複数持っている場合も加味、よって account's' と変数を定義している）
        const accounts = await ethereum.request({ method: "eth_accounts" });
        // もしアカウントが一つでも存在したら、以下を実行。
        if (accounts.length !== 0) {
          // accountという変数にユーザーの1つ目（=Javascriptでいう0番目）のアドレスを格納
          const account = accounts[0];
          console.log("Found an authorized account:", account);
          // currentAccountにユーザーのアカウントアドレスを格納
          setCurrentAccount(account);
        } else {
          console.log("No authorized account found");
        }
      }
    } catch (error) {
      console.log(error);
    }
  };
  // connectWallet メソッドを実装します。
  const connectWalletAction = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }
      // ウォレットアドレスに対してアクセスをリクエストしています。
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });
      // ウォレットアドレスを currentAccount に紐付けます。
      console.log("Connected", accounts[0]);
      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.log(error);
    }
  };

  // ページがロードされたときに useEffect()内の関数が呼び出されます。
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);
  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">⚡️ METAVERSE GAME ⚡️</p>
          <p className="sub-text">プレイヤーと協力してボスを倒そう!</p>
          <div className="connect-wallet-container">
            <img src="https://i.imgur.com/TXBQ4cC.png" alt="LUFFY" />
            {/*
             * ウォレットコネクトを起動するために使用するボタンを設定しています。
             * メソッドを呼び出すために onClick イベントを追加することを忘れないでください。
             */}
            <button
              className="cta-button connect-wallet-button"
              onClick={connectWalletAction}
            >
              Connect Wallet To Get Started
            </button>
          </div>
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built with @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

ここで新しく実装した機能は、以下の2つです。

**1 \. `connectWallet`メソッドを実装**

```javascript
// connectWallet メソッドを実装します。
const connectWalletAction = async () => {
  try {
    const { ethereum } = window;
    if (!ethereum) {
      alert("Get MetaMask!");
      return;
    }
    // ウォレットアドレスに対してアクセスをリクエストしています。
    const accounts = await ethereum.request({
      method: "eth_requestAccounts",
    });
    // ウォレットアドレスを currentAccount に紐付けます。
    console.log("Connected", accounts[0]);
    setCurrentAccount(accounts[0]);
  } catch (error) {
    console.log(error);
  }
};
```

`eth_requestAccounts`メソッドを使用することで、MetaMaskからユーザーにウォレットへのアクセスを許可するよう呼びかけることができます。

**2 \. `Connect Wallet`ボタンの実装**

```javascript
{
  /*
   * ウォレットコネクトを起動するために使用するボタンを設定しています。
   * メソッドを呼び出すために onClick イベントを追加することを忘れないでください。
   */
}
<button
  className="cta-button connect-wallet-button"
  onClick={connectWalletAction}
>
  Connect Wallet To Get Started
</button>;
```

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discordの`#ethereum`で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の3点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

ウォレット接続機能が実装できたら、次のレッスンに進みましょう 🎉
