# GW明けにやることリスト


## vscode の拡張
- Bicep
- Remote-SSH
- OpenAPI (Swagger) Editor
- Azure API Management
- Open Api snippets
- Swagger Viewer
- YAML
- Azure CLI Tools
- SQLTools


# SpringBoot

https://github.com/roshiwata/springboot-MyBootApp/tree/apply1

https://github.com/roshiwata/-communication-springboot3


# wes-develop-vm-ubuntu


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
source ~/.bashrc
```

# azコマンド
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```




=======ここまで=======

# python

```

```


# 個人的なやつ
```
ssh-keygen -t ed25519
cd .ssh
tail id_ed25519.pub
〜GitHubのSSHに登録〜
```

```
mkdir 01_poc
cd 01_poc
mkdir sample-springboot
cd sample-springboot
git clone git@github.com:roshiwata/springboot-MyBootApp.git

```

```
ssh -i ~/.ssh/keys/wakayama-poc-vm-key.pem -N -L 8081:localhost:8081 wesdeveloper@20.89.95.203
```
