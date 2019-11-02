*他の言語で読む: [English](README.md).*

# Node-RED MiniShift Workshop starter

IBM Cloudを使用して、MiniShiftを使用して、またはローカルで、Node-RED を実行する方法を紹介します。

**事前準備**

ワークショップに必要なサービスを作成するため、環境をセットアップする前に IBM Cloud アカウントが必要です。まだお持ちでない場合には以下の手順で作成してください。

1. [こちらでアカウントを作成します](https://cloud.ibm.com/registration)
2. 送信されたメールのリンクをクリックして、アカウントの確認を実施してください

## IBM Cloud 上で Node-RED を実行する

1. ご自身の [IBM Cloud アカウント](http://cloud.ibm.com) で IBM Cloud にログインします
2. 右上にある "Catalog (カタログ)" をクリック
3. "Node-RED Starter" を探してクリック
4. アプリケーションにユニークな名前を設定して "Create (作成)" をクリック
5. いったんアプリが作成されたら [resource list (リソースの表示)](https://cloud.ibm.com/resources) からアクセスできるようになります

**訳者注:** 以下の日本語のインストール情報も参考になります
* [IBMクラウドで実行する](https://nodered.jp/docs/getting-started/ibmcloud)
* [Node-RED で遊んでみる第一歩: Web ページを表示してみる](https://qiita.com/yamachan360/items/e3c552c1d2e83e5bf602)

## MiniShift 上で Node-RED を実行する

マシンに Minishift がインストールされていない場合、[こちらのガイド (英語)](https://docs.okd.io/latest/minishift/index.html) に従ってインストールを実施してください。

**訳者注:** 以下の日本語のインストール情報も参考になります
* [Windows 10上でHyper-Vを使ってminishiftをセットアップ](https://qiita.com/osonoi/items/8e818e9d3018cb14ee4a)
* [Minishiftで使うhyperkitドライバーの設定 for Mac](https://medium.com/@taiponrock/minishift%E3%81%A7%E4%BD%BF%E3%81%86hyperkit%E3%83%89%E3%83%A9%E3%82%A4%E3%83%90%E3%83%BC%E3%81%AE%E8%A8%AD%E5%AE%9A-for-mac-5ccf316e5520)

### OpenShift におけるノードの現状

OpenShift の `nodejs` カートリッジのドキュメントは、以下の場所にあります:

http://openshift.github.io/documentation/oo_cartridge_guide.html#nodejs

### 1. Minishift の実行

ターミナル (PowerShell) を開き、次のコマンドを実行します:
1. `minishift --vm-driver=hyperkit start` (これは Mac の例で、使用しているVMにあわせ変更してください)
2. `eval $(minishift oc-env)`
3. `oc login -u developer`

**訳者注:** Windows の PowerShell で Hyper-V を VM として使用する場合の例:
1. `minishift --vm-driver=hyperv start`
2. `minishift oc-env | Invoke-Expression`
3. `oc login -u developer`


### 2. OpenShift-Node-RED の入手

**リポジトリのクローン**

先ほどのターミナル (PowerShell) 上で：
1. [starter リポジトリ](https://github.com/emarilly/openshift-node-red) を以下のコマンドでクローンします:
- `git clone https://github.com/emarilly/openshift-node-red`
2. ディレクトリ (フォルダ) を ``OpenShift-noe-red`` に変更します
- `cd openshift-node-red`

**package.json ファイルの更新**

お好きな IDE もしくはテキスト編集ソフトを使用して、`package.json` ファイルを開いて編集します:
1. Node.js エンジンのバージョンを [現在の LTS](https://nodejs.org/en/download/) (`>=10.0.0`) に変更します
2. 上記の Node の LTS にあわせて NPM エンジンのバージョンを (`>= 6.0.0`) に変更します
3. Node-RED の dependency を現在のバージョンにあわせ (`>= 1.0`) に変更します
4. 以下の scripts セクションを追加します
  ```
  "scripts": {
    "build": "true",
    "start": "node server.js"
  },
  ```

**server.js ファイルの更新**

次に `server.js` ファイルを開いて編集します:
1. basic authentication (BASIC認証) をセットアップしている部分をコメントアウトします
  ```
  //setup basic authentication
  //var basicAuth = require('basic-auth-connect');
  //self.app.use(basicAuth(function(user, pass) {
  //    return user === 'test' && pass === atob('dGVzdA==');
  //}));
  ```
2. ローカルポートのデフォルト値を `8080` に変更します
  ```
  self.port      = process.env.OPENSHIFT_NODEJS_PORT || 8080;
  ```
3. デフォルトの IP アドレスを "0.0.0.0" に変更します
  ```
  self.ipaddress = process.env.OPENSHIFT_NODEJS_IP || "0.0.0.0";
  ```

これらがすべて完了したら、ターミナル (PowerShell) に戻って、  次のコマンドを実行します: `npm install`

### Node-RED を Minishift にプッシュする

1. 名前を付けて新しいアプリケーションを作成します (名前の例: myfirst-node-red)
- `oc new-app nodejs~./ --name=myfirst-node-red`
2. アプリケーションのビルドを開始します
- `oc start-build myfirst-node-red --from-dir=./`
3. 以下のコマンドを使用して、いつでもステータスを確認できます
- run `oc status`
4. ビルドが完了したら、次のコマンドを実行してサービスを取得します
- `oc get service myfirst-node-red`
5. 以下のコマンドを使用してホスト名でサービスを公開します:
- `oc expose service myfirst-node-red`

これで完了です! 以下のコマンドを実行し、その結果を参照して、Minishift で実行されている Node-RED アプリケーションにアクセスできます。

`oc get route/myfirst-node-red`

```
NAME               HOST/PORT                                        PATH      SERVICES           PORT       TERMINATION   WILDCARD
myfirst-node-red   myfirst-node-red-node-red.<aaa.bbb.ccc.ddd>.nip.io             myfirst-node-red   8080-tcp                 None
```
6. ```http://myfirst-node-red-node-red.<aaa.bbb.ccc.ddd>.nip.io``` にブラウザでアクセスしてください

## ローカル環境で Node-RED を実行する

[Node-RED User Group Japan ウェブサイト](https://nodered.jp/docs/getting-started/local) の指示に従ってください

これで準備完了です！ ワークショップのページに戻って、指示に従ってください。


## トラブルシューティング

1. Minishift でアプリをビルドする場合、最初のビルドでビルドが失敗することがあります。次のコマンドをもう一度実行してください `oc start-build myfirst-node-red --from-dir=./`
