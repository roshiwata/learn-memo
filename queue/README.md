# Azureのメッセージングサービス4種
https://qiita.com/gtracker64/items/b54c43ce5fe41fd4bd47


## メッセージ系
- https://learn.microsoft.com/ja-jp/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted
- https://www.serverless360.com/blog/azure-event-hubs-vs-service-bus

### Queue Storage
- シンプルなメッセージキュー
- AzureStorageを利用
- データ量が多い場合
  - 無制限のキューサイズ
- RESTベース
- ストレージの容量で課金
- 単一の送信先
- キューに対して実行されたすべてのトランザクションのサーバー側のログが必要


## Service Bus
### キュー：
![image](https://github.com/roshiwata/learn-memo/assets/58873037/44ced4f1-f5b1-460b-8199-4052dc2b1a5d)

- 単一の受信者
- 柔軟なキュー
- 容量は最大80GB
- 以下のいずれかを選択可能
  - At-Least-Once：最低1回（重複あり、損失なし）
    - 少なくとも1回。つまり1回は確実に配信されるが、重複(２回以上同じメッセージ)がありうる。
  - At-Most-Once：最大1回（重複なし、損失あり）
    - 最大1回。つまり0回=配信されないことがある。重複はない。
- トランザクションあり（複数メッセージをグループ化できる）
- 先入れ先出し法（FIFO）の保証
- 信頼性向上
  - 非同期に処理できる
- 自動重複検出をサポートしている
- AMQPを使う場合
- 最終的には、キューベースのポイントツーポイント通信から、パブリッシュ/サブスクライブ メッセージング パターンへの移行を想定している場合。 このパターンは、追加の受信者 (サブスクライバー) の統合を可能にします。 各受信者は、キューに送信されたメッセージの一部またはすべての独立したコピーを受け取ります。
- ソリューションで、バッチ メッセージの発行および使用が必要な場合。
- 
- ソリューションで、キューをポーリングせずにメッセージを受信できる必要がある場合。 Service Bus では、Service Bus がサポートする TCP ベースのプロトコルを使用し、長いポーリングの受信操作を使用することによってこれを実現できます。
- アプリケーションでメッセージを実行時間の長い並列ストリームとして処理する必要がある場合 (メッセージは、それ自体の [セッション ID] プロパティを使用してストリームに関連付けられます)。 このモデルでは、処理を行うアプリケーションの各ノードは、メッセージではなくストリームに対して競合します。 処理を行うノードにストリームが渡されると、そのノードはトランザクションを使用してアプリケーション ストリームの状態を確認できます。
- ロールベースのアクセス モデルを提供して、キューの送信側と受信側に異なる権限/アクセス許可を与える必要がある場合。


### トピック：
![image](https://github.com/roshiwata/learn-memo/assets/58873037/79b11ba0-17fb-4172-9416-81c03fccd913)

- 複数の受信者
- 複数クライアントからのサブスク・処理が可能

## Queue Storage or Service Bus
- Queueストレージを選択（最も単純)
  - データ量が多い(キューのサイズが80GBを超える)
  - 簡単にコーディングできるシンプルなキューがほしい

- Service Busキューを選択
  - At-Most-Once保証(最大1回、重複なし)が必要
  - 順序保証(FIFO)が必要
  - トランザクションが必要（全て処理される or 何も処理されない)
  - データ量が比較的少ない(キューのサイズが80GB未満)

- Service Busトピックを選択（最も複雑）
  - 複数のサブスクライバー（受信者）が必要


## 検討事項
- どれくらいの容量使うのだろう。80GBで十分か？
  - 十分だと思う。（1出荷指示_KB　×　_指示数）
- At-Most-Onceがいるのか？
  - いらん
- FIFOが保証必要か？
  - 必要
- メッセージが到着したら通知してくれる機能が必要がどうか
  - 一旦不要そうだがあったほうがよさそう
⇛「Service Bus」


# イベント系
## Event Grid：
- シンプルなイベント
- イベントのパブリッシュ/サブスクライブモデルに基づくイベント配信サービス
- Pull型　サブスクライバがさばききれないとエラーになる
- Event Grid はサブスクライバに配信された直後にメッセージはトピックから削除される。またサブスクライバでエラーが起きてもメッセージは戻らない。
- メッセージの順序を保証しない
- イベント駆動のリアクティブ・プログラミングを可能にするメッセージのイベントハンドラ。



## Event Hubs：
  - 複数の送信者受信者
    - 送信者は高スループットでメッセージを渡すことができる
  - 高速度で大量のデータを取り入れる独自の機能もある
    - １ｓに数万？
- 高スループット、多数のパブリッシャー、セキュリティ、回復性を持つイベント
https://blog.nextscape.net/archives/Date/2015/08/eventhubs
- ビッグデータ（データ・パイプライン）寄り。

- Publisher/Subscriber
- Producer/Consumer
- 発行者
- Broker

- EventHubsは「イベントの貯蔵庫」
　- ある一定期間イベントという名の文字列を貯め込む
    - イベントを投げるやつのことを以下のように言い換えている
      - Publisher/ Producer/ 発行者（Producerの訳）
    - 受け取る側のことを
      - Subscriber/ Consumer
    - EventHubsのように間に入ってうまいことやってくれる仲介者
      - Broker
- サイズ制限が結構厳しい
- イベントは受信しても消えない
  - 〇〇日間保持するという設定をできる。その日数立たないとイベントは消えない
    - キューとは少し感覚が違う

- EventHubsの構造
  - 内部にパーティションを持つ
    - １〜３２　（初期値８）
    - どのパーテションに格納するかは決められるがイベントハブに任せることができる
    - 並列で処理させるためにそうなっている

![Screenshot from 2023-05-21 15-56-38](https://github.com/roshiwata/learn-memo/assets/58873037/3a8085ff-f64a-47de-a6f6-607c94cec889)

- 
https://blog.nextscape.net/archives/Date/2015/08/eventhubs2

- EventHubsに格納されたイベントは、自分で取りに行くのではなく、通知されるプッシュ型である
- EventHubsに格納されたイベントは外側から消すことはできません。保持期間が過ぎるのをじっと待つだけです。

![image](https://github.com/roshiwata/learn-memo/assets/58873037/c38d1389-1f99-41af-8b07-4ecbf0da0b55)

![image](https://github.com/roshiwata/learn-memo/assets/58873037/4105f355-c06c-4841-82cd-0623ae29cb98)

![image](https://github.com/roshiwata/learn-memo/assets/58873037/75ed14e0-cd47-4792-a7dc-217532087407)





- メッセージは特定の受信者に直接送信され、データの伝達や要求と応答のパターンに使用されます。
- イベントは興味のあるコンポーネントに対してブロードキャストされ、システム内の事象や状態の変化を通知するために使用されます。

## Event Grid vs Event Hubs vs Service Bus
- https://www.youtube.com/watch?v=NCYaUcoaAqo

Event Grid
- Azureのサービス間の通信
- すでに存在するAzureサービスを前提に稼働する
- リアクティブプログラミングで使用される。（高速）

Event Hubs



## Azure Event Hub, Event Grid, and Service Bus - Made Simple
- https://www.youtube.com/watch?v=hqtUZNmVVdA
- Queue
  - 1対1  
  - 1つのあぷりによってのみ消費される

イベントとメッセージの違い
- イベント
  - 公開または発行する情報の一部
  - 誰がそれぞ受信するかは気にしない
  - 通常、イベントは状態変化のようなもの
  - イベントの変化に対して処理をするが、誰が何をしたのかは気にしない
  - メッセージの送信者にメッセージが処理されたことを確認してもらいたい場合
    - 公開者はそのメッセージが処理されることを期待している
- メッセージ


