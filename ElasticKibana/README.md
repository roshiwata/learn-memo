## Elastic Kibanaを使ってみよう  
https://zenn.dev/komisan19/articles/f752c0d0299a92  
でテスト  

### こっちのほうがわかりやすい。ノードとかの概念がわかりやすい  
https://qiita.com/kiyokiyo_kzsby/items/344fb2e9aead158a5545　　
でもエラーで実行できなかったから上のURLの環境を用いる。  

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


