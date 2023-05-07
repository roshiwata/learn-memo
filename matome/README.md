# GW明けにやることリスト


## vscode
### 拡張
- Bicep
- Remote-SSH
- OpenAPI (Swagger) Editor
- Azure API Management
- Open Api snippets
- Swagger Viewer
- YAML
- Azure CLI Tools
- SQLTools

## Virtual Machine
### VMの作成（wes-develop-vm-ubuntu）
https://github.com/roshiwata/wes-environment/blob/main/wes_env_setup.sh

「キャプチャ」

![Screenshot from 2023-05-07 14-28-13](https://user-images.githubusercontent.com/58873037/236659515-8e1a6539-399a-4f05-aac7-a8781f60cc5c.png)

![Screenshot from 2023-05-07 14-30-33](https://user-images.githubusercontent.com/58873037/236659579-78689acf-db08-4f6d-b5e0-2b5399671fa3.png)

「VMの作成」

![Screenshot from 2023-05-07 14-32-57](https://user-images.githubusercontent.com/58873037/236659645-04e7a79b-4edb-47d9-90a5-cfab5819e2e6.png)


### 個人的な環境設定（サンプルアプリの用意まで）
https://github.com/roshiwata/wes-environment/blob/main/waka_env_set.sh

### サンプルアプリの起動
```
$ mvn compile
$ mvn package
$ mvn spring-boot:run
```

### ポートフォワードしてアクセスを確認
```
$ ssh -i ~/.ssh/keys/wakayama-poc-vm-key.pem -N -L 8081:localhost:8081 wesdeveloper@20.89.95.203

・ブラウザでURL(http://localhost:8081/notnice/good)を開く

$ curl -X GET http://localhost:8081/nice/get
```

## Azure Kubetenetes Services

## bicep/githubによるデプロイ（AKSクラスターの作成）

```
# Githubのアカウント、リポジトリ設定
**$ githubOrganizationName='roshiwata'
$ githubRepositoryName='IcA-test'**

# アプリの登録より、新規作成。アプリの詳細情報を取得（applicationRegistrationDetails）
{ "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#applications/$entity", "addIns": [], "api": { "acceptMappedClaims": null, "knownClientApplications": [], "oauth2PermissionScopes": [], "preAuthorizedApplications": [], "requestedAccessTokenVersion": 2 }, "appId": "d65c6d7b-f205-4994-8f22-a65bff6104f7", "appRoles": [], "applicationTemplateId": null, "certification": null, "createdDateTime": "2023-05-02T10:56:12.018479Z", "defaultRedirectUri": null, "deletedDateTime": null, "description": null, "disabledByMicrosoftStatus": null, "displayName": "github-actions-aks-all", "groupMembershipClaims": null, "id": "ba44297c-6e99-4438-be95-c3ca89be290e", "identifierUris": [], "info": { "logoUrl": null, "marketingUrl": null, "privacyStatementUrl": null, "supportUrl": null, "termsOfServiceUrl": null }, "isDeviceOnlyAuthSupported": null, "isFallbackPublicClient": null, "keyCredentials": [], "notes": null, "optionalClaims": null, "parentalControlSettings": { "countriesBlockedForMinors": [], "legalAgeGroupRule": "Allow" }, "passwordCredentials": [], "publicClient": { "redirectUris": [] }, "publisherDomain": "tatsuhiro0323gmail.onmicrosoft.com", "requestSignatureVerification": null, "requiredResourceAccess": [], "samlMetadataUrl": null, "serviceManagementReference": null, "servicePrincipalLockConfiguration": null, "signInAudience": "AzureADandPersonalMicrosoftAccount", "spa": { "redirectUris": [] }, "tags": [], "tokenEncryptionKeyId": null, "verifiedPublisher": { "addedDateTime": null, "displayName": null, "verifiedPublisherId": null }, "web": { "homePageUrl": null, "implicitGrantSettings": { "enableAccessTokenIssuance": false, "enableIdTokenIssuance": false }, "logoutUrl": null, "redirectUriSettings": [], "redirectUris": [] } }
**$ applicationRegistrationDetails=$(az ad app create --display-name 'IcA-test')**

# オブジェクトID　ba44297c-6e99-4438-be95-c3ca89be290e
**$ applicationRegistrationObjectId=$(echo $applicationRegistrationDetails | jq -r '.id')
**

# アプリケーションID　d65c6d7b-f205-4994-8f22-a65bff6104f7
**$ applicationRegistrationAppId=$(echo $applicationRegistrationDetails | jq -r '.appId')
**

# フェデレーション資格情報をアプリに付与　IcA-test
# アプリをgithubリポジトリ/ブランチに連携
$ az ad app federated-credential create \
   --id $applicationRegistrationObjectId \
   --parameters "{\"name\":\"IcA-test\",\"issuer\":\"https://token.actions.githubusercontent.com\",\"subject\":\"repo:${githubOrganizationName}/${githubRepositoryName}:ref:refs/heads/main\",\"audiences\":[\"api://AzureADTokenExchange\"]}"

# リソースグループを作成
# resourceGroupResourceIdにリソースグループ情報を格納 /subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/github-actions-aks-all
$ resourceGroupResourceId=$(az group create --name ToyWebsite --location japaneast --query id --output tsv)

# サービスプリンシパルを作成
$ az ad sp create --id $applicationRegistrationObjectId

# リソースグループのアクセス制御にアプリを権限与える
$ az role assignment create --assignee $applicationRegistrationAppId --role Contributor --scope $resourceGroupResourceId


echo "AZURE_CLIENT_ID: $applicationRegistrationAppId"
echo "AZURE_TENANT_ID: $(az account show --query tenantId --output tsv)"
echo "AZURE_SUBSCRIPTION_ID: $(az account show --query id --output tsv)"
をGitHubシークレットに登録
```
```
こちらをpushするとOK
https://github.com/roshiwata/github-actions-aks-all
```

### 懸念点
- AKSのセキュリティ（IP制限、SSHキー）設定
- VMのほうがよい？

## AKSクラスターへのデプロイ
```
https://github.com/roshiwata/springboot-MyBootApp/tree/apply1-get
```

pom.xmlの変更点
```
# docker imageの名前を設定(各Appにある)
${docker.image.prefix}/springboo-apply1

# AzureのACRを指定する場所（Wでアプリをデプロイしたいなら別の名前をつける）
<docker.image.prefix>wesoffiresis.azurecr.io</docker.image.prefix>
```

docker imageを作成
```
# docker imageをそれぞれのappに対して作成（このあたりも自動化したいなあ）
az acr login && mvn compile jib:build　　 
```

deploy_apply1.yamlの変更点
```
省略
```

```
$ az acr login --name restapispringbootmayacr
$ az aks get-credentials --resource-group restapi-springboot-may --name restapi-springboot-may-cluster
$ kubectl create secret docker-registry acr-credential-com --docker-server=restapispringbootmayacr.azurecr.io --docker-username=restapispringbootmayacr --docker-password=BN+9GIvTWEBKgwrXFQ8Vbc9GDxJvEiAUp8gfSNDiXD+ACRAfDNMv --docker-email=tatsuhiro.0323@gmail.com
$ cd kubernetes
$ kubectl apply -f deploy_apply1.yaml 
以下で外部IPアドレスを調べる。
$ kubectl get svc
$ kubectl get pods
```

```
curl -X GET http://10.10.10.10/nice/get
```
## API Managementに登録

nicegetとしてAPI managementに登録。

![Screenshot from 2023-05-06 20-00-25](https://user-images.githubusercontent.com/58873037/236620265-c1044227-6723-4816-aee1-618f26d70e39.png)

![Screenshot from 2023-05-06 20-01-26](https://user-images.githubusercontent.com/58873037/236620303-2a0758b3-d804-47a3-8d17-127c1ea973a8.png)


add oprarionでFrontendを以下のように設定。

![Screenshot from 2023-05-06 20-02-20](https://user-images.githubusercontent.com/58873037/236620330-8d22c5fa-02ab-4a94-beff-e201d890ea81.png)


一番右のバックエンドで以下のように設定。（AKSの外部IP）
![Screenshot from 2023-05-06 20-18-07](https://user-images.githubusercontent.com/58873037/236620908-8996ea04-dcc7-4925-aaf6-f596b0a606d1.png)


サブスクリプションは製品ごとに出来上がる。
固定のAPIに作成してもよい。

```
a8dc2c53850744feab8275dc9895900b
c2b3be5b567e42d48de15a9e6eed78cc
```
どちらでもOK。
```
curl -X GET https://apim-wakayama-test-poc.azure-api.net/niceget/nice/get -H "Ocp-Apim-Subscription-Key: c2b3be5b567e42d48de15a9e6eed78cc"
```

![Screenshot from 2023-05-06 19-57-36](https://user-images.githubusercontent.com/58873037/236620284-638fc23c-e65c-44d2-b7db-716a69d2f360.png)



# SpringBoot

https://github.com/roshiwata/springboot-MyBootApp/tree/apply1-get

### 応用
https://github.com/roshiwata/-communication-springboot3

でpod間の通信

「」
