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


