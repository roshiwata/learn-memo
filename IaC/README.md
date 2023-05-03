# IaC
## Azure Resource Managerについて  
参考：https://learn.microsoft.com/ja-jp/azure/azure-resource-manager/management/overview  

Azure のデプロイおよび管理サービス

### ARMテンプレート
参考：https://learn.microsoft.com/ja-jp/azure/azure-resource-manager/templates/overview  

概要：
- Azure ソリューション用にインフラストラクチャをコードとして実装するためのもの
- プロジェクトのインフラストラクチャと構成が定義

形式：JSON

参考：https://aireblog.com/how-to-deploy-arm-template/

概要：
- 作成したARMテンプレートは繰り返し使用できる
- 同じ設定の仮想マシンを繰り返しデプロイ
- 複数のリソースを一括デプロイ

ARMテンプレートをAzureにデプロイする方法：
- ローカルテンプレートをデプロイ
- メインテンプレートとリンク済みテンプレートをデプロイ
- 継続的デプロイパイプラインでデプロイ


【ローカルテンプレートをデプロイ】
Visual StudioでARM Tools拡張をインストール

sample.json
"arm"通したらVScodeで自動的に作成される
これでデプロイされた。
C:\Users\tatsu\OneDrive\12_github>az deployment group create --resource-group "wakayama-poc-rg" --name "iacdeploytest01" --template-file "sample.json"

![image](https://user-images.githubusercontent.com/58873037/235415328-e2892409-519b-4afe-9a54-97d336283390.png)

【メインテンプレートとリンク済みテンプレートをデプロイ】

C:\Users\tatsu\OneDrive\12_github>az storage account create --resource-group "StorageAccountRG" --name "storageaccount4swaaa" --location "japaneast" --sku "Standard_LRS"



## Bicep の方がいいらしい 
https://qiita.com/tetsuya-ooooo/items/9812d30d55b72044fd4d

az bicep install

bicepをデプロイすると内部的にARMテンプレにコンパイルされとる！
bicepの方が見やすいし書きやすい

## GitHubと連携させる。

https://learn.microsoft.com/ja-jp/training/modules/build-first-bicep-deployment-pipeline-using-github-actions/3-exercise-create-run-basic-workflow


そもそものGitHubActionsのお勉強もしよう

- runs-onはdocker imageを指定してるよ




jqのインストール
https://zenn.dev/unsoluble_sugar/articles/e47b37b04dd1153d5b29
https://stedolan.github.io/jq/


https://learn.microsoft.com/ja-jp/training/modules/build-first-bicep-deployment-pipeline-using-github-actions/5-exercise-create-github-secret?pivots=cli  
https://chat.openai.com/  
https://github.com/roshiwata/IcA-test/actions  
https://portal.azure.com/#cloudshell/  

ワークロードIDの作成、GitHubへの紐づけ
```
githubOrganizationName='roshiwata'
githubRepositoryName='IcA-test'
applicationRegistrationDetails=$(az ad app create --display-name 'IcA-test')

applicationRegistrationObjectId=$(echo $applicationRegistrationDetails | jq -r '.id')
applicationRegistrationAppId=$(echo $applicationRegistrationDetails | jq -r '.appId')

az ad app federated-credential create \
   --id $applicationRegistrationObjectId \
   --parameters "{\"name\":\"IcA-test\",\"issuer\":\"https://token.actions.githubusercontent.com\",\"subject\":\"repo:${githubOrganizationName}/${githubRepositoryName}:ref:refs/heads/main\",\"audiences\":[\"api://AzureADTokenExchange\"]}"

resourceGroupResourceId=$(az group create --name ToyWebsite --location japaneast --query id --output tsv)
az ad sp create --id $applicationRegistrationObjectId
az role assignment create --assignee $applicationRegistrationAppId --role Contributor --scope $resourceGroupResourceId
```

```
echo "AZURE_CLIENT_ID: $applicationRegistrationAppId"
echo "AZURE_TENANT_ID: $(az account show --query tenantId --output tsv)"
echo "AZURE_SUBSCRIPTION_ID: $(az account show --query id --output tsv)"
をGitHubシークレットに登録
```
```
うまくいかない。。
umakuikan/
に格納。pushしてActionsを見ると以下のエラー。

Run azure/login@v1
Using OIDC authentication...
Federated token details: 
 issuer - https://token.actions.githubusercontent.com 
 subject claim - repo:roshiwata/IcA-test:ref:refs/heads/main
/usr/bin/az cloud set -n azurecloud
Done setting cloud: "azurecloud"
Error: : No subscriptions found for ***.

Error: Az CLI Login failed. Please check the credentials and make sure az is installed on the runner. For more information refer https://aka.ms/create-secrets-for-GitHub-workflows
```
とりあえずおいておいて以下実施
https://blog.beachside.dev/entry/2021/08/27/183000


※1
```
az ad sp create-for-rbac --name "github actions demo 1" --role contributor  --scopes /subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/iac-github --sdk-auth
をすると、
(MissingSubscription) The request did not have a subscription or a valid tenant level resource provider.
Code: MissingSubscription
Message: The request did not have a subscription or a valid tenant level resource provider.
```


git bashだとダメでそれ以外だとOKだった。
以下の記載もあったのだが、C:/Program Files/Gitという記載があった。よくわからんPathが切られていた。

```
Creating 'contributor' role assignment under scope 'C:/Program Files/Git/subscriptions/4463a0e9-df15-4e30-ac13-45e4e74bb39f/resourceGroups/iac-github'
  Role assignment creation failed.

  role assignment response headers: {'Cache-Control': 'no-cache', 'Pragma': 'no-cache', 'Content-Type': 'application/json; charset=utf-8', 'Expires': '-1', 'x-ms-failure-cause': 'gateway', 'x-ms-request-id': '39c202ef-95ca-4056-9744-8fbea9c69114', 'x-ms-correlation-request-id': '39c202ef-95ca-4056-9744-8fbea9c69114', 'x-ms-routing-request-id': 'JAPANEAST:20230501T232515Z:39c202ef-95ca-4056-9744-8fbea9c69114', 'Strict-Transport-Security': 'max-age=31536000; includeSubDomains', 'X-Content-Type-Options': 'nosniff', 'Date': 'Mon, 01 May 2023 23:25:14 GMT', 'Content-Length': '135'}
```

ということで、
https://learn.microsoft.com/ja-jp/training/modules/build-first-bicep-deployment-pipeline-using-github-actions/5-exercise-create-github-secret?pivots=cli  
の続き！！！
（https://github.com/roshiwata/IcA-test）

```
※
az ad app create --display-name 'IcA-test'
ではサービスプリンシパルは作成されないが、以下で作成されるのだ。
az ad sp create --id <application-id>
ただ、この作り方だと「すべてのアプリケーション」でしか作成されないのだ。。
az ad sp create-for-rbac：アプリの登録もされるしエンタープライズにも追加されるが、エンタープライズの方は「すべてのアプリケーション」でしか出てこない。
portalのアプリの登録から登録：アプリの登録もされるしエンタープライズにも追加される。「エンタープライズアプリケーション」として出てくる
```



az role assignment create --assignee $applicationRegistrationAppId --role Contributor --scope $resourceGroupResourceId

リソースグループごとはOK

手動でデプロイ（GitHub上でクリック）⇒
```
on: [workflow_dispatch]
```
自動デプロイ
```
on:
  push:
    branches:
      - main
```
pushされてなおかつdeploy/**　
が変更されていたら自動デプロイ
```
on:
  push:
    branches:
      - main
    paths:
      - 'deploy/**'
```
pushされてなおかつdeploy/**　もしくは　test/**
が変更されていたら自動デプロイ

```
on:
  push:
    branches:
      - main
    paths:
      - 'deploy/**'
      - 'test/**'
```


### 一応これもやろう
https://blog.beachside.dev/entry/2021/08/27/183000  
github-actions-test
Credentials でちょっとすっきりできる
https://github.com/roshiwata/github-actions-test






### AKS/ ACRのデプロイ
これでやろう。
https://zenn.dev/takoeno/articles/56f3ac56fcc4f9

AKSはマネージドIDを自分で作らないといけない（ユーザー割り当てマネージドID）



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

![image](https://user-images.githubusercontent.com/58873037/235725330-945df66a-8cc6-4d48-b91b-69f2c63bc855.png)



https://stackoverflow.com/questions/74321138/azure-role-assignment-badrequestformat


![image](https://user-images.githubusercontent.com/58873037/235725488-30a826e2-96f4-4444-afdc-630a50cff2b7.png)


![image](https://user-images.githubusercontent.com/58873037/235733700-2c5d9806-f7bf-48d5-8feb-7d61d76bf771.png)


これ（github-actions-aks-all　e0efeba8-bd95-4379-ac84-f2e8e29d98c5）の権限が足りない気がする。

このエラーは、指定されたクライアントに必要な権限が与えられていないことを示しています。指定されたクライアント（ e0efeba8-bd95-4379-ac84-f2e8e29d98c5 ）は、Microsoft.Authorization/roleAssignments/writeアクションを実行する権限を持っておらず、デプロイメントが失敗しました。

![image](https://user-images.githubusercontent.com/58873037/235811126-b494ec86-6f64-4d59-a35d-a6d9625a2abd.png)


所有者はロール割り当ての権限がありそう。。！

![image](https://user-images.githubusercontent.com/58873037/235811458-4decd01a-6b3b-4fdd-9e31-080667611cf6.png)


権限を変えたらこのエラーに変わった。サブネットの指定方法に問題がありそう！
![image](https://user-images.githubusercontent.com/58873037/235817844-66f553de-f73b-4e26-a49c-e35285b089b2.png)

#49 Change how to write subnet
のように書き方を変えるとOK。

そういえばロールの割り当ての有無を確認しよう
あり
![image](https://user-images.githubusercontent.com/58873037/235822758-4c63d8e0-63d0-4886-9694-e8a8c58a41ef.png)

![image](https://user-images.githubusercontent.com/58873037/235822713-0ece97c1-56e5-4361-9b32-4ef168a0961e.png)
![image](https://user-images.githubusercontent.com/58873037/235824432-dee76d99-bc04-473f-8e52-27c668d22a34.png)


なし
![image](https://user-images.githubusercontent.com/58873037/235825192-8695ab17-e5db-4bc0-a114-2ae795c293a3.png)

![image](https://user-images.githubusercontent.com/58873037/235825246-6fee7933-2893-4316-99ed-83153a9848e1.png)

そういうことである！
リソースは一度消さなかったらあり⇒なしにはできなかった。。なし⇒ありにはできた！
リソースの設定とかデプロイは「上書き」なんだね
