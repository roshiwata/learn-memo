# Azureのお勉強

## dockerコンテナを用いたReactの雛形実装
```
$ git clone git@github.com:roshiwata/ware-t-robot.git
$ git checkout -b docker origin/docker
$ cd ware-t-robot/docker 
$ docker-compose build
$ docker-compose run --rm react-app sh -c "npm install -g create-react-app && create-react-app react-sample --template typescript"
$ docker-compose up
```

## dockerなしでReactの雛形実装
### まずは基本
以下を参考にした。

https://smillione.co.jp/it_blog/3006/  



※npx, npmのインストールが必要
```
sudo apt update
sudo apt install nodejs
nodejs -v
sudo apt install npm
node -v
mkdir react-app
cd react-app/
npx create-react-app my-app
```

※nodeのアップデートが必要らしい
>Create React App requires Node 14 or higher. 
https://note.com/a10bomber/n/ndb8364977a5c

```
npx create-react-app my-app
cd my-app
npm start
```

### AzureでWebappを作成
webappでgithubを紐付けると  
.github/workflows/master_wes-robot-webapp2.ymlができる。  
これにより、pushしたときにwebappにデプロイされる

https://fwywd.com/tech/deployment-by-azure-app-service-for-nodejs  
これを参考に進める方針

package.json の start スクリプトを  
Azure で　構成→スタートアップコマンドに入力（node index.js）

```
wes-robot-webapp
https://github.com/roshiwata/ware-t-robot
main branch

wes-robot-webapp2
https://github.com/roshiwata/react-app
master branch

wes-robot-webapp3
https://github.com/roshiwata/waka-azure-deploy-express
main branch
```

## Azure IoT Hubの使用
基本はこれ  
https://engineer-ninaritai.com/azure-iothub/

※途中でpythonをインストールする必要がある  
https://websetnet.net/ja/how-to-install-python-3-7-in-ubuntu-linux/

※途中で使うのはこれ  
https://github.com/Azure-Samples/azure-iot-samples-python　　

```
cd ~/05_azure/azure-iot-samples-python/iot-hub/Quickstarts/simulated-device
python3.8 SimulatedDeviceAsync.py
```

※送信したデータの見方  
https://portal.azure.com/#cloudshell/  
で  
```
az iot hub monitor-events --device-id ubuntu-dell-g15 --hub-name robot-iot-wakka
```
をすると見れる。


※プロトコルの設定（確認したかどうか不明）
https://learn.microsoft.com/ja-jp/azure/iot-hub/iot-hub-mqtt-support
websocket=True
を指定するとMQTT over websocket
で通信できる。


============================

## kubernetes のテスト
これを基本とする。
https://www.seplus.jp/dokushuzemi/blog/2022/01/aks_tutorial.html


※azコマンドのインストール  
https://learn.microsoft.com/ja-jp/cli/azure/install-azure-cli-linux?pivots=apt

※wes-robot-kuber-class3  
で外部接続できた！  
以下のマニフェストファイルを使用。  
deploy_mer.yaml  
```
apiVersion: v1
kind: Service
metadata:
  name: example-nginx
spec:
  type: LoadBalancer
  ports: 
  - port: 80
    targetPort: 80
    protocol: TCP
  selector: 
    app: example-nginx
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example-nginx
  template:
    metadata:
      labels:
        app: example-nginx
    spec:
      containers:
      - name: webapp
        image: weswakaconresis.azurecr.io/fordtest-nginx:v1
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: acr-credential

```

※手順  
```
$ az login
$ az acr login --name weswakaconresis
$ docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine weswakaconresis.azurecr.io/fordtest-nginx:v1
$ docker push weswakaconresis.azurecr.io/fordtest-nginx:v1

$ az acr login --name weswakaconresis
$ az aks get-credentials --resource-group wes-robot-test --name wes-robot-kuber-class3
$ kubectl create secret docker-registry acr-credential --docker-server=weswakaconresis.azurecr.io --docker-username=weswakaconresis --docker-password=tlZZA7AllDLinG+A+smhpmxTCdl4tGeG5n+kbjUMjs+ACRBs3fSP --docker-email=tatsuhiro.0323@gmail.com
$ kubectl apply -f deploy_mer.yaml
```

```
$ kubectl get svc

NAME            TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
example-nginx   LoadBalancer   10.0.141.126   20.63.175.14   80:31314/TCP   18m
kubernetes      ClusterIP      10.0.0.1       <none>         443/TCP        3h56m

```
EXTERNAL-IPにブラウザからアクセス（この場合は20.63.175.14）  

※デプロイとサービスの様子は以下で確認できる
```  
$ kubectl describe svc example-nginx  
$ kubectl describe deploy example-nginx  
```

＝＝＝＝＝＝＝＝

## Helmを試す。
https://zenn.dev/08thse/articles/21-aks-helm-sql-server-2019
```
$ az aks get-credentials --resource-group wes-robot-test --name cluster-helm
$ kubectl get services
```
起動できていることが確認できる。

https://level69.net/archives/30110


＝＝＝＝＝＝＝＝　　
## Reactで作成したWebアプリをAKSにデプロイする

①以下の講座でReactのお勉強
https://panasonic-cns.udemy.com/course/modern_javascipt_react_beginner/learn/lecture/21899160#overview

```
npm パッケージのレジストリ
package.json　設計書的な役割
node_modules　各モジュールの実態　js
package-lock yarnlock

モジュールバンドラー　複数のjsファイルを１つにまとめるためのもの
トランスパイラ　新しいJSの記法を古い記法に変換してくれる

SPA
　HTMLは一つでJSでHTMLを書き換える

var 上書き可能　再宣言可能
let 上書き可能　再宣言不可
const 上書き不可　再宣言不可
```

※tatsu@tatsu-g15-ubuntu:~/05_azure/react-todo-js$ docker-compose build  
tatsu@tatsu-g15-ubuntu:~/05_azure/react-todo-js$ docker-compose up  
でlocalhost:3000で接続可能。

※
＞code sandboxでReactのWebアプリを作りました。    
＞そのWebアプリをローカルのPC上でdockerコンテナ上で実行したいと考えています。  
＞node:14.17.5 のdocker imageを元にしてコンテナを作成し、作成したWebアプリ用のフォルダでnpm startを行ったところ、sh: 1: react-scripts: not foundというエラーが出ました。  
＞解決方法を教えてください。  
に関しては、npm installを実行して  node_moduleが作られた後にnpm startしたらいけるようになった。  

react-todo-js_node_1がコンテナ名
これをbuild して　image化したら良さそう.

※port番号の変え方。
https://qiita.com/urouro_net/items/dd7166f9728d08bc933b


## 作成したコンテナをデプロイ

これを再度使用！
https://www.seplus.jp/dokushuzemi/blog/2022/01/aks_tutorial.html


wes-offi-aks-sample5にデプロイ完了  
be-image2 にOKのものをpushしました。  
→そして、be-image3にkubernetesを追加しました。　　
https://github.com/roshiwata/react-temp　　

```
$ cd 05_azure
$ mkdir test-environment3
$ cd test-environment3
$ git clone git@github.com:roshiwata/react-temp.git
$ cd react-temp
$ git checkout -b be-image3 origin/be-image3
$ docker-compose build
Successfully tagged react-temp_node:latest

$ az login
$ az acr login --name wesoffiresis
$ docker tag react-temp_node:latest wesoffiresis.azurecr.io/react-sample-ok:v1
$ docker push wesoffiresis.azurecr.io/react-sample-ok:v1

AKSを作成（ex. wes-todo-sample）

$ az acr login --name wesoffiresis
$ az aks get-credentials --resource-group wes-offi-p --name wes-todo-sample
$ kubectl create secret docker-registry acr-credential --docker-server=wesoffiresis.azurecr.io --docker-username=wesoffiresis --docker-password=G5U8IlHQZJXHPIi7M01Cjvb9X3l5UZt4/5xTptKZLw+ACRC9JQAz --docker-email=tatsuhiro.0323@gmail.com
$ cd kubernetes
$ kubectl apply -f deploy.yml
```

kubectl get podsで起動確認  
```
NAME                               READY   STATUS    RESTARTS   AGE
react-todo-sample-cd8798b5-9xl9h   1/1     Running   0          71s
```

kubectl get svcで外部IPアドレス確認  
```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)        AGE
kubernetes          ClusterIP      10.0.0.1      <none>         443/TCP        4m38s
react-todo-sample   LoadBalancer   10.0.194.36   20.89.189.99   80:32387/TCP   79s
```

ブラウザに外部IPアドレス(EXTERNAL-IP)を貼り付けてアクセス

これで、「Reactで作成したWebアプリをコンテナ化してACRにPushし、AKSにデプロイして外部公開」ができた。  

次にすること：  
　・githubと連携してpushしたら勝手にデプロイされるようにする  
　・githubと連携してpushしたら勝手にビルドテストしてくれるようにする  
　・外部公開したWebアプリをSSL暗号化  

###
AKS
リソース ID

aks-sample-aks
リソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourcegroups/rg-sample/providers/Microsoft.ContainerService/managedClusters/aks-sample-aks

wes-offi-aks-sample5
リソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourcegroups/wes-offi-p/providers/Microsoft.ContainerService/managedClusters/wes-offi-aks-sample5

ワークスペースのリソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/DefaultResourceGroup-EJP/providers/Microsoft.OperationalInsights/workspaces/DefaultWorkspace-4463a0e9-df15-4e30-ac13-45e4e74bb39f-EJP

wes-springboot-communication
リソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourcegroups/wes-offi-p/providers/Microsoft.ContainerService/managedClusters/wes-springboot-communication

ワークスペースのリソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/DefaultResourceGroup-EJP/providers/Microsoft.OperationalInsights/workspaces/DefaultWorkspace-4463a0e9-df15-4e30-ac13-45e4e74bb39f-EJP

wes-todo-sample
リソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourcegroups/wes-offi-p/providers/Microsoft.ContainerService/managedClusters/wes-todo-sample

ワークスペースのリソース ID
/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/DefaultResourceGroup-EJP/providers/Microsoft.OperationalInsights/workspaces/DefaultWorkspace-4463a0e9-df15-4e30-ac13-45e4e74bb39f-EJP


robot-iot-wakka
デバイス ID　ubuntu-dell-g15
主キー　ubuntu-dell-g15
セカンダリ キー　GPFKcz3sW/xOF57IpwXa/OEKg4F12RSaPrI9CX8EcF8=
プライマリ接続文字列　HostName=robot-iot-wakka.azure-devices.net;DeviceId=ubuntu-dell-g15;SharedAccessKey=J81d7NtnD0R4U5ETtZi3MfDkUlF2RUji5f44Z4cVEAU=
セカンダリ接続文字列　HostName=robot-iot-wakka.azure-devices.net;DeviceId=ubuntu-dell-g15;SharedAccessKey=GPFKcz3sW/xOF57IpwXa/OEKg4F12RSaPrI9CX8EcF8=

ID　/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/wes-robot-test/providers/Microsoft.Devices/IotHubs/robot-iot-wakka



wak-iothub

デバイス ID　dell-laptop-test1
主キー　pf961U/ndtSp5AhAF1efbkf/JHzgn4gkeiWbUdNLKBc=
セカンダリ キー　8txVYvD/nseMpMA1zHvBVA1aDIcraZHreljUfY+kqaA=
プライマリ接続文字列　HostName=wak-iothub.azure-devices.net;DeviceId=dell-laptop-test1;SharedAccessKey=pf961U/ndtSp5AhAF1efbkf/JHzgn4gkeiWbUdNLKBc=
セカンダリ接続文字列　HostName=wak-iothub.azure-devices.net;DeviceId=dell-laptop-test1;SharedAccessKey=8txVYvD/nseMpMA1zHvBVA1aDIcraZHreljUfY+kqaA=

ID　/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/waka-iot-hub/providers/Microsoft.Devices/IotHubs/wak-iothub


## AzureADによるVMの認証

これでやってみよう。
https://learn.microsoft.com/ja-jp/azure/active-directory/devices/howto-vm-sign-in-azure-ad-linux

Azure ロールベースのアクセス制御 (RBAC) ポリシー　条件付きアクセス ポリシー
で権限を色々と変えられる。これなんだろう。。

⇒VM にログインできるユーザーを決定する
「管理者権限」「ユーザー権限」
の２つがある。

azコマンど使えるようにインストール

az ssh vm --resource-group wakayama-poc-rg --name wakayama-poc-test02
によるログインはキーの有無にかかわらず

VMの設定でアクセス制御（IAM）で追加から管理マシンの管理者ログインを追加すると行けた
仮想マシンのユーザー ログインでも可


az ssh vmでログインした場合、アクセス制御の権限になるので
ユーザー権限の時はsudo使えません。

az ssh vmでログインしたら、
tatsuhiro.0323@gmail.com@wakayama-poc-test02:

ssh ---でログインしたら、
wakayama@wakayama-poc-test02


VM作るときにAzureADを利用。をクリックしないとこのログイン方法は無理

アクセス制御のところに以下のものが初期である。これ何だろう。
・ユーザー アクセス管理者
・仮想マシンの管理者ログイン
・共同作成者
・所有者
あまり深追いはしない


NEXT　システム割り当てマネージド IDでログインを試したい

![image](https://user-images.githubusercontent.com/58873037/235335635-a629973b-aa20-4e5e-ab81-630b3e00f846.png)



１，４　AzureAD認証しない
２，３，５　AzureAD認証する

３　パスワード
１，２，４，５　SSHキーペア認証


azコマンドのインストール
https://learn.microsoft.com/ja-jp/cli/azure/install-azure-cli-windows?tabs=azure-cli

Windows：「AzureCLIの最新のリリース」

Linux:
https://learn.microsoft.com/ja-jp/cli/azure/install-azure-cli-linux?pivots=apt

curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bashで一発

ユーザー割り当てマネージドID
システム割り当てマネージドIDがある

違いはこちら

システム割り当てマネージドIDはリリース作成時に勝手にできるもの（リソースを削除すると勝手に削除される。）
ユーザ割り当てマネージドIDは自分で作るもの　自分で接続元には割り当て設定を、接続先にはアクセス制御設定をする必要がある。複数のリソースが同じリソースにアクセスする必要があるときとかに有効　　
接続元
![image](https://user-images.githubusercontent.com/58873037/235343150-cf251fa0-f840-4a40-bda6-26f00252c964.png)

接続先
![image](https://user-images.githubusercontent.com/58873037/235343188-a42bd38a-bb0a-4480-b162-1d0acdd9f52c.png)

でもこれでSSH接続できるようになるわけではなかった。
SSHアクセスはあくまでもユーザとのして認証しに行っているからまた別なんだろう。いったん保留。
AKSの時みたいにコードからのアクセスはできるのだろう。（https://techblog.ap-com.co.jp/entry/2022/12/02/113000）


KeyValueはオンプレサーバの認証情報とかを格納するもの

マネージドIDは本質的にサービスプリンシパルのラッパー



これがかなり参考になりそう。熟読しよう。
https://tech-lab.sios.jp/archives/27461

https://tech-lab.sios.jp/archives/23371

サービスプリンシパル

サービスプリンシパルは、アプリケーション用のIDである。
サービスプリンシパルは、アプリケーションの具現化インスタンスである。


「アプリの登録」から「新規登録」
「アプリの登録」の一覧に表示される。
エンタープライズ アプリケーション にも表示される
エンタープライズ アプリケーション＝サービスプリンシパルである。

サービスプリンシパルはアプリケーションの具現化インスタンスなのさ。

アプリの登録で
wakayama-poc-appliaction-test01 | 証明書とシークレット
で新しいクライアントシークレット

値
ZQu8Q~~OnmdhOaViBhHb14M3PNiOkjiBRn4yJcvJ

![image](https://user-images.githubusercontent.com/58873037/235348500-3ebece21-1976-4298-9bb3-81cb49ba40d2.png)

VMのアクセス制御でサービスプリンシパルを許可


![image](https://user-images.githubusercontent.com/58873037/235348884-ebd420ba-351a-493c-9a4f-91a0e411f128.png)



az login --service-principal --username 4116a769-a51f-4420-9f7e-8a9651568a22 --password ZQu8Q~~OnmdhOaViBhHb14M3PNiOkjiBRn4yJcvJ --tenant 7979adce-21e6-45ac-852a-3199b7e7bf25


az vm show -g wakayama-poc-rg -n wakayama-poc-test-sp01
で見れる

アクセス制御を付与していないVMだとエラーになる
az vm show -g wakayama-poc-rg -n wakayama-poc-test06

![image](https://user-images.githubusercontent.com/58873037/235349541-73430ebd-3ac3-4b70-81ff-4b535b6260f9.png)

![image](https://user-images.githubusercontent.com/58873037/235350296-13e529a6-86b5-4ffc-a9f2-0a5ade15f42b.png)

クライアント用のアプリケーション/サービスプリンシパルを作成
wakayama-poc-appliaction-client01 
ctg8Q~BZazSR5s8QmlpnuAWr6T4Qj~QBSY19-bKT
ctg8Q~BZazSR5s8QmlpnuAWr6T4Qj~QBSY19-bKT

![image](https://user-images.githubusercontent.com/58873037/235350306-a28be673-6933-4c42-a220-fe1e28c24bcd.png)
```
https://login.microsoftonline.com/7979adce-21e6-45ac-852a-3199b7e7bf25/oauth2/v2.0/authorize?client_id=5cbd47bd-7348-4dc2-9f3e-dcb56f5efbbd&response_type=code&redirect_uri=https://localhost/&state=12345&scope=api://54906d20-a047-4e70-b651-69d5eb47b6b7/files.read

https://localhost/?code=0.AWoAzq15eeYhrEWFKjGZt-e_Jb1HvVxIc8JNnz7ctW9e-71qAJo.AgABAAIAAAD--DLA3VO7QrddgJg7WevrAgDs_wUA9P__g3aiU-3u4InubVLIZ5a29L_jNYJ6K1R02i8oLfZAoLXd9sKpOm8vUO0ClXRAdTvwKeQhFrmLFp-Len4wJFrxGMtV4VFfjmr0dDWa5UydiMoMGHzb6R_r_bNnNKXf6sFoO2Fh_1QoSpgDVKdds9Zq3YBN27MwNUmCql81UsB2btq3DqZAkKlzOVFYA9Ig0FnRpTiYW32tpbogYx0FRkHXJdrs9KYN3H_IPzCtYeeJk9qgURDIFWAoUcyZt9ITEQ5nJFsk_qj5gq-2FD6ZhZdUWo1TZif9-thk_1XHTz_kr7QhIGSfak2vmneXUkeVlecrIMnHWrn4mWggbDcy3ap_Rde7x04_mw5ujMDhUPU8RK2Nj-MNZYZO64B_Mj_A0Esc_lC1DKt01irSPTjKtPo10y6HrVcLTRLwtXjzsOf1-k7tdFUFn7H5ee_qTaqg419N4VtgW-MWMr_gmBAb-ppjhTSGZWjWsn0fAsfls8n9unif3ZpjsscQ6XlfoypEWkrtryXExuypThFMdOHyWS3HsHMf1KY0BRQVwO9GmbHwibK4zo-EuqG4Z6OZ842aVvkaTJ0-yZqUsgeC3UfDxwE79Dsiwke7Pu1V2WLSNOjTQbhASWJuIXLfgHfTBeCN0LukiW7Tc8sPHldpQZX8ZUNRVL6G7NRyM32wVN8Jl-JleTf_EZplAx3HJizXmRtTL6LelVW8JCWzDuQzeU4Fm6T2H1szPUS-RiBoJxtpIWMkln49Q6H8MhhahV0WMxp8Sb2mHEabUhflQPMqRcMbIZ4TETvHnillbe9jkuDhcPTx8ZWdDNJ6x8LdsF1qeH6xi7CxV1M-UeBz4QYKlhK1zIt2A5vw1GCJo4VJDlMRlG9i6kr79qpy6EIO-dr3FMvEwJBhhOwBk1rmzrsOc4wAOmo6FyZRUhO8NfL1B_DZgwWWTnpPssyKg9EIQZ6wFcI0VyxtWebovao0m19clRJcSE5MIsS9ZORGVD564N06TSPU9a63s5-VlWy-nyVVQ8V4xmV8yI3Kn570r6CDx5s_8N3hF9NQzHHyq-985LkVSsKfjGCZKsIHbj6pa0b8j4OzDJSKwNJhARq1TYYd4e7LSKmxAABDsmHcCKXwQ1TMFVfpkm_HFdHz3jasq4D8sUBQvTRZ-0Hy6oNSvm5mkfNtKbyuYEWy-uLY2J3-DtASXgP1MrCna8gW4MZRI_2C9VhKZIF1OHwg9qteDdRUAnn0mLAjDvEuzrPKbhgH8ZypGh_4ePsOD33ukRO9F0a1giP2Y07DcUGQ3izG2w&state=12345&session_state=d8fc0147-d5e0-4c52-8987-975a4666560e#
```
にリダイレクト


それを踏まえて以下


```
curl -X POST https://login.microsoftonline.com/7979adce-21e6-45ac-852a-3199b7e7bf25/oauth2/v2.0/token -F grant_type=authorization_code -F code=0.AWoAzq15eeYhrEWFKjGZt-e_Jb1HvVxIc8JNnz7ctW9e-71qAJo.AgABAAIAAAD--DLA3VO7QrddgJg7WevrAgDs_wUA9P__g3aiU-3u4InubVLIZ5a29L_jNYJ6K1R02i8oLfZAoLXd9sKpOm8vUO0ClXRAdTvwKeQhFrmLFp-Len4wJFrxGMtV4VFfjmr0dDWa5UydiMoMGHzb6R_r_bNnNKXf6sFoO2Fh_1QoSpgDVKdds9Zq3YBN27MwNUmCql81UsB2btq3DqZAkKlzOVFYA9Ig0FnRpTiYW32tpbogYx0FRkHXJdrs9KYN3H_IPzCtYeeJk9qgURDIFWAoUcyZt9ITEQ5nJFsk_qj5gq-2FD6ZhZdUWo1TZif9-thk_1XHTz_kr7QhIGSfak2vmneXUkeVlecrIMnHWrn4mWggbDcy3ap_Rde7x04_mw5ujMDhUPU8RK2Nj-MNZYZO64B_Mj_A0Esc_lC1DKt01irSPTjKtPo10y6HrVcLTRLwtXjzsOf1-k7tdFUFn7H5ee_qTaqg419N4VtgW-MWMr_gmBAb-ppjhTSGZWjWsn0fAsfls8n9unif3ZpjsscQ6XlfoypEWkrtryXExuypThFMdOHyWS3HsHMf1KY0BRQVwO9GmbHwibK4zo-EuqG4Z6OZ842aVvkaTJ0-yZqUsgeC3UfDxwE79Dsiwke7Pu1V2WLSNOjTQbhASWJuIXLfgHfTBeCN0LukiW7Tc8sPHldpQZX8ZUNRVL6G7NRyM32wVN8Jl-JleTf_EZplAx3HJizXmRtTL6LelVW8JCWzDuQzeU4Fm6T2H1szPUS-RiBoJxtpIWMkln49Q6H8MhhahV0WMxp8Sb2mHEabUhflQPMqRcMbIZ4TETvHnillbe9jkuDhcPTx8ZWdDNJ6x8LdsF1qeH6xi7CxV1M-UeBz4QYKlhK1zIt2A5vw1GCJo4VJDlMRlG9i6kr79qpy6EIO-dr3FMvEwJBhhOwBk1rmzrsOc4wAOmo6FyZRUhO8NfL1B_DZgwWWTnpPssyKg9EIQZ6wFcI0VyxtWebovao0m19clRJcSE5MIsS9ZORGVD564N06TSPU9a63s5-VlWy-nyVVQ8V4xmV8yI3Kn570r6CDx5s_8N3hF9NQzHHyq-985LkVSsKfjGCZKsIHbj6pa0b8j4OzDJSKwNJhARq1TYYd4e7LSKmxAABDsmHcCKXwQ1TMFVfpkm_HFdHz3jasq4D8sUBQvTRZ-0Hy6oNSvm5mkfNtKbyuYEWy-uLY2J3-DtASXgP1MrCna8gW4MZRI_2C9VhKZIF1OHwg9qteDdRUAnn0mLAjDvEuzrPKbhgH8ZypGh_4ePsOD33ukRO9F0a1giP2Y07DcUGQ3izG2w -F scope=api://54906d20-a047-4e70-b651-69d5eb47b6b7/files.read -F client_id=5cbd47bd-7348-4dc2-9f3e-dcb56f5efbbd -F client_secret=ctg8Q~BZazSR5s8QmlpnuAWr6T4Qj~QBSY19-bKT -F redirect_uri=https://localhost/
```
```
{"token_type":"Bearer","scope":"api://54906d20-a047-4e70-b651-69d5eb47b6b7/files.read","expires_in":3781,"ext_expires_in":3781,"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ii1LSTNROW5OUjdiUm9meG1lWm9YcWJIWkdldyIsImtpZCI6Ii1LSTNROW5OUjdiUm9meG1lWm9YcWJIWkdldyJ9.eyJhdWQiOiJhcGk6Ly81NDkwNmQyMC1hMDQ3LTRlNzAtYjY1MS02OWQ1ZWI0N2I2YjciLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83OTc5YWRjZS0yMWU2LTQ1YWMtODUyYS0zMTk5YjdlN2JmMjUvIiwiaWF0IjoxNjgyODU5NTA3LCJuYmYiOjE2ODI4NTk1MDcsImV4cCI6MTY4Mjg2MzU4OSwiYWNyIjoiMSIsImFpbyI6IkFaUUFhLzhUQUFBQW94dHVGKzArZFNQQ3RRN0x4MC96S0k1UE1zTDVCZ2lIRnhPRUlJbkRmM291dEluNVZlOTB1c2tmM3Q5ZGVDVm1MMnd5RzRiczlOZURKU3YxMjdwbmFiamFNQ2JCcGpqVS95cFY5T090VTZyRVhQSnpHZ003WWg1SGlsUFBWMmhuRWJNKzFaMm5CVFFYTWh6K2tXWVhxTkxTL05KMDFQVUc2Rm8xeUhHM0hHNFFYSlJQZDQ1QS9oVHlCWHNhbWFLeCIsImFtciI6WyJwd2QiLCJtZmEiXSwiYXBwaWQiOiI1Y2JkNDdiZC03MzQ4LTRkYzItOWYzZS1kY2I1NmY1ZWZiYmQiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6InRhdHN1aGlyby4wMzIzQGdtYWlsLmNvbSIsImZhbWlseV9uYW1lIjoi6Iul5bGxIiwiZ2l2ZW5fbmFtZSI6IumBlOWkpyIsImlkcCI6ImxpdmUuY29tIiwiaXBhZGRyIjoiMTUzLjI1Mi43Ni4xNiIsIm5hbWUiOiLoi6XlsbEg6YGU5aSnIiwib2lkIjoiZWNmNWE3NTgtNjczNy00MGZhLWI4NWQtNzVlODRhOGY0MmJjIiwicmgiOiIwLkFXb0F6cTE1ZWVZaHJFV0ZLakdadC1lX0pTQnRrRlJIb0hCT3RsRnAxZXRIdHJkcUFKby4iLCJzY3AiOiJmaWxlcy5yZWFkIiwic3ViIjoicUJ2SDJyVlFla1FuTEVjeGVXejZYVDFsZ2puOTI1Yk5TVTBVR0JjNzNpayIsInRpZCI6Ijc5NzlhZGNlLTIxZTYtNDVhYy04NTJhLTMxOTliN2U3YmYyNSIsInVuaXF1ZV9uYW1lIjoibGl2ZS5jb20jdGF0c3VoaXJvLjAzMjNAZ21haWwuY29tIiwidXRpIjoiM2w5Q0JsYmNjMG1xWFNuZ2N3c2ZBQSIsInZlciI6IjEuMCJ9.qkXJQvG1PtlQRy9ZjtRBjtUTt7X3BsejrWhVJamgYcj6UsLT5vWz22LVeD8O9oYIzBK8PoeKcTk6fkS0wB_OqGqSIfc7rlxDI2FSPumQUoA1JjO_6_fDa5SvWfaPll35pnivNpfxsJUIjKTHt2IqDn9rgQgZnEGEWDnnVro3aHlGEEVmt25S1q6Jii7j1c48u1cYLZy3pbxLBHmhDr1NErLTvT2cywUKgfH7STRekDGV4BWtYtkX2bpUphi9M_rR2oxv131Rt4CBmn4mxbEF_2Yd4i57usjuPnGCtAwyIqzJFw8E5qEhRyYkyncwAEh3XjkmghUOaMe3qsuAMszR4w"}
```


【目的】
　クライアントがリソースサーバーにアクセスするためアクセストークンを受け取ること
【手順】
　①クライアント用アプリとリソース用アプリを作成
 　クライアント用アプリ
    　APIのアクセス許可
     　アクセストークンのスコープに、下で作成したＡＰＩを追加
     シークレットキーの作成
  　リソース用アプリ
   　API公開　スコープの追加
　②認可コードを受け取る
 　クライアントアプリのＩＤ
  　リソース用アプリで作成したAPIスコープ
　③認可コードを用いてアクセストークンを受け取る
    認可コード
    クライアントのシークレットキー
 　クライアントアプリのＩＤ
  　リソース用アプリで作成したAPIスコープ    

　クライアントがアクセストークンを受け取るために必要な認可コードを事前にユーザが受け取っておく
 
 
