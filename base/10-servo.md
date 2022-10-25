# Grove サーボモータ

![image](https://i.gyazo.com/3adc4bb764b6a3a1805ddff57b581750.jpg)

## 今回のプログラムはどのように動くか

![image](https://i.gyazo.com/71e72d85154a2ec03f3af33dd0c27d61.jpg)

書き込むと A ボタンで -90 度、B ボタンで 0 度、C ボタンで 90 度にサーボモータの角度が操れます。

## Grove への Grove ケーブルのつなぎかた

今回はサーボモータに直接つながっているので Grove 変換ケーブルをつなぐ必要はありません。

## サーボモータにパーツを装着

![image](https://i.gyazo.com/2c3150b68beaeb1905d11c17396da14c.jpg)

サーボモータと同行されているパーツのうち、こちらの白いプラスチック製の長いパーツを取り出します。

![image](https://i.gyazo.com/61f83f347e0650ed68c7be18d6c3b541.jpg)

サーボ側の白い出っ張りのギザギザと、

![image](https://i.gyazo.com/cd1d8e06b541779403c9c3f5bfb6085c.jpg)

パーツ側のギザギザの凹みを合わせてはめ込みます。

![image](https://i.gyazo.com/e33c7875b52fde2beef0e5efd39e8d49.jpg)

よく確認しましょう。

![image](https://i.gyazo.com/b22d6830013ae89b91f57f038f8f701a.jpg)

はめ込むことで角度の変化がより分かりやすくなります。

実際にこのパーツ含めたパーツ群は、何かを動かすときに力を伝えるための軸の役割しています。

## PORT A という Grove ポートを確認

![image](https://i.gyazo.com/cfed896c7b6274658b500ed829f5f795.jpg)

今回は PORT A という Grove ポートにつなぐので確認します。

GPIO としては 21 と 22 で操作できます。今回は 22 で指示を出します。

## M5Stack への Grove ケーブルのつなぎかた

![image](https://i.gyazo.com/77ead28b9239790ae6595d6b8127ad18.jpg)

PORT A に挿しこみます。

![image](https://i.gyazo.com/6dcc43aae0d608f527196dddc7f4f424.jpg)

このように接続できます。

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Servo` というファイル名で保存します。

```c
#include <M5Stack.h>

#define SERVO_MIN_WIDTH_MS  0.6
#define SERVO_MAX_WIDTH_MS  2.4
#define LEDC_CHANNEL_3      3   // LEDCのチャンネル指定
#define LEDC_TIMER_BIT      16  // LEDCのPWMタイマーの精度設定
#define LEDC_SERVO_FREQ     50  // サーボ信号の１サイクル　50Hz:20ms
#define SERVO_PIN           22  // Servo ピン

void setup() {
  
  M5.begin(
    true,  // LCD ディスプレイ ON
    false, // SDカードは動かさない設定
    true,  // シリアル ON
    false  // I2C 無効(PORT A を GPIO で使用するため)
  );

  // スタート
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 100);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(3);

  // Arduino のシリアルモニタ・M5Stack LCDディスプレイ両方にメッセージを出す
  Serial.print("Servo");  // Arduino のシリアルモニタにメッセージを出す
  M5.Lcd.print("Servo");  // M5Stack LCDディスプレイにメッセージを出す（英語のみ）

  // servoモーター設定
  servo_setup();
}

// サーボ角度 PWM 補助
int servo_pwm_count(int v)
{
  float vv = (v + 90) / 180.0 ;
  return (int)(65536 * (SERVO_MIN_WIDTH_MS + vv * (SERVO_MAX_WIDTH_MS -SERVO_MIN_WIDTH_MS)) / 20.0) ;
}

// サーボ設定
void servo_setup(){
  // 16ビット精度で制御
  ledcSetup(LEDC_CHANNEL_3, LEDC_SERVO_FREQ, LEDC_TIMER_BIT) ;
  // CH3 を SERVO にする
  ledcAttachPin(SERVO_PIN, LEDC_CHANNEL_3) ;
}

// サーボの角度を設定
void servo_degree(int degree){
  ledcWrite(LEDC_CHANNEL_3, servo_pwm_count(degree)) ; 
}

void loop() {
  M5.update();

  if (M5.BtnA.wasReleased()) {
    // A ボタン
    M5.Lcd.fillScreen(BLACK);
    M5.Lcd.setCursor(10, 100);
    M5.Lcd.setTextColor(WHITE);
    M5.Lcd.setTextSize(3);
    Serial.print("A");  //
    M5.Lcd.print("A Degree:90");  //
    // サーボの角度を設定
    servo_degree(90) ; 
  } else if (M5.BtnB.wasReleased()) {
    // B ボタン
    M5.Lcd.fillScreen(BLACK);
    M5.Lcd.setCursor(10, 100);
    M5.Lcd.setTextColor(WHITE);
    M5.Lcd.setTextSize(3);
    Serial.print("B");  //
    M5.Lcd.print("B Degree:0");  //
    // サーボの角度を設定
    servo_degree(0) ; 
  } else if (M5.BtnC.wasReleased()) {
    // C ボタン
    M5.Lcd.fillScreen(BLACK);
    M5.Lcd.setCursor(10, 100);
    M5.Lcd.setTextColor(WHITE);
    M5.Lcd.setTextSize(3);
    Serial.print("C");  //
    M5.Lcd.print("C Degree:-90");  //
    // サーボの角度を設定
    servo_degree(-90) ; 
  }
}
```

## M5Stack に書き込んでみる

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/3f75202f73fccd2a5400939e52ca2b27.jpg)

動かしてみると、Servo という文字が現れます。

![image](https://i.gyazo.com/01daaddb977d3be016f952029d7a5b68.jpg)

A ボタンで -90 度、B ボタンで 0 度、C ボタンで 90 度にサーボモータの角度が操れます。

## MQTT と連携するソースコードを試す

つづいて、MQTT 連携です。

![image](https://i.gyazo.com/d04434612e99118cd30d8b46ad749f75.jpg)

たとえば

```
{"type":"control","control":60}
```

と受け取る 60 度に制御できる仕組みです。

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Servo-MQTT` というファイル名で保存します。

```c
#include <M5Stack.h>

#define SERVO_MIN_WIDTH_MS  0.6
#define SERVO_MAX_WIDTH_MS  2.4
#define LEDC_CHANNEL_3      3   // LEDCのチャンネル指定
#define LEDC_TIMER_BIT      16  // LEDCのPWMタイマーの精度設定
#define LEDC_SERVO_FREQ     50  // サーボ信号の１サイクル　50Hz:20ms
#define SERVO_PIN           22  // Servo ピン

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

// MQTT メッセージを LINE BOT に知らせるトピック
char *pubTopic = "handson/data";

// MQTT メッセージを LINE BOT から待つトピック
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

  // servoモーター設定
  servo_setup();
}

// サーボ角度 PWM 補助
int servo_pwm_count(int v)
{
  float vv = (v + 90) / 180.0 ;
  return (int)(65536 * (SERVO_MIN_WIDTH_MS + vv * (SERVO_MAX_WIDTH_MS -SERVO_MIN_WIDTH_MS)) / 20.0) ;
}

// サーボ設定
void servo_setup(){
  // 16ビット精度で制御
  ledcSetup(LEDC_CHANNEL_3, LEDC_SERVO_FREQ, LEDC_TIMER_BIT) ;
  // CH3 を SERVO にする
  ledcAttachPin(SERVO_PIN, LEDC_CHANNEL_3) ;
}

// サーボの角度を設定
void servo_degree(int degree){
  ledcWrite(LEDC_CHANNEL_3, servo_pwm_count(degree)) ; 
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
      doc["type"] = "message";
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

  // Serial.println(jsonData["type"]);
  // タイプの取り出し
  // https://arduinojson.org/v6/example/parser/
  String type = jsonData["type"];
  // Serial.println(jsonData["type"]);
  
  if(type == "control"){
    // コントロール値を取り出し
    const int control = jsonData["control"];
  
    // データの表示
    M5.Lcd.setCursor(0, 60);
    M5.Lcd.setTextSize(4);
    M5.Lcd.println("control:");
    M5.Lcd.println(control);
    
    // サーボの角度変更
    servo_degree(control);
  } else {
    // メッセージを取り出し
    const char* message = jsonData["message"];
    
    // データの表示
    M5.Lcd.setCursor(0, 60);
    M5.Lcd.setTextSize(4);
    M5.Lcd.println("message:");
    M5.Lcd.println(message);
  }
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

シンプルに MQTT ブローカーにデータを送ってみます。

### MQTT Explorer 

[MQTT Explorer](http://mqtt-explorer.com/) というツールを使ってシンプルに MQTT ブローカーにデータを送ってみます。

![2a78ac8bad45bd2a38fe661c961d770f](https://i.gyazo.com/2a78ac8bad45bd2a38fe661c961d770f.png)

講師側で接続設定を行って Connect をクリックします。

![3721b58735c8752429cd340b7d747a3f](https://i.gyazo.com/3721b58735c8752429cd340b7d747a3f.png)

Publish 項目で設定してデータを送ります。

![2f834b75fc96dd8cc40130eca8cd1bac](https://i.gyazo.com/2f834b75fc96dd8cc40130eca8cd1bac.png)

こんな設定です。

### Node-RED(enebular)

![b330e365b24a56b0c2fa00d554afba76](https://i.gyazo.com/b330e365b24a56b0c2fa00d554afba76.png)

Node-RED(enebular) の MQTT ノードを使ってシンプルに MQTT ブローカーにデータを送ってみます。

## 次は

左のメニューから、次に進みましょう。