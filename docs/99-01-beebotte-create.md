# Beebotte で MQTT ブローカー作成

Beebotte で MQTT ブローカー作成する場合の手順です。

## Beebotte

![7eab328829ff25dfcf38f30d1cb74938](https://i.gyazo.com/7eab328829ff25dfcf38f30d1cb74938.png)

<a href="https://beebotte.com/" target="_blank">Beebotte</a> は、リアルタイム接続オブジェクト用のクラウドプラットフォームでということで、IoT にも相性がよく REST、WebSocket、MQTTをサポートする豊富なAPIがあります。

![d0ec5ebe055cb71d1dcdca839fce5428](https://i.gyazo.com/d0ec5ebe055cb71d1dcdca839fce5428.png)

クレジットカード不要で始めることができるのも大きなメリットでしょう。

2022 年 10 月現在、Free プランでも、

> Unlimited Channels
> 50,000 Messages/day
> 5,000 Persistent Messages/day
> 3 Months History
> SSL Encryption

日本語に訳してみると、

> 無制限のチャンネル
> 50,000メッセージ/日
> 5,000永続メッセージ/日
> 3ヶ月の履歴
> SSL暗号化

なので、ホビーレベルであれば、制限内でも十分に使えます。

## Beebotte でチャンネル作成

アカウント登録が済んでいる前提で進めます。

![b0eda154682c29f1b96a05dda9cb64b2](https://i.gyazo.com/b0eda154682c29f1b96a05dda9cb64b2.png)

右のメニューから Channels をクリックして My Channels を表示します。

Create New ボタンをクリックします。

![bcf322e94fa4f6b2d71c2edb07ddb1a6](https://i.gyazo.com/bcf322e94fa4f6b2d71c2edb07ddb1a6.png)

Create a new channel ページに移動するので、

![24a8ca8fcd81458f081307065f231522](https://i.gyazo.com/24a8ca8fcd81458f081307065f231522.png)

- Channel Name
    - handson
- Resource name
    - data

と設定して、Create channel ボタンをクリックします。

## リソースグループ 設定確認と test チャンネルの Channel Token をメモ

登録が済むとチャンネル一覧画面に移動します。

![5938c190316d4d7d42e77b3c92d81225](https://i.gyazo.com/5938c190316d4d7d42e77b3c92d81225.png)

My Channels に、このようにいま設定したチャンネルが表示されるのでクリックします。矢印の部分がタイトルです。

![e7f7f0fed71b49eed851f21b533f2cd4](https://i.gyazo.com/e7f7f0fed71b49eed851f21b533f2cd4.png)

矢印の部分の赤文字の Channel Token をメモしつつ、Configured resources に先ほど設定したリソースグループ res が設定されていることを確認します。

これで、やりとりするための MQTT 設定は完了です。

## MQTT接続設定のおさらい

<a href="https://beebotte.com/docs/mqtt" target="_blank">Beebotte MQTT Support</a> で MQTT の設定が確認できます。

MQTT のブローカー設定としては以下のように設定します。

- MQTTブローカー先
    - mqtt.beebotte.com
- ポート
    - 1883
- 接続するユーザー名
    - さきほどメモした Channel Token を使う
- 接続するパスワード
    - （なにも書かない）
- 接続トピック
    - `チャンネル名`/`リソースグループ名` を設定
    - handson チャンネルで data  リソースグループの場合は `handson/data`