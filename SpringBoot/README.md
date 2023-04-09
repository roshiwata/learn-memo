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
