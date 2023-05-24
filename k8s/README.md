~/.kube/config


```
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/wakayama/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Tue, 23 May 2023 14:35:56 UTC
        provider: minikube.sigs.k8s.io
        version: v1.30.1
      name: cluster_info
    server: https://192.168.49.2:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Tue, 23 May 2023 14:35:56 UTC
        provider: minikube.sigs.k8s.io
        version: v1.30.1
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/wakayama/.minikube/profiles/minikube/client.crt
    client-key: /home/wakayama/.minikube/profiles/minikube/client.key
 ```
  
 ![image](https://github.com/roshiwata/learn-memo/assets/58873037/33a3e906-6648-4095-922f-bfeb2629c9da)


Pod：コンテナをグループ化
Pod　管理上の基本単位
　仮想NICを共有し、仮想ホストのような働きをする
 
 
ワーカーノード






![image](https://github.com/roshiwata/learn-memo/assets/58873037/bee6900a-86d5-4fff-aa54-2f623af01e54)

 
 
 
 
 
 
 
 
 Serviceとはなんぞや
 ![image](https://github.com/roshiwata/learn-memo/assets/58873037/53be77ea-4d6a-412b-a71e-0f482a2e989c)


PodをServiceとして公開するのが基本的な流れ

サービスの名前がわかればPodに直接接続できる

Podからサービスにする
kubectl expose pod helloworld --type ClusterIP --port 8080 --name helloworld-clusterip

Podとサービスは同じPort番号にするのが普通
kubectl expose pod helloworld --type ClusterIP --port 8080 --name helloworld-clusterip


 NodePoer Serviceはクラスタ外へのPodの公開ができる
 
 
 
 
 
 クラスター内からしかアクセスできないサービスとクラスター外からもアクセスできるサービスに分ける感じやね
 
 
 minikube service helloworld-nodeport --url
 
 
 Nodeも起動したり停止したりするから実は安定的ではない
 
 
 LoadBalancer
 クラウドプロバイダのL4ロードバランサのDNSから、各ノードの特定のポートにRoutingしてPodにアクセスできる
 
 LBは高い
 LBはhttpレベルの振り分けができない
 
 Ingress（L7のロードバランサー）
 クラスター外部からURLのホスト・パスでサービスへの振り分けができる
 
