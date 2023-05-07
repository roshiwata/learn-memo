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

「キャプチャ」

![Screenshot from 2023-05-07 14-28-13](https://user-images.githubusercontent.com/58873037/236659515-8e1a6539-399a-4f05-aac7-a8781f60cc5c.png)

![Screenshot from 2023-05-07 14-30-33](https://user-images.githubusercontent.com/58873037/236659579-78689acf-db08-4f6d-b5e0-2b5399671fa3.png)



## 個人的な環境設定（サンプルアプリの用意まで）
https://github.com/roshiwata/wes-environment/blob/main/waka_env_set.sh

## ポートフォワードしてアクセスを確認
```
ssh -i ~/.ssh/keys/wakayama-poc-vm-key.pem -N -L 8081:localhost:8081 wesdeveloper@20.89.95.203

・ブラウザで以下
localhost:8081

curl -X GET http://localhost:8081/nice/get
```



# SpringBoot

https://github.com/roshiwata/springboot-MyBootApp/tree/apply1

https://github.com/roshiwata/-communication-springboot3


