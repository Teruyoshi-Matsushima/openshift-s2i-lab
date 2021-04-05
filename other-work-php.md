こちらでは、[PHPプロジェクト](https://github.com/osonoi/php-s2i-openshift.git) をOCコマンドによりs2iしていきます。<br>

### A.1. プロジェクト  クローン
ご自身の環境にお好みのディレクトリ/フォルダを作り、そこに移動してください。
その後対象プロジェクト (https://github.com/osonoi/php-s2i-openshift.git) をクローンします。<br>

```
git clone https://github.com/osonoi/php-s2i-openshift
cd php-s2i-openshift
```

### A.2. イメージ ビルド

予め、先の手順で示したように[OCコマンドを実行できるようにし、](https://github.com/Teruyoshi-Matsushima/openshift-s2i-lab/blob/main/work.md#20-oc%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E5%AE%9F%E8%A1%8C%E7%92%B0%E5%A2%83%E6%BA%96%E5%82%99) <br>
OpenShift 環境にログインしてください。

**oc new-build**コマンドでカスタムビルダーイメージをビルドする BuildConfig を定義します。

```
oc new-build --strategy source --binary php --name example-health-php
```

少し時間がかかりますが、最後に**--> Success**と表示されると成功です。

次に、先の手順で作成された**BuildConfig**を指定して、**oc start-build**によりイメージを構築します。

```
oc start-build example-health-php --from-dir src/ --follow
```

こちらも少し時間がかかりますが、イメージがdocker-registryにアップロードされると成功です。<br>

### A.3 デプロイ
先の手順でビルド＆docker-registryへのアップロード完了しました。</br>
次は、docker-registryからアプリをOpenShift上にデプロイします。

```
oc new-app -i example-health-php
```

OpenShiftの上にアプリはデプロイできました。<br>

### A.4 アプリ公開
先の手順で、OpenShift上でアプリは動き出しましたが、まだこのアプリはインターネット環境への接続口を開けていないため、私達はインターネットを介してアクセスすることはできません。<br>
そのため、次のコマンドでこのアプリに外部環境との接続口を構築します。

```
oc expose svc/example-health-php
```

### A.5 アクセスURL
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
example-health-php   example-health-php-default.jpcsm4d02-4216c78965aaa521311d0371fde68bf9-0000.jp-tok.containers.appdomain.cloud          example-health-php   8080-tcp                 None
```

上記の中で**example-health-php-default.jpcsm4d02-4216c78965aaa521311d0371fde68bf9-0000.jp-tok.containers.appdomain.cloud**の部分がアクセスURLになります。<br>
ご自身の環境からこのURLにアクセスしてみてください。
