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

## VMの作成（wes-develop-vm-ubuntu）
https://github.com/roshiwata/wes-environment/blob/main/wes_env_setup.sh



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



# SpringBoot

https://github.com/roshiwata/springboot-MyBootApp/tree/apply1

https://github.com/roshiwata/-communication-springboot3


