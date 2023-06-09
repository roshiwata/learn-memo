# SpringBootのお勉強

## STSのインストール
基本はこれです。  
https://qiita.com/akrian/items/5b72476e0b047fbb23e3  


https://spring.io/tools  
ここからダウンロード  (x86_64 version)
```
$ tar xvfz spring-tool-suite-4-4.18.0.RELEASE-e4.27.0-linux.gtk.x86_64.tar.gz
$ cd sts-4.18.0.RELEASE/
$ ./SpringToolSuite4
```
※参考　STS、Eclipseの違い  
https://spring.pleiades.io/guides/gs/sts/  

※ショートカットランチャーの作成  
https://pom2019.hatenablog.com/entry/20190506/174316  

SpringBootはサーブレットコンテナが内蔵されている  
それを使って直接サーバを起動してアプリケーションを実行します  
→クラウド上でのデプロイに向いている  

mvnコマンドなどを使いたい  
https://maven.apache.org/download.cgi  
からapache-maven-3.9.1-bin.tar.gzをインストールして解凍し、/optに追加  
環境変数PATHに/opt/==/binを追加(~/.bashrc)  

mvn archetype:generate  
したらカレントディレクトリにプロジェクトフォルダが出来上がる  

```
tatsu@tatsu-g15-ubuntu:~/07_springboot/MyBootApp$ mvn spring-boot:run
tatsu@tatsu-g15-ubuntu:~/Documents/workspace-spring-tool-suite-4-4.18.0.RELEASE/MyBootApp$ mvn spring-boot:run
```
どちらもエラー出るの。  
でもSTSでは実行できるの。  
一旦この問題は無視。  

3章まで終わらせて一旦次へ。  
作業フォルダは以下です。
```
/home/tatsu/Documents/workspace-spring-tool-suite-4-4.18.0.RELEASE/MyBootApp
```

〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜〜

これでデプロイの勉強を進める  
https://learn.microsoft.com/ja-jp/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-kubernetes

※java 17 にバージョンアップ  
```
$ sudo apt update
$ sudo apt search openjdk-\(\.\)\+-jre$
$ sudo apt install openjdk-17-jre
```

jib-maven-pluginは、MavenプロジェクトをコンテナにビルドするためのMavenプラグインです。  
これでコンテナ化できるみたい。  

※デプロイの手順  
AKSを作成（ex. wes-todo-sample）  
```
$ az acr login --name wesoffiresis
$ az aks get-credentials --resource-group wes-offi-p --name wes-todo-sample
$ kubectl create secret docker-registry acr-credential --docker-server=wesoffiresis.azurecr.io --docker-username=wesoffiresis --docker-password=G5U8IlHQZJXHPIi7M01Cjvb9X3l5UZt4/5xTptKZLw+ACRC9JQAz --docker-email=tatsuhiro.0323@gmail.com
$ cd kubernetes
$ kubectl apply -f deploy.yml
```
コードはこちらに公開  
https://github.com/roshiwata/spring-boot-docker  
spring-test1-230409 branch  
deploy.ymlは別のものから少し変えました。

※公開用のportを8080から別のものに変更  
src/main/resources/application.properties  
に  
server.port=8081  
などと記載。デフォルトでは
server.port=8080になっている。  
SpringBootの入門書3章を見て作ったWebアプリは以下に公開。  
https://github.com/roshiwata/springboot-MyBootApp/tree/master  
https://github.com/roshiwata/springboot-MyBootApp/tree/hello_world  



https://github.com/roshiwata/springboot-MyBootApp/tree/master  
をAKSに公開します。
appy2ブランチを使います。
```
tatsu@tatsu-g15-ubuntu:~/07_springboot/communication-test/apply2/springboot-MyBootApp$ mvn spring-boot:run
```
でアクセスを確認
localhost:8082

```
$ az login
$ az config set defaults.acr=wesoffiresis
$ az acr login
$ az acr login && mvn compile jib:build
$ az acr login --name wesoffiresis
$ az aks get-credentials --resource-group wes-offi-p --name springboot-test-sample-comunication1
$ kubectl create secret docker-registry acr-credential2 --docker-server=wesoffiresis.azurecr.io --docker-username=wesoffiresis --docker-password=G5U8IlHQZJXHPIi7M01Cjvb9X3l5UZt4/5xTptKZLw+ACRC9JQAz --docker-email=tatsuhiro.0323@gmail.com
$ cd kubernetes
$ kubectl apply -f deploy_apply2.yaml

```


2023.04.22 再学習  
VMを開発環境としてお試しのサービスを作りたい。  
cd ~/07_springboot/220422/springboot-MyBootApp
(git clone git@github.com:roshiwata/springboot-MyBootApp.git)

mvn compile
mvn package
mvn spring-boot:run

@ContorollerはHTMLを返すことに特化している
@RestContorollerは文字列とかいろいろ返すことができる。

RestContorollerの場合はこんな感じでModelAndViewみたいに書かないとだめ。

	@RequestMapping("/")
	public ModelAndView index(ModelAndView mav) {
		mav.setViewName("index");
		return mav;
	}


Contorollerはreturn "index"でよい


https://github.com/roshiwata/communication-springboot
ここに、アプリ感通信をまとめた。(sample-1ブランチ)

各アプリで実行する
communication-springboot/app1/springboot-MyBootApp$ mvn spring-boot:run  
communication-springboot/app2/springboot-MyBootApp$ mvn spring-boot:run  
curl -X GET http://localhost:8081/hello

# ★★★★★★★こちらだとちゃんとpushできてる★★★★★★★★★★
https://github.com/roshiwata/-communication-springboot3

AKSにダブルデプロイしてみよう   
sample-2　　

ついでにspringbootでAKSでデプロイする方法を整理しよう！！！！
まず、初期のプロジェクト（https://github.com/spring-guides/gs-spring-boot-docker）から以下を追記

https://github.com/roshiwata/spring-boot-docker/commit/be9d1f080836024984a9d7e080cc22590822ea6c
https://github.com/roshiwata/spring-boot-docker/tree/spring-test1-230409/complete


そして、pom.xmlのこの部分
<image>${docker.image.prefix}/springboo-apply1</image>
を変更する。これはdocker imageの名前を設定するところなの。
各appにあるよ。

ちなみに以下はAzureのACRを指定する場所だよ。
<docker.image.prefix>wesoffiresis.azurecr.io</docker.image.prefix>

Wでアプリをデプロイしたいから別の名前をつけるの。

communication-springboot/app1/springboot-MyBootApp$ az acr login && mvn compile jib:build　　
docker imageをそれぞれのappに対して作成（このあたりも自動化したいなあ）

az acr login && mvn compile jib:build


Azure上のACRに作成される  

※デプロイの手順  
AKSを作成（ex. wes-springboot-communication）  
```
$ az acr login --name wesoffiresis
$ az aks get-credentials --resource-group wes-offi-p --name wes-springboot-communication
$ kubectl create secret docker-registry acr-credential-com --docker-server=wesoffiresis.azurecr.io --docker-username=wesoffiresis --docker-password=G5U8IlHQZJXHPIi7M01Cjvb9X3l5UZt4/5xTptKZLw+ACRC9JQAz --docker-email=tatsuhiro.0323@gmail.com
$ cd kubernetes
$ kubectl apply -f deploy_apply_com.yml


うーむ。。。↓↓↓↓↓↓
tatsu@tatsu-g15-ubuntu:~/07_springboot/220422/communication-springboot/kubernetes$ kubectl get pods
NAME                            READY   STATUS    RESTARTS   AGE
spring-apply1-bc945f594-59brk   1/1     Running   0          6m18s
spring-apply2-bbf985d86-hfl8f   1/1     Running   0          7m28s
tatsu@tatsu-g15-ubuntu:~/07_springboot/220422/communication-springboot/kubernetes$ kubectl get svc
NAME            TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP      10.0.0.1      <none>        443/TCP        14m
spring-apply1   LoadBalancer   10.0.91.35    <pending>     81:32066/TCP   9m19s
spring-apply2   LoadBalancer   10.0.126.16   <pending>     82:31186/TCP   9m19s

まあこれはおそらくAKSクラスタのスケールの問題やな
