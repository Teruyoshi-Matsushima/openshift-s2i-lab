## 2. 本日のOpenShiftワークショップ本編
### 概要
OpenShift Container Platform のコマンドラインインターフェース (CLI) を使用し、ターミナルからアプリケーションを作成し、IBM Cloud 上の OpenShift にデプロイします。</br>
このCLIを使ったプロジェクト管理方法は以下の場合に適しています。
* プロジェクトソースコードを直接使用している。
* OpenShift Container Platform 操作をスクリプト化する。
* 帯域幅のリソースによる制限があり、Web コンソール を使用できない。

### 2.0. OCコマンド実行環境準備




### 2.1. プロジェクト  クローン
ワークショッププロジェクト用にお好みのディレクトリ/フォルダを作り、そこに移動してください。
まずこのハンズオンでオマージュするプロジェクト [https://github.com/osonoi/node-build-config-openshift](https://github.com/osonoi/node-build-config-openshift)をクローンします。<br>

```
git clone https://github.com/IBM/node-build-config-openshift
cd node-build-config-openshift
```

今クローンしたディレクトリにある**Dockerfile**を見れば、これから作成しようとしているアプリがどのようにコンテナライズされているのか分かります。<br>

```
cat Dockerfile
```

**Dockerfile**内の中身です。

```
# Use the official Node 10 image
FROM node:10

# Change directory to /usr/src/app
WORKDIR /usr/src/app

# Copy the application source code
COPY . .

# Change directory to site/
WORKDIR site/

# Install dependencies
RUN npm install

# Allow traffic on port 8080
EXPOSE 8080

# Start the application
CMD [ "npm", "start" ]
```

### 2.2. イメージ ビルド
**oc new-build**コマンドでカスタムビルダーイメージをビルドする BuildConfig を定義します。

```
oc new-build --strategy docker --binary --docker-image node:10 --name example-health
```

少し時間がかかりますが、最後に**--> Success**と表示されると成功です。

次に、先の手順で作成された**BuildConfig**を指定して、**oc start-build**によりイメージを構築します。

```
oc start-build example-health --from-dir . --follow
```

こちらも少し時間がかかりますが、イメージがdocker-registryにアップロード完了すれば成功です。<br>

### 2.3 デプロイ
先の手順でビルド＆docker-registryへのアップロード完了しました。</br>
次は、docker-registryからアプリをOpenShift上にデプロイします。

```
oc new-app -i example-health
```

OpenShiftの上にアプリはデプロイできました。<br>

### 2.4 アプリ公開
先の手順で、OpenShift上でアプリは動き出しましたが、まだこのアプリはインターネット環境への接続口を開けていないため、私達はインターネットを介してアクセスすることはできません。<br>
そのため、次のコマンドでこのアプリに外部環境との接続口を構築します。

```
oc expose svc/example-health
```

### 2.5 アクセスURL
先の手順で**example-health**に外部からアクセスすることができるようになりました。<br>
ですが、アクセスURLは分かりませんよね？<br>
そこで、次のコマンドを使ってアクセスURLを調べてください。

```
oc get routes
```

おそらく以下のような出力になるのではないでしょうか？
```
$ oc get routes

NAME             HOST/PORT                                                                                                                        PATH      SERVICES         PORT       TERMINATION   WILDCARD
example-health   example-health-example-health-ns.aida-dev-apps-10-30-f2c6cdc6801be85fd188b09d006f13e3-0001.us-south.containers.appdomain.cloud             example-health   8080-tcp                 None
```

上記の中で**example-health-example-health-ns.aida-dev-apps-10-30-f2c6cdc6801be85fd188b09d006f13e3-0001.us-south.containers.appdomain.cloud**の部分がアクセスURLになります。<br>
ご自身の環境からこのURLにアクセスしてみてください。

### 2.6 アプリケーションへのアクセス
デプロイされ、Webへ公開されたアプリケーションへアクセスできました。<br>
実際にこのアプリケーションへログインしてみましょう。ID,Passwordともに「test」と入れてログインしてください。<br>
医療関連のデータを管理するサンプルアプリケーションへログインできたかと思います。
![](./images/018.png)

お疲れ様でした。ここまでで、GitHub上のソースコードをCLIを使ってOpenShiftへデプロイする方法を学びました。


## その他のアプリケーション
もし、PHPのサンプルアプリケーションで試してみたい方は下記のリポジトリーのソースコードを試してみてください。
[https://github.com/osonoi/php-s2i-openshift](https://github.com/osonoi/php-s2i-openshift)
![](./images/029.png)
