# サーバーにメッセージを送ってみる

## 今回のプログラムはどのように動くか

![image](https://i.gyazo.com/416402edf3476298c3065d0500962a92.png)

このような仕組みです。

![image](https://i.gyazo.com/e9c21a3d286467ed300b2ac6e1f48ad7.jpg)

起動時にサーバーにメッセージが送られ、その後、各ボタンをクリックしてサーバーにメッセージが送られます。

## テストサーバーを用意しました

![image](https://i.gyazo.com/b9f26a859d98bbee5dc509b2cfe3aaa2.png)

授業のためにテストサーバーを用意しました

https://app-1ft-iot-test-server.herokuapp.com/ui

- こういうサーバーがあるとデータがちゃんと届いているかのチェックがしやすいです
- ひとまず今回は HTTP で受信するとログが出ます
- 時間に余裕があれば軽くデモします

## ソースコードを反映

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Test-HTTP-POST-JSON` で保存します。

```c
#include <M5Stack.h>

// HTTP 通信を行うライブラリ
#include <HTTPClient.h>

// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";

void setup() {
  // init lcd, serial, but don't init sd card
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  // スタート
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
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
  
  // 起動時に送る
  delay(1000);
  send_message("{\"message\":\"Launched!\"}");
}

// HTTP でメッセージ送信部分
void send_message(String msg) {

  // 今回送るURL
  String url = "https://app-1ft-iot-test-server.herokuapp.com/api/test/message";

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
  
  M5.Lcd.println("-> send_message");
  M5.Lcd.print("msg: ");
  M5.Lcd.println(msg);

  // 送るデータ
  String queryString = msg;
  // HTTPClient 準備
  HTTPClient httpClient;
  // URL 設定
  httpClient.begin(url);
  // Content-Type
  httpClient.addHeader("Content-Type", "application/json");

  M5.Lcd.println("sended.");
  Serial.println("sended.");
  
  // ポストする
  int status_code = httpClient.POST(queryString);
  if( status_code == 200 ){
    String response = httpClient.getString();
    
    M5.Lcd.println("response:");
    M5.Lcd.println(response);
  }
  httpClient.end();
  
  delay(2000);
}

void loop() {
  M5.update();
  
  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    // \" はダブルクォーテーションで囲まれた中で JSON 内のダブルクォーテーションを表現するために \" でエスケープしてます。
    send_message("{\"message\":\"Pushed A\"}");
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Pushed B\"}");
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Pushed C\"}");
  }
}
```

## Wi-Fi 情報を反映して、また保存

```c
// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";
```

先ほどと同じように Wi-Fi 情報を反映します。もう一度保存します。（大事）

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/e9c21a3d286467ed300b2ac6e1f48ad7.jpg)

起動時に サーバーに `{"message":"Launched!"}` という JSON データが送られます。

また、3つボタンが並んでいますが、Aボタン、Bボタン、Cボタンでプログラムと対応しています。クリックしてサーバーにメッセージが送られるか確認してみましょう。

たとえば、Cボタンをクリックすると `{"message":"Pushed C"}` という JSON データが送られます。

# 送るデータを変更してみる（書き換え訓練）

これだと送った人が分からないので、英数字で自分の名前を決めて、送るデータをちょっと書き換えましょう。

## Launched メッセージをちょっと変更

```c
  // 起動時に送る
  delay(1000);
  send_message("{\"message\":\"Launched!\"}");
```

を、以下のように書き加えます。Seigo で変更した例です。

```c
  // 起動時に送る
  delay(1000);
  send_message("{\"message\":\"Seigo Launched!\"}");
```

## ボタンを押したときのメッセージをちょっと変更

```c
void loop() {
  M5.update();
  
  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    // \" はダブルクォーテーションで囲まれた中で JSON 内のダブルクォーテーションを表現するために \" でエスケープしてます。
    send_message("{\"message\":\"Pushed A\"}");
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Pushed B\"}");
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Pushed C\"}");
  }
}
```

を、以下のように書き加えます。Seigo で変更した例です。

```c
void loop() {
  M5.update();
  
  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    // \" はダブルクォーテーションで囲まれた中で JSON 内のダブルクォーテーションを表現するために \" でエスケープしてます。
    send_message("{\"message\":\"Seigo Pushed A\"}");
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Seigo Pushed B\"}");
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("{\"message\":\"Seigo Pushed C\"}");
  }
}
```

## 余談：英数字で顔文字表現テクニック

もし余裕があれば、

 - `(^_^)`
 - `(=_=)`
 - `:-)`
 - `;-)`
 - `(*o*)/`

のような顔文字は英数字でも表現できるので、ぜひ試してみましょう～。

## エクストラ : ArduinoJson

`send_message("{\"message\":\"Pushed A\"}");` という書き方で JSON を作るのはシンプルな値では何とかなりますが複雑なデータを作ったり読み込んだりするケースでツラくなってきます。

![af6c2d79d0fb6907a4b8ed888ade6f09](https://i.gyazo.com/af6c2d79d0fb6907a4b8ed888ade6f09.jpg)

そのときは、[ArduinoJson](https://arduinojson.org/) というライブラリを使いましょう。 JSON データが格段に扱いやすくなります。

```c
// JSON を格納するオブジェクト DynamicJsonDocument
DynamicJsonDocument doc(255);
// JSON データ作成
doc["message"] = "Pushed A";
// 変換後の char を準備
char serializedJsonString[255];
// JSON オブジェクトを String に変換して serializedJsonString 変数に格納
// 中身は {"message":"Pushed A"}
serializeJson(doc,serializedJsonString);
// send_message 関数に serializedJsonString を送る
send_message(serializedJsonString);
```

たとえば、このように JSON を格納するオブジェクト DynamicJsonDocument を作成し、JSON データ作成したあと、変換後の String を準備して serializeJson 関数で JSON オブジェクトを String に変換して serializedJsonString 変数に格納します。

- メニュー > ツール > ライブラリを管理
- ライブラリから ArduinoJson を完全一致で検索
- ArduinoJson が見つかったらインストール

でインストールを済ませたら、該当のコードで `#include <ArduinoJson.h>` で呼び出して使います。

```c
#include <M5Stack.h>

// HTTP 通信を行うライブラリ
#include <HTTPClient.h>

// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";

// JSON を扱いやすくするライブラリ
#include <ArduinoJson.h>
// JSON を格納するオブジェクト DynamicJsonDocument
DynamicJsonDocument doc(255);
// 変換後の char を準備
char serializedJsonString[255];

void setup() {
  // init lcd, serial, but don't init sd card
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  // スタート
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
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
  
  // 起動時に送る
  delay(1000);
  send_message("{\"message\":\"Launched!\"}");
}

// HTTP でメッセージ送信部分
void send_message(String msg) {

  // 今回送るURL
  String url = "https://app-1ft-iot-test-server.herokuapp.com/api/test/message";

  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
  
  M5.Lcd.println("-> send_message");
  M5.Lcd.print("msg: ");
  M5.Lcd.println(msg);

  // 送るデータ
  String queryString = msg;
  // HTTPClient 準備
  HTTPClient httpClient;
  // URL 設定
  httpClient.begin(url);
  // Content-Type
  httpClient.addHeader("Content-Type", "application/json");

  M5.Lcd.println("sended.");
  Serial.println("sended.");
  
  // ポストする
  int status_code = httpClient.POST(queryString);
  if( status_code == 200 ){
    String response = httpClient.getString();
    
    M5.Lcd.println("response:");
    M5.Lcd.println(response);
  }
  httpClient.end();
  
  delay(2000);
}

void loop() {
  M5.update();
  
  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    // JSON データ作成
    doc["message"] = "Pushed A";
    // JSON 変換
    serializeJson(doc,serializedJsonString);
    // データを送る
    send_message(serializedJsonString);
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    // JSON データ作成
    doc["message"] = "Pushed B";
    // JSON 変換
    serializeJson(doc,serializedJsonString);
    // データを送る
    send_message(serializedJsonString);
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    // JSON データ作成
    doc["message"] = "Pushed C";
    // JSON 変換
    serializeJson(doc,serializedJsonString);
    // データを送る
    send_message(serializedJsonString);
  }
}
```

## 次は

左のメニューから、次に進みましょう。