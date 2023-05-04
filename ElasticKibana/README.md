## Elastic Kibanaを使ってみよう  
https://zenn.dev/komisan19/articles/f752c0d0299a92  
でテスト  

### こっちのほうがわかりやすい。ノードとかの概念がわかりやすい  
https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545　　
でもエラーで実行できなかったから上のURLの環境を用いる。  

s
### 起動確認  
```
curl -X GET "localhost:9200/_cat/health?v&pretty"
```

### 各ノードのステータスを取得  
```
curl -X GET "localhost:9200/_cat/nodes?v&pretty"
```


### 例えば顧客情報を管理するcustomerindexを作成する場合は以下のようになります。  
```
curl -X PUT "localhost:9200/customer?pretty&pretty"
```

### indexのステータスは以下で確認することができる。  
```
curl -X GET "localhost:9200/_cat/indices?v&pretty"
```

```
health status index                           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .geoip_databases                MD0OndgqT_6ITiMM9nnLvQ   1   0         42            0     40.6mb         40.6mb
green  open   .kibana_task_manager_7.16.2_001 d50IfYz2S1SPufikNGKurg   1   0         17          621    141.2kb        141.2kb
green  open   .apm-custom-link                u-f2vm7XTzmLotBLO6oyrw   1   0          0            0       226b           226b
green  open   .kibana_7.16.2_001              rSeM08hITOyBy-WbQb6fUg   1   0         15            0      2.3mb          2.3mb
yellow open   customerkun                     j9pa3i41Qqiydv7cKkDO-w   1   1          0            0       226b           226b
green  open   .apm-agent-configuration        qBnikg_9Roaw-UMPcesTow   1   0          0            0       226b           226b
yellow open   customer                        BXOfAN6PTgWYuHfxC_uPeg   1   1          0            0       226b           226b
```

```
health：indexの状態。
green：異常なし。
yellow：レプリカシャード作成失敗。
red：プライマリシャード作成失敗。
pri：プライマリシャードの数。今回は1つ。
rep：レプリカシャードの数。今回は1つ。
docs.count：保存しているドキュメント数。現段階ではドキュメントを登録していないので0。
```

なぜかyellow。。。  


これでやってみよう。  
https://qiita.com/mug-cup/items/ba5dd0a14838e83e69ac  

sudo sysctl -w vm.max_map_count=262144
しないと以下のエラーになる。


### 起動確認  
```
curl -X GET "localhost:9200/_cat/health?v&pretty"
```

### 各ノードのステータスを取得  
```
curl -X GET "localhost:9200/_cat/nodes?v&pretty"
```


### 例えば顧客情報を管理するcustomerindexを作成する場合は以下のようになります。  
```
curl -X PUT "localhost:9200/customer?pretty&pretty"
```

### indexのステータスは以下で確認することができる。  
```
curl -X GET "localhost:9200/_cat/indices?v&pretty"
```

### https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545  でいろいろお試し

document追加  
```
curl -X PUT "localhost:9200/customer/_doc/12?pretty&pretty" -H 'Content-Type: application/json' -d'{
  "namejjkk12": "Johnjjkk Doe1dddddd2"
}
'
```
確認  
```
curl -X GET "localhost:9200/customer/_doc/12?pretty&pretty"
```


更新
```
curl -X POST "localhost:9200/customer/_update/1?pretty&pretty" -H 'Content-Type: application/json' -d'
> {
>   "doc": { "name": "Jane Doe", "age": 20 }
> }
> '

```



## エージェントを立てて、、をやってみる

https://qiita.com/ryuichi1208/items/910764e721c47ba31386

プロセス	ポート番号
Elastisearch	9200
Kibana	5201
Logstash	5044

やっぱやめ。以下で行う。
https://qiita.com/j-yama/items/98542cbb458ca723c766

![Screenshot from 2023-04-20 17-07-13](https://user-images.githubusercontent.com/58873037/233301722-1797e836-4b50-4f80-866e-0da74476150f.png)

Logstash, Beats(データ転送プロダクト)がElasticsearchにデータを送信。
Elasticsearch内部のデータを使ってKibanaでデータを確認したり、ダッシュボードを表示



