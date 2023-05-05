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

