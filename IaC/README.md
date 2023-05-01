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
