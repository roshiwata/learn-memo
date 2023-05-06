# vscode の拡張


# SpringBoot

https://github.com/roshiwata/springboot-MyBootApp/tree/apply1

https://github.com/roshiwata/-communication-springboot3


# java のインストール

```
sudo apt update
sudo apt -y install openjdk-17-jre
```



# mvnコマンド
```
wget https://dlcdn.apache.org/maven/maven-3/3.9.1/binaries/apache-maven-3.9.1-bin.tar.gz
tar -zxvf apache-maven-3.9.1-bin.tar.gz
sudo mv apache-maven-3.9.1 /opt/
rm apache-maven-3.9.1-bin.tar.gz
echo "PATH="/opt/apache-maven-3.9.1/bin":$PATH" >> ~/.bashrc
```

=======ここまで済み=======
しかし、mvn --versionでエラー。。。原因を探らないと！


# mvnコマンド
```
https://maven.apache.org/download.cgi
からapache-maven-3.9.1-bin.tar.gzをインストールして解凍し、/optに追加
環境変数PATHに/opt/==/binを追加(~/.bashrc)
```

# azコマンド
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```


# python

```

```
