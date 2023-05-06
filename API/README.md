# API
## Azure API Management
https://qiita.com/tetsuya-ooooo/items/3cd4d3d4c7fc1d180cda


機能一覧
API の保護と最適化
　ポリシーでアクセス制限、キャッシュ管理、データ変換、などなど
開発者エクスペリエンス
　ポータル利用で容易に開発
統一された管理/分析
　リアルタイムの使用量、応答性能、正常性分析
あらゆる配置に適応
　インターネットも VPN/ExpressRoute も
柔軟なスケーリング
　スケールアップ/スケールアウト
 
![image](https://user-images.githubusercontent.com/58873037/236199872-30cdec2d-ecb2-42e8-81bd-6787f3164d59.png)

API 構築
　API開発者の役割
 
API発行
　API管理者の役割

Product
　APIの管理単位
　ここにAPIを入れることで管理者開発者がそのAPIを呼び出せる
 ポリシー
　　API のリクエスト、レスポンスなどに対して順に実行される一連のステートメントの集まり
  ステートメントの例
  　リクエスト本文やレスポンス本文を XML から JSON に変換、API の呼び出し回数を制限するなど
  ポリシーの適用は
  　Global > Product > API の順


API利用
　API利用者の役割
 
 API を Product に含めたので、その API を呼び出すことができます。
 API のテストは、Azure ポータル、開発者ポータルから実施することができます。
 
 
 API 運用
　　API の利用状況、パフォーマンス、メトリックなどを分析する機能があります。

![image](https://user-images.githubusercontent.com/58873037/236207209-43665456-76b7-4097-8737-47a6f471461e.png)


![image](https://user-images.githubusercontent.com/58873037/236207372-d3a0f768-0dbf-42d8-a27d-0ac2fc482051.png)


Product
　Consoto product


API
　Demo Conference API
  Test API

Operation
　Test call
   Front end の設定とか応答の追加とか。
   応答→　{ "sampleField" : "test" }

APIの中にOperationがある


## openAPI swagger
https://zenn.dev/peraichi_blog/articles/01ges56ak79g2rket9wm27w9pc

https://panasonic-cns.udemy.com/course/rest-webapi-development/learn/lecture/19866400#overview


![image](https://user-images.githubusercontent.com/58873037/236363030-3a8953ff-a50a-45c4-b747-e137820e69d9.png)

![image](https://user-images.githubusercontent.com/58873037/236363909-87a06ae7-0892-4d67-9f15-01362c17c976.png)


![image](https://user-images.githubusercontent.com/58873037/236364981-db1a290c-8146-454c-a2b7-f3595e9a5821.png)

クエリパラメータ
　省略可能
パスパラメータ
　一意のパラメータを示すのに必要かどうか
 
ステータスコード
　200
 　ステータスコード
  　処理結果の概要


https://labs.play-with-docker.com/p/cha9kk2e69v000dub5ag
 
 ![image](https://user-images.githubusercontent.com/58873037/236365109-69dfd655-495e-4838-90ba-5fbd6e37972f.png)
 
 VScodeでOpenAPI環境を作成したいんだ
 
 https://zenn.dev/s_t_pool/articles/954dfe51b950c18d08e9
 
 ライセンスsリストp
 
 https://spdx.org/licenses/

スタブの作成として、講座内ではこれを使用しているが、セキュリティ考えるとローカルでdocker立ち上げるのがよい

https://labs.play-with-docker.com/p/cha9kk2e69v000dub5ag


![image](https://user-images.githubusercontent.com/58873037/236388297-ed8ab9d5-6776-4e06-9d99-068f1e273290.png)

![image](https://user-images.githubusercontent.com/58873037/236388367-3a8fd8f4-10cb-4153-8a46-b13c0339b9ae.png)




npm start



API management

https://www.slideshare.net/uesaka/azure-api-management-20203-230674743?from_action=save

![image](https://user-images.githubusercontent.com/58873037/236594600-a055d3a3-3d47-4475-bf64-1e6b02c314c1.png)

![image](https://user-images.githubusercontent.com/58873037/236594623-38c203d4-0b73-443c-9c79-f3212af92564.png)

![image](https://user-images.githubusercontent.com/58873037/236594769-b506e125-9657-4e6e-a536-0522a493bf89.png)

![image](https://user-images.githubusercontent.com/58873037/236594962-82ff3696-2d1e-4ae1-bcd1-2b6a946d3884.png)



restapi-springboot-may

の中のAKSにnicegetアプリをデプロイ

これをバックエンドとしてAPIを公開したいなあ。API managementで。



https://github.com/roshiwata/springboot-MyBootApp/tree/apply1-get

を用いてこれをAKSにデプロイ。




```
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp$ az acr login --name restapispringbootmayacr
Login Succeeded
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp$ mvn compile jib:build
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< com.tuyano.springboot:MyBootApp >-------------------
[INFO] Building MyBootApp 0.0.1-SNAPSHOT
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- resources:3.3.0:resources (default-resources) @ MyBootApp ---
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- compiler:3.10.1:compile (default-compile) @ MyBootApp ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- jib:2.8.0:build (default-cli) @ MyBootApp ---
[WARNING] 'mainClass' configured in 'maven-jar-plugin' is not a valid Java class: ${start-class}
[INFO] 
[INFO] Containerizing application to restapispringbootmayacr.azurecr.io/restapi-springboot-niceget...
[WARNING] Base image 'mcr.microsoft.com/java/jdk:17-zulu-alpine' does not use a specific image digest - build may not be reproducible
[INFO] Using credentials from Docker config (/home/tatsu/.docker/config.json) for restapispringbootmayacr.azurecr.io/restapi-springboot-niceget
[INFO] Using base image with digest: sha256:3ea5bc673c87bcdab2db8b84ad25d183235b4c3d3bdee0b350d6e4f49b53f505
[INFO] 
[INFO] 
[INFO] Container entrypoint set to [java, -cp, /app/resources:/app/classes:/app/libs/*, com.tuyano.springboot.MyBootAppApplication]
[INFO] 
[INFO] Built and pushed image as restapispringbootmayacr.azurecr.io/restapi-springboot-niceget
[INFO] Executing tasks:
[INFO] [==============================] 100.0% complete
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  24.707 s
[INFO] Finished at: 2023-05-06T16:54:06+09:00
[INFO] ------------------------------------------------------------------------
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp$ az aks get-credentials --resource-group restapi-springboot-may --name restapi-springboot-may-cluster
Merged "restapi-springboot-may-cluster" as current context in /home/tatsu/.kube/config
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp$ kubectl create secret docker-registry acr-credential-com --docker-server=restapispringbootmayacr.azurecr.io --docker-username=restapispringbootmayacr --docker-password=BN+9GIvTWEBKgwrXFQ8Vbc9GDxJvEiAUp8gfSNDiXD+ACRAfDNMv --docker-email=tatsuhiro.0323@gmail.com
secret/acr-credential-com created
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp$ cd kubernetes
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ ls
deploy_apply1.yaml
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ kubectl apply -f deploy_apply1.yaml 
service/niceget-apply created
deployment.apps/niceget-apply created
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ kubectl get svc
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP      10.0.0.1       <none>        443/TCP        30m
niceget-apply   LoadBalancer   10.0.154.227   <pending>     80:32115/TCP   11s
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ kubectl get svc
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
kubernetes      ClusterIP      10.0.0.1       <none>          443/TCP        30m
niceget-apply   LoadBalancer   10.0.154.227   20.78.115.231   80:32115/TCP   14s
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
niceget-apply-7b5b955854-z4t4f   1/1     Running   0          36s
tatsu@tatsu-g15-ubuntu:~/07_springboot/22-5-5-2/springboot-MyBootApp/kubernetes$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
niceget-apply-7b5b955854-z4t4f   1/1     Running   0          39s
```



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

