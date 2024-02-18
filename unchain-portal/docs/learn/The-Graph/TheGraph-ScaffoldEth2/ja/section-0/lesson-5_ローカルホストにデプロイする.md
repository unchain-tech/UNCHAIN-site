## デプロイ

### ✅ サブグラフの作成と公開

これで、The Graphの設定を完了するために、第4のウィンドウを開くことができます。😅 この4番目のウィンドウでは、ローカルサブグラフを作成します！

> 注意：これは一度だけ行う必要があります。

```
yarn local-create
```

![](./../../img/section-0/0_5_1.png)

> サブグラフが作成されたことを示す出力と、docker内のgraph-nodeでのログ出力が表示されるはずです。

次に、サブグラフを公開します！ このコマンドを実行すると、サブグラフにバージョンを付ける必要があります（例：0.0.1）。

```
yarn local-ship
```

![](./../../img/section-0/0_5_2.png)

> このコマンドは、以下のことを一度に行います... 🚀🚀🚀

- hardhat/deploymentsフォルダからコントラクトのABIをコピーします
- networks.jsonファイルを生成します
- サブグラフスキーマとコントラクトABIからAssemblyScriptタイプを生成します
- マッピング関数をコンパイルしてチェックします
- ...そして、ローカルサブグラフをデプロイします！

> ts-nodeのエラーが発生した場合は、次のコマンドでインストールできます。

```
npm install -g ts-node
```

サブグラフのデプロイが成功すると、以下のようになります：

![](./../../img/section-0/0_5_3.png)

ビルドが完了し、サブグラフのエンドポイントアドレスが表示されます。

```
Build completed: QmYdGWsVSUYTd1dJnqn84kJkDggc2GD9RZWK5xLVEMB9iP

Deployed to http://localhost:8000/subgraphs/name/scaffold-eth/your-contract/graphql

Subgraph endpoints:
Queries (HTTP):     http://localhost:8000/subgraphs/name/scaffold-eth/your-contract
```