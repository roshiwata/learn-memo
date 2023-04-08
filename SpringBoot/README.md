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



