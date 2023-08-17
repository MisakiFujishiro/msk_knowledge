# MSK

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/amazon-msk.png" style="width: 70%;">
</div>

---
## MSKの概要
----
### Topic/Partition
<div style="font-size: 0.8em;">
    <p>
        生成されたメッセージはKafkaのTopicに送信される<br>
        Topicはpartitionと呼ばれる単位に分割され<br>
        partitionの中にデータが格納される。
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-3.jpg" style="width: 70%;">
</div>


---
## MSKの型
----
### ストリームデータ型
<div style="font-size: 0.8em;">
    <p>
        ストリームデータは大規模かつ連続的に生成され、<br>
        リアルタイムかつ順序性を担保して処理されることで<br>
        価値を生むことが多い
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-4.jpg" style="width: 70%;">
</div>


----
### Pub-Sub
<div style="font-size: 0.8em;">
    <p>
        Producerが生成したデータを<br>
        複数のConsumerが受信することができる
</p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-5.jpg" style="width: 70%;">
</div>


----
### Pull型

<div style="font-size: 0.8em;">
    <p>
        データは一度kafkaに格納される<br>
        データの取得は、consumerが能動的に行う
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-6.jpg" style="width: 70%;">
</div>

----
### まとめ
<div style="font-size: 0.8em;">
    <p>
        連続したリアルタイムデータであるストリームデータを扱い、<br>
        同じデータを複数のConsumerが受信可能なPub-Sub型であり、<br>
        Consumerが能動的にデータを取得するpull型のサービス
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-7.jpg" style="width: 70%;">
</div>




---
## Consumerグループ
----
### Pub-Subモデルの実現
<div style="font-size: 0.8em;">
    <p>
        Pub-Subモデルを実現するために、Consumer側で所属するConsumer-Groupを定義する<br>
        Consumer-Group単位でメッセージを受信する
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-8.jpg" style="width: 70%;">
</div>





---
## offset
----
### ストリーム型データの扱い
<div style="font-size: 0.8em;">
    <p>
        ストリーム型のデータの処理順序を担保するため<br>
        offsetと呼ばれるメッセージのインデックスが付与される<br>
        kafkaではpartition単位でoffsetが払い出される
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-9.jpg" style="width: 50%;">
</div>

----
### offsetの種類
<div style="font-size: 0.8em;">
    <p>
        Log-End-Offset:partitionの末尾のメッセージの番号<br>
        Current-Offset:consumerが読み込んだメッセージの番号<br>
        commit-Offset:consumerがコミットしたメッセージの番号
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-10.jpg" style="width: 70%;">
</div>


----
### 正常処理時のoffset
<div style="font-size: 0.8em;">
    <p>
        consumerはcommit-offsetを確認<br>
        後続メッセージを取得してcurrent-offsetを更新する<br>
        正常終了した場合はcommit-offsetを更新する
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-11.jpg" style="width: 70%;">
</div>


----
### 処理失敗時のoffset
<div style="font-size: 0.8em;">
    <p>
        後続メッセージを取得して処理中に失敗した場合、<br>
        再起動した、consumerはcommit-offsetを再確認し、<br>
        その後続のメッセージを再度取得する
        <br>
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-12.jpg" style="width: 50%;">
</div>





---
## offsetの管理
----
### __consumer_offsets
<div style="font-size: 0.8em;">
    <p>
        各トピック、各consumer-gruoupごとのcommit-offsetが<br>
        __consumer_offsetsに書き込まれている<br>
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-13.jpg" style="width: 70%;">
</div>



----
### __consumer_offsetsの削除ポリシー
<div style="font-size: 0.6em;">
    <p>
        consumerGroupが活動しない期間が長いと、__consumer_offsetsの情報を削除する<br>
        この期間はoffsets.retention.minutesで指定さている(デフォルト7日)<br>
        commit-offsetが削除された際の挙動はauto-offset-resetの定義次第
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-14.jpg" style="width: 70%;">
</div>


----
### auto-offset-reset:latest
<div style="font-size: 0.6em;">
    <p>
        consumerが既存のoffsetを見つけられない場合どのメッセージから消費するかの設定<br>
        latest:最新のoffsetからメッセージを処理、新しく到着するメッセージのみを消費する<br>
        破棄されてから、Consumerが起動されるまでに投入されたデータは処理されない
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-15.jpg" style="width: 70%;">
</div>

----
### auto-offset-reset:earliest
<div style="font-size: 0.6em;">
    <p>
        consumerが既存のoffsetを見つけられない場合どのメッセージから消費するかの設定<br>
        earliest:トピックの最初のメッセージから消費する<br>
        破棄されてから、consumerが起動すると、全ての処理が再実行される
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-16.jpg" style="width: 70%;">
</div>



----
### Lagについて
<div style="font-size: 0.6em;">
    <p>
        Topic単位で未処理データ数をLog-End-Offsetとcommit-offsetの差の和をとった<br>
        sumoffsetlagというメトリクスで取得できる<br>
        sumoffsetlagは__consumer_offsetsを参照し算出するためconsumerが0台でも算出可能
    </p>
</div>

<div style="display: flex; justify-content: space-around;margin-top: -20px;">
  <img src="img/Msk-17.jpg" style="width: 70%;">
</div>
