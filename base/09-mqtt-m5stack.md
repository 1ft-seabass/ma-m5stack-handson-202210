# MQTT を使って M5Stack の遠隔ディスプレイ制御をしよう

## 今回のプログラムはどのように動くか

講師が事前に <a href="https://beebotte.com/" target="_blank">Beebotte</a> というサービスで MQTT ブローカーの設定を、みなさんの M5Stack を講師側から遠隔操作します。

半角英数字で MQTT ブローカーへメッセージが送信してみます。

![1f9239ae85328a52a26fc8c2998cf6e5](https://i.gyazo.com/1f9239ae85328a52a26fc8c2998cf6e5.jpg)

M5Stack 側で MQTT ブローカーからこのメッセージを受け取るので M5Stack にメッセージが届きます。

この例は `Hello!` を送った例です。

![a7cf2d4e7809738029ea2dcfe40522d6](https://i.gyazo.com/a7cf2d4e7809738029ea2dcfe40522d6.png)

実際の動きはこのようなイメージです。

## Beebotte

<img width="1481" alt="2022-02-28 14.01.39.jpg (153.3 kB)" src="https://img.esa.io/uploads/production/attachments/3062/2022/02/28/8131/9bb5e40d-f1d5-44cf-bce0-5b6f4d0ebd0f.jpg">

<a href="https://beebotte.com/" target="_blank">Beebotte</a> は、リアルタイム接続オブジェクト用のクラウドプラットフォームでということで、IoT にも相性がよく REST、WebSocket、MQTTをサポートする豊富なAPIがあります。

<img width="1194" alt="image.png (69.0 kB)" src="https://img.esa.io/uploads/production/attachments/3062/2022/02/28/8131/f7db0fc3-b2aa-40e9-a51c-97affabd8c1f.png">

2022 年 2月現在、Free プランでも、

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

## 今回は作成済み

チャットに講師が事前に <a href="https://beebotte.com/" target="_blank">Beebotte</a> で作った MQTT ブローカーの設定をお伝えします。

## 自分でつくりたい方は

今後、ハッカソンで自分でつくりたい方がいるかもしれません。

そのときは、Beebotte のアカウント登録を済ませてから、こちらの記事で作成してみてください。

[Beebotte で MQTT ブローカー作成](99-01-beebotte-create.md)

## ライブラリを Arduino IDE にインストール

ここから Arduino IDE の作業です。

### MQTT のやり取りできるライブラリ PubSubClient をインストールする

この作業はインストールなので、一度だけ対応すれば OK です。

以前の温度湿度センサーは GitHub に行かないとライブラリがなかったですが、これは、とてもやりやすく、Arduino のライブラリ管理から検索してインストールできます。

![b7636560b07f1290bb03a0ac8044842a](https://i.gyazo.com/b7636560b07f1290bb03a0ac8044842a.png)

ツール > ライブラリを管理 でライブラリマネージャを起動します。

![image](https://i.gyazo.com/6a82b0efb54bc4a78c3faf643f8b8494.png)

`PubSubClient` で検索して、`完全同名` のライブラリを探します。

![image](https://i.gyazo.com/1e50c2b9a016c7a733822ae9d40bc020.png)

マウスを乗せると、バージョンとインストールのボタンが右下に出るので `2.8` のバージョンを指定してインストールボタンをクリックします。

![image](https://i.gyazo.com/5d4ab17b5a0bf7dc323f20ff5efedf3b.png)

インストールできたら、ひょっとすると、リストが一番上に戻ってしまうかもしれませんが、根気よく `PubSubClient` に移動して INSTALLED になっていたら成功です。

### JSON を扱いやすくする ArduinoJson をインストール

この作業はインストールなので、一度だけ対応すれば OK です。

![6c191b7ead13d2d12b2a7dab334d2224](https://i.gyazo.com/6c191b7ead13d2d12b2a7dab334d2224.png)

ツール > ライブラリを管理 でライブラリマネージャを起動します。

![image](https://i.gyazo.com/b8b223beedfe7b134fe5380e1920e584.png)

`ArduinoJson` で検索して、`完全同名` のライブラリを探します。

マウスを乗せると、バージョンとインストールのボタンが右下に出るので `6.18.5` のバージョンを指定してインストールボタンをクリックします。

![image](https://i.gyazo.com/ec1d0688667c161c941eced03a1bade9.png)

インストールできたら、ひょっとすると、リストが一番上に戻ってしまうかもしれませんが、根気よく `ArduinoJson` に移動して INSTALLED になっていたら成功です。

### ArduinoJson の情報は充実

![image](https://i.gyazo.com/ce0d85128ef8b9dd91734f1176607439.png)

https://arduinojson.org/ というウェブサイトを持っていて情報が充実しています。

![image](https://i.gyazo.com/35ae79ee668a291a65845fb495bb1f32.png)

ソースコードのサンプルもあり、すぐに使いやすくなっています。

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Test-MQTT` というファイル名で保存します。

```c
#include <M5Stack.h>

// Wi-Fi をつなぐためのライブラリ
// 今回は MQTT のため
#include <WiFiClient.h>
#include <WiFi.h>

// MQTT をつなぎためのライブラリ
// 今回追加インストールする
#include <PubSubClient.h>  // インストールすれば色がつく
// JSON を扱いやすくするライブラリ
#include <ArduinoJson.h> // こちらは色がついてなくてOK

// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";

// 今回使いたい MQTT のブローカーのアドレス
const char *mqttEndpoint = "今回使いたい MQTT のブローカーのアドレス";
// 今回使いたい MQTT のポート
const int mqttPort = 1883;
// 今回使いたい MQTT のユーザー名
const char *mqttUsername = "今回使いたい MQTT のユーザー名";
// 今回使いたい MQTT のパスワード
const char *mqttPassword = "今回使いたい MQTT のパスワード";

// デバイスID
// デバイスIDは機器ごとにユニークにします
// YOURNAME を自分の名前の英数字に変更します
// デバイスIDは同じMQTTブローカー内で重複すると大変なので、後の処理でさらにランダム値を付与してますが、名前を変えるのが確実なので、ちゃんと変更しましょう。
char *deviceID = "M5Stack-YOURNAME";

// MQTT メッセージを MQTT ブローカーに知らせるトピック
char *pubTopic = "handson/data";

// MQTT メッセージを MQTT ブローカーから待つトピック
char *subTopic = "handson/data";

// JSON 送信時に使う buffer
char pubJson[255];

// PubSubClient まわりの準備
WiFiClient httpClient;
PubSubClient mqttClient(httpClient);

void setup() {
  // init lcd, serial, but don't init sd card
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  // スタート
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(2);

  // Arduino のシリアルモニタ・M5Stack LCDディスプレイ両方にメッセージを出す
  Serial.print("START");  // Arduino のシリアルモニタにメッセージを出す
  M5.Lcd.print("START");  // M5Stack LCDディスプレイにメッセージを出す（英語のみ）

  // WiFi 接続開始
  WiFi.begin(ssid, password);
  // 勝手に Button A が押されることを回避
  WiFi.setSleep(false);

  while (WiFi.status() != WL_CONNECTED) {
      delay(500);

      // Arduino のシリアルモニタ・M5Stack LCDディスプレイ両方にメッセージを出す
      Serial.print(".");
      M5.Lcd.print(".");
  }

  // WiFi Connected
  // WiFi 接続完了
  M5.Lcd.setCursor(10, 40);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(2);

  // Arduino のシリアルモニタ・M5Stack LCDディスプレイ両方にメッセージを出す
  // 前のメッセージが print で改行入っていないので println で一つ入れる
  Serial.println("");  // Arduino のシリアルモニタにメッセージを出し改行が最後に入る
  M5.Lcd.println("");  // M5Stack LCDディスプレイにメッセージを出す改行が最後に入る（英語のみ） 

  // Arduino のシリアルモニタ・M5Stack LCDディスプレイ両方にメッセージを出す
  Serial.println("WiFi Connected.");  // Arduino のシリアルモニタにメッセージを出す
  M5.Lcd.println("WiFi Connected.");  // M5Stack LCDディスプレイにメッセージを出す（英語のみ）

  // ちゃんとつながったと分かるために 2 秒待ってから MQTT の処理に行く
  delay(2000);

  // MQTT の接続先設定
  mqttClient.setServer(mqttEndpoint, mqttPort);
  // MQTT のデータを受け取った時（購読時）の動作を設定
  mqttClient.setCallback(mqttCallback);
  // MQTT の接続
  mqttConnect();
}



void mqttConnect() {

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(2);
  
  // MQTT clientID のランダム化（名称重複対策）
  char clientID[40] = "clientID";
  String rndNum = String(random(0xffffff), HEX);
  String deviceIDRandStr = String(deviceID);
  deviceIDRandStr.concat("-");
  deviceIDRandStr.concat(rndNum);
  deviceIDRandStr.toCharArray(clientID, 40);
  M5.Lcd.println("[MQTT]");
  M5.Lcd.println("");
  M5.Lcd.printf("- clientID ");
  M5.Lcd.println("");
  M5.Lcd.println(clientID);

  // 接続されるまで待ちます
  while (!mqttClient.connected()) {
    if (mqttClient.connect(clientID,mqttUsername,mqttPassword)) {
      Serial.println("Connected.");
      M5.Lcd.println("");
      M5.Lcd.println("- MQTT Connected.");
      
      // subTopic 変数で指定されたトピックに向けてデータを送ります
      int qos = 0;
      mqttClient.subscribe(subTopic, qos);
      Serial.println("Subscribe start.");
      M5.Lcd.println("");
      M5.Lcd.println("- MQTT Subscribe start.");
      M5.Lcd.println(subTopic);

      // 初回データ送信 publish ///////////
      // データ送信のための JSON をつくる
      DynamicJsonDocument doc(1024);
      doc["message"] = "Connected";
      // pubJson という変数に JSON 文字列化されたものが入る
      serializeJson(doc, pubJson);
      // pubTopic 変数で指定されたトピックに向けてデータを送ります
      mqttClient.publish(pubTopic, pubJson);
    } else {
      // MQTT 接続エラーの場合はつながるまで 5 秒ごとに繰り返します
      Serial.print("Failed. Error state=");
      Serial.println(mqttClient.state());
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

// JSON を格納する StaticJsonDocument を準備
StaticJsonDocument<2048> jsonData;

// MQTT のデータを受け取った時（購読時）の動作を設定
void mqttCallback (char* topic, byte* payload, unsigned int length) {
  
  // データ取得
  String str = "";
  Serial.print("Received. topic=");
  Serial.println(topic);
  for (int i = 0; i < length; i++) {
      Serial.print((char)payload[i]);
      str += (char)payload[i];
  }
  Serial.print("\n");

  // 来た文字列を JSON 化して扱いやすくする
  // 変換する対象は jsonData　という変数
  DeserializationError error = deserializeJson(jsonData, str);

  // JSON パースのテスト
  if (error) {
    Serial.print(F("deserializeJson() failed: "));
    Serial.println(error.f_str());
    return;
  }

  // 以下 jsonData 内が JSON として呼び出せる
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(0, 0);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(2);
  M5.Lcd.println("MQTT Subscribed data");

  // データの取り出し
  // https://arduinojson.org/v6/example/parser/
  const char* message = jsonData["message"];

  // データの表示
  M5.Lcd.setCursor(0, 100);
  M5.Lcd.setTextSize(4);
  M5.Lcd.println(message);
  
}

// 常にチェックして切断されたら復帰できるようにする対応
void mqttLoop() {
  if (!mqttClient.connected()) {
      mqttConnect();
  }
  mqttClient.loop();
}
 
void loop() {

  M5.update();

  // 常にチェックして切断されたら復帰できるようにする対応
  mqttLoop();

  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    // データ送信のための JSON をつくる
    DynamicJsonDocument doc(1024);
    doc["message"] = "Pushed A";
    // pubJson という変数に JSON 文字列化されたものが入る
    serializeJson(doc, pubJson);
    // pubTopic 変数で指定されたトピックに向けてデータを送ります
    mqttClient.publish(pubTopic, pubJson);
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    // データ送信のための JSON をつくる
    DynamicJsonDocument doc(1024);
    doc["message"] = "Pushed B";
    // pubJson という変数に JSON 文字列化されたものが入る
    serializeJson(doc, pubJson);
    // pubTopic 変数で指定されたトピックに向けてデータを送ります
    mqttClient.publish(pubTopic, pubJson);
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    // データ送信のための JSON をつくる
    DynamicJsonDocument doc(1024);
    doc["message"] = "Pushed C";
    // pubJson という変数に JSON 文字列化されたものが入る
    serializeJson(doc, pubJson);
    // pubTopic 変数で指定されたトピックに向けてデータを送ります
    mqttClient.publish(pubTopic, pubJson);
  }
}
```

## Wi-Fi 情報を反映

```c
// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";
```

自分のつなぎたい Wi-Fi の SSID とパスワードを反映します。

## MQTT の接続設定を反映

今回は私（講師）の方が、Beebotte というサービスで、ひとつブローカーを立ち上げているので、そのまま使いましょう。

```c
// 今回使いたい MQTT のブローカーのアドレス
const char *mqttEndpoint = "今回使いたい MQTT のブローカーのアドレス";
// 今回使いたい MQTT のポート
const int mqttPort = 1883;
// 今回使いたい MQTT のユーザー名
const char *mqttUsername = "今回使いたい MQTT のユーザー名";
// 今回使いたい MQTT のパスワード
const char *mqttPassword = "今回使いたい MQTT のパスワード";
```

ここの設定をお知らせする設定で置き換えましょう。まるまる上書きします。

## クライアントIDに自分の名前を反映します

このあと、MQTT のクライアントIDに自分の名前（英語）を反映します。英語で考えておきましょう。

```c
// デバイスID
// デバイスIDは機器ごとにユニークにします
// YOURNAME を自分の名前の英数字に変更します
// デバイスIDは同じMQTTブローカー内で重複すると大変なので、後の処理でさらにランダム値を付与してますが、名前を変えるのが確実なので、ちゃんと変更しましょう。
char *deviceID = "M5Stack-YOURNAME";
```

たとえば、hogehoge さんなら YOURNAME を hogehoge に変更します。

```c
// デバイスID
// デバイスIDは機器ごとにユニークにします
// YOURNAME を自分の名前の英数字に変更します
// デバイスIDは同じMQTTブローカー内で重複すると大変なので、後の処理でさらにランダム値を付与してますが、名前を変えるのが確実なので、ちゃんと変更しましょう。
char *deviceID = "M5Stack-hogehoge";
```

## M5Stack に書き込んでみる

そして、もう一度保存します。（大事）

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

書き込みと同時に Connected というメッセージが MQTT ブローカーに送信されます。

半角英数字で MQTT ブローカーへメッセージが送信してみます。

![1f9239ae85328a52a26fc8c2998cf6e5](https://i.gyazo.com/1f9239ae85328a52a26fc8c2998cf6e5.jpg)

M5Stack 側で MQTT ブローカーからこのメッセージを受け取るので M5Stack にメッセージが届きます。この例は `Hello!` を送った例です。

## 講師側からデータを送る

![a7cf2d4e7809738029ea2dcfe40522d6](https://i.gyazo.com/a7cf2d4e7809738029ea2dcfe40522d6.png)

シンプルに MQTT ブローカーにデータを送ってみます。

もし、みなさんがハッカソンでプログラムに組み込む場合は、各プログラミング言語の MQTT 接続や MQTT でのパブリッシュ（データ送信）を調べて組み込んでみましょう。

- [pythonでMQTT送受信 - Qiita](https://qiita.com/hsgucci/items/6461d8555ea1245ef6c2)
- [Node.js mqtt - npm](https://www.npmjs.com/package/mqtt)

## MQTT Explorer 

[MQTT Explorer](http://mqtt-explorer.com/) というツールを使ってシンプルに MQTT ブローカーにデータを送ってみます。

![2a78ac8bad45bd2a38fe661c961d770f](https://i.gyazo.com/2a78ac8bad45bd2a38fe661c961d770f.png)

講師側で接続設定を行って Connect をクリックします。

![5d82c8e82c1c1c35b331e0e8ce3c1957](https://i.gyazo.com/5d82c8e82c1c1c35b331e0e8ce3c1957.png)

Publish 項目で設定してデータを送ります。

![21d73b8e9f0a98d0bf00a5354b6f4815](https://i.gyazo.com/21d73b8e9f0a98d0bf00a5354b6f4815.png)

こんな設定です。

### Node-RED(enebular)

![5dd8d7f99bdd1fb75d0ecaf4e4641e1a](https://i.gyazo.com/5dd8d7f99bdd1fb75d0ecaf4e4641e1a.png)

Node-RED(enebular) の MQTT ノードを使ってシンプルに MQTT ブローカーにデータを送ってみます。

参考文献はこちらです。

Beebotte の MQTT ブローカーと Node-RED の MQTT ノードでやり取りをするメモ – 1ft-seabass.jp.MEMO
https://www.1ft-seabass.jp/memo/2022/02/28/connecting-beebotte-using-nodered/

## 次は

左のメニューから、次に進みましょう。