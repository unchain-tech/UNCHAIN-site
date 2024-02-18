###  🖥 このレッスンの参考動画URL
[Dapp University](https://youtu.be/CgXQC4dbGUE?t=3794)

### 📝 ステーキングの仕組み実装する

ここからはこのYield Farmingの根幹となるステーキングのシステムを実装していきます。

まず、`TokenFarm.sol`を以下のように更新していきましょう。

```solidity
pragma solidity ^0.8.18;

import "./DappToken.sol";
import "./MockDaiToken.sol";

contract TokenFarm{
    string public name = "Dapp Token Farm";
    DappToken public dappToken;
    DaiToken public daiToken;

    // 7. これまでにステーキングを行ったすべてのアドレスを追跡する配列を作成
    address[] public stakers;

    // 4.投資家のアドレスと彼らのステーキングしたトークンの量を紐づける mapping を作成
    mapping (address => uint) public stakingBalance;

    // 6. 投資家のアドレスをもとに彼らがステーキングを行ったか否かを紐づける mapping を作成
    mapping (address => bool) public hasStaked;

    // 10. 投資家の最新のステイタスを記録するマッピングを作成
    mapping (address => bool) public isStaking;

    constructor(DappToken _dappToken, DaiToken _daiToken){
        dappToken = _dappToken;
        daiToken = _daiToken;
    }
    // 1.ステーキング機能を作成する
    function stakeTokens(uint _amount) public {
        // 2. ステーキングされるトークンが0以上あることを確認
        require(_amount > 0, "amount can't be 0");
        // 3. 投資家のトークンを TokenFarm.sol に移動させる
        daiToken.transferFrom(msg.sender, address(this), _amount);

        // 5. ステーキングされたトークンの残高を更新する
        stakingBalance[msg.sender] = stakingBalance[msg.sender] + _amount;

        // 8. 投資家がまだステークしていない場合のみ、彼らをstakers配列に追加する
        if(!hasStaked[msg.sender]){
            stakers.push(msg.sender);
        }
        // 9. ステーキングステータスの更新
        isStaking[msg.sender] = true;
        hasStaked[msg.sender] = true;
    }
}
```

コードの理解を促進するために、1から10までコメントに番号を振りました。1つずつみていきましょう。

まず、新しく追加された　`stakeTokens()`関数に注目してください。

```solidity
// 1.ステーキング機能を作成する
function stakeTokens(uint _amount) public {
：
}
```

`stakeTokens`関数は、ステークするトークンの量(`_amount`)を引数としています。

- また、この関数は、スマートコントラクトの外部から呼び出せるように`public`修飾子を持っています。

`stakeTokens`関数の主な役割は、**投資家のウォレットから`TokenFarm.sol`というスマートコントラクトに Dai トークンを転送すること**です。

更に詳しく見ていきましょう。

```solidity
// 1. ステーキング機能を作成する
function stakeTokens(uint _amount) public {
    // 2. ステーキングされるトークンが0以上あることを確認
    require(_amount > 0, "amount can't be 0");
    // 3. 投資家のトークンを TokenFarm.sol に移動させる
    daiToken.transferFrom(msg.sender, address(this), _amount);
:
}
```

ここで最も重要になるのが、`transferFrom()`関数です。

`daiToken`のもととなるスマートコントラクト`MockDaiToken.sol`は他のERC-20トークンと同じように`transferFrom()`関数を保持しています。
- `transferFrom()`関数の中身が気になる人は、`MockDaiToken.sol`の中に定義されている`transferFrom()`を見てみてください!

`transferFrom()`を使用すると、投資家に代わってコントラクト自体(`TokenFarm.sol`)が、実際に資金を移動させることができるようになります。
- `msg.sender`はSolidity内部の特別な変数です。`msg`またはメッセージはSolidity内のグローバル変数で、関数が呼び出されるたびに送信されるメッセージに対応します。`sender`は関数を呼び出した人を意味します。
- 第二引数`address(this)`は、アドレス型に変換されたスマートコントラクトそのもの(`TokenFarm.sol`)です。
- 第三引数は、 `msg.sender`が移動させるトークンの量`amount`を意味します。

### 🎁 ステーキングに関するデータを保存する

`stakeTokens()`関数を実装するためには、いろいろなことを記録しておく必要があります。
- トークンファームの中にどれだけのトークンがあるのか、ユーザーがどれだけの金額を預けているのか、など。

そのために、まず、投資家のアドレスと彼らのステーキングしたトークンの量を紐づけるマッピングを作成します。

```solidity
// 4.投資家のアドレスと彼らのステーキングしたトークンの量を紐づける mapping を作成
mapping (address => uint) public stakingBalance;
```

マッピングはKeyに対応するValueを返すデータ構造です。
今回定義した`stakingBalance`の場合、Keyは`address`（投資のウォレットアドレス）、Valueは投資家がステークするトークンの量になります。

次に、`stakeTokens()`の中で、`stakingBalance`マッピングを使用し、ステーキングされたトークンの残高が更新されるようにします。

```solidity
// 5. ステーキングされたトークンの残高を更新する
stakingBalance[msg.sender] = stakingBalance[msg.sender] + _amount;
```

次に、投資家がステーキングを行ったことを記録していきます。そのために、別のマッピングを作成します。

```solidity
// 6. 投資家のアドレスをもとに彼らがステーキングを行ったか否かを紐づける mapping を作成
mapping (address => bool) public hasStaked;
```

また、これまでにステークしたことのあるすべてのアドレスを追跡する配列(`stakers`)も作成します。

```solidity
// 7. これまでにステーキングを行ったすべてのアドレスを追跡する配列を作成
address[] public stakers;
```

Solidityの配列はリストなので、`stakers`の中身は、以下のような形になります。

```
["0x0...", "0x43...", "0x12..."]
```

`stakers`配列が必要なのは、後でステーキングをしてくれた投資家たちに報酬を発行する必要があるためです。

それでは、`stakeTokens()`に戻り、投資家を`stakers`配列に追加する機能をみていきましょう。

```solidity
// 8. 投資家がまだステークしていない場合のみ、彼らをstakers配列に追加する
if(!hasStaked[msg.sender]){
    stakers.push(msg.sender);
}
```

ここでポイントとなるのは、`stakers`配列には、ユニークなアドレスのみ保管しているということです。

よって、上記のコードでは、**ステーキングする投資家（ユーザー）が Token Farm のはじめてのお客様であった場合にのみ**、彼らのアドレスを、`stakers`配列に追加する仕様になっています。


最後に、投資家のステーキングに関する状態を更新するコードを追加します。

```solidity
// 9. ステーキングステータスの更新
isStaking[msg.sender] = true;
hasStaked[msg.sender] = true;
```

```solidity
// 10. 投資家の最新のステイタスを記録するマッピングを作成
mapping (address => bool) public isStaking;
```
最後に、Yield Farmingを完成させるために、残り2つの機能を実装していきます。
1. コミュニティのトークン発行機能
2. ステーキングしたトークンを手元に戻す機能（アンステーキング機能）

それでは早速`TokenFarm.sol`を下のように更新していきましょう!

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import "./DappToken.sol";
import "./MockDaiToken.sol";

contract TokenFarm{
    string public name = "Dapp Token Farm";
    address public owner;
    DappToken public dappToken;
    DaiToken public daiToken;

    address[] public stakers;
    mapping (address => uint) public stakingBalance;
    mapping (address => bool) public hasStaked;
    mapping (address => bool) public isStaking;

    constructor(DappToken _dappToken, DaiToken _daiToken){
        dappToken = _dappToken;
        daiToken = _daiToken;
        owner = msg.sender;
    }


    //1.ステーキング機能
    function stakeTokens(uint _amount) public {
        require(_amount > 0, "amount can't be 0");
        daiToken.transferFrom(msg.sender, address(this), _amount);

        stakingBalance[msg.sender] = stakingBalance[msg.sender] + _amount;

        if(!hasStaked[msg.sender]){
            stakers.push(msg.sender);
        }

        isStaking[msg.sender] = true;
        hasStaked[msg.sender] = true;
    }

    // ----- 追加する機能 ------ //
    //2.トークンの発行機能
    function issueTokens() public {
        // Dapp トークンを発行できるのはあなたのみであることを確認する
        require(msg.sender == owner, "caller must be the owner");

        // 投資家が預けた偽Daiトークンの数を確認し、同量のDappトークンを発行する
        for(uint i=0; i<stakers.length; i++){
            // recipient は Dapp トークンを受け取る投資家
            address recipient = stakers[i];
            uint balance = stakingBalance[recipient];
            if(balance > 0){
                dappToken.transfer(recipient, balance);
            }
        }
    }

    //　3.アンステーキング機能
    // * 投資家は、預け入れた Dai を引き出すことができる
    function unstakeTokens(uint _amount) public {
        // 投資家がステーキングした金額を取得する
        uint balance = stakingBalance[msg.sender];
        // 投資家がステーキングした金額が0以上であることを確認する
        require(balance > _amount, "staking balance should be more than unstaked amount");
        // 偽の Dai トークンを投資家に返金する
        daiToken.transfer(msg.sender, _amount);
        // 返金した分のdappTokenを利子として付与する
        dappToken.transfer(msg.sender, _amount);
        // 投資家のステーキング残高を0に更新する
        stakingBalance[msg.sender] = balance - _amount;
        // 投資家のステーキング状態を更新する
        isStaking[msg.sender] = false;
    }
}
```

以上で、`TokenFarm.sol`にYield Farmingを実装する上で必要な機能が全て備わりました!

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
ここまででステーキングの実装は完成しましたね!

次のレッスンではステーキングしたトークンに対して報酬としてコミュニティのトークンを提供する機能と、ステーキングしたトークンを自分の手元に戻す機能の実装に入ってバックエンドは完成するので後一踏ん張り頑張っていきましょう!
