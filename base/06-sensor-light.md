# Grove 光センサー を動かす

![a31956caee6816edc04e0729ec7a5371](https://i.gyazo.com/a31956caee6816edc04e0729ec7a5371.jpg)

## 今回のプログラムはどのように動くか

![350acf32b907506e4cc2fefe4efb51ba](https://i.gyazo.com/350acf32b907506e4cc2fefe4efb51ba.jpg)

書き込みすると、光センサーが明るさを感知してディスプレイに明るさをパーセンテージで表示します。

## Grove への Grove ケーブルのつなぎかた

Grove と Grove ケーブルのツメを合わせるように差し込みます。

![7783fce8c73f3996224ddeab7946870d](https://i.gyazo.com/7783fce8c73f3996224ddeab7946870d.jpg)

このように差し込みました。

## M5Stack への Grove ケーブルのつなぎかた

![image](https://i.gyazo.com/d771bba6a2e3d54e10b093c557a0acee.jpg)

裏面右側のピン番号を合わせて以下のようにつなぎます。

- 赤ケーブル
  - 5V
- 黒ケーブル
  - G
- 白ケーブル
  - つながない
- 黄ケーブル
  - 35

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Light-sensor` というファイル名で保存します。

```c
#include <M5Stack.h>

// 光センサーの値
int currentLightValue = 0;

// 記録しているボタンの状態（前の状態を比較する）
int lightValue = 0;

// 黄色いケーブルを差し込む M5Stack ピン番号
int analogPin = 35;

void setup() {
  
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  M5.Power.begin();

  M5.Lcd.clear(BLACK);
  M5.Lcd.setTextSize(3);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.print("Light Analog");

  // スピーカーぱちぱち音対策
  dacWrite(25, 0); // Speaker OFF
}

void loop() {
  M5.update();

  // まずアナログ値の素の値として取得 0 - 4096
  int lightSensorBaseValue = analogRead(analogPin);

  // 4096 を最大値としてパーセント値にする
  currentLightValue = lightSensorBaseValue / 4096.00 * 100.00; 
  
  M5.Lcd.clear(BLACK);
  M5.Lcd.setCursor(0, 100);
  
  // 2行目
  M5.Lcd.println("[Light Sensor]");
  M5.Lcd.print("Percent : ");
  M5.Lcd.print(currentLightValue);
  M5.Lcd.println("%");
  
  delay(500);
}
```

## M5Stack に書き込んでみる

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/819cd1259c50711ce5521e06c0771647.jpg)

書き込みすると、光センサーが明るさを感知してディスプレイに明るさをパーセンテージで表示します。

![image](https://i.gyazo.com/ede4ddeffc7c3235e0f3439e8d0b64c3.jpg)

手で隠すと暗くなりパーセンテージが変化することも確認してみましょう。

## LINE Notify と連携するソースコードを試す

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Light-sensor-LINENotify` というファイル名で保存します。

```c
#include <M5Stack.h>

// HTTP 通信を行うライブラリ
#include <HTTPClient.h>

// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";

// LINE Notify トークン
String tokenLINENotify = "LINE Notify トークン";

// 光センサーの値
int currentLightValue = 0;

// 明るいかくらいかの判定 1/0
int currentLightFlag = 1;

// 記録しているボタンの状態（前の状態を比較する）
int lightValue = 0;

// 黄色いケーブルを差し込む M5Stack ピン番号
int analogPin = 35;
void setup() {
  // init lcd, serial, but don't init sd card
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  // スピーカーぱちぱち音対策
  dacWrite(25, 0); // Speaker OFF

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
  send_message("Launched");
}

// HTTP でメッセージ送信部分
void send_message(String msg) {

  // 今回送る LINE Notify API
  // 参考 https://notify-bot.line.me/doc/ja/
  String url = "https://notify-api.line.me/api/notify";

  Serial.println("-> send_message");
  Serial.print("msg: ");
  Serial.println(msg);
  
  // 送るデータ
  String queryString = String("message=") + msg;
  // HTTPClient 準備
  HTTPClient httpClient;
  // URL 設定
  httpClient.begin(url);
  // Content-Type
  httpClient.addHeader("Content-Type", "application/x-www-form-urlencoded");
  // Authorization
  httpClient.addHeader("Authorization", "Bearer " + tokenLINENotify);
  
  // データ送信完了
  Serial.println("sended.");

  // ポストする
  int status_code = httpClient.POST(queryString);
  if( status_code == 200 ){
    String response = httpClient.getString();
  }
  
  httpClient.end();
  
  delay(2000);
}

void loop() {
  M5.update();

  // まずアナログ値の素の値として取得 0 - 4096
  int lightSensorBaseValue = analogRead(analogPin);

  // 4096 を最大値としてパーセント値にする
  currentLightValue = lightSensorBaseValue / 4096.00 * 100.00; 
  
  M5.Lcd.clear(BLACK);
  M5.Lcd.setCursor(0, 100);
  
  // 2行目
  M5.Lcd.println("[Light Sensor]");
  M5.Lcd.print("Percent : ");
  M5.Lcd.print(currentLightValue);
  M5.Lcd.println("%");

  // 暗くなったらメッセージを送る
  if(currentLightFlag == 1){
    // 明るいとき 5%以上
    if( currentLightValue < 5.0 ){
      // 暗くなったらメッセージ
      send_message("Dark");
      // 暗くなったフラグ
      currentLightFlag = 0;
    }
  } else {
    // 暗いとき
    if( currentLightValue > 5.0 ){
      // 明るくなったらメッセージ
      send_message("Light");
      // 明るくなったフラグ
      currentLightFlag = 1;
    }
  }
  
  
  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("Pushed A");
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("Pushed B");
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したら JSON 形式のメッセージを飛ばす
    send_message("Pushed C");
  }

  delay(500);
}
```


## Wi-Fi 情報を反映

```c
// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";
```

先ほどと同じように Wi-Fi 情報を反映します。

## LINE Notify のトークンを反映

```c
// LINE Notify トークン
String tokenLINENotify = "LINE Notify トークン";
```

Wi-Fi 設定と近いところにある `String tokenLINENotify = "LINE Notify のトークン";` の `LINE Notify のトークン` の部分を、先ほどメモした LINE Notify のトークンに置き換えましょう。ダブルクオーテーションを消していないか気をつけましょう。

## M5Stack に書き込んでみる

そして、もう一度保存します。（大事）

![44a66c2c13d72b6958d0229f6b66794a](https://i.gyazo.com/44a66c2c13d72b6958d0229f6b66794a.jpg)

M5Stack に書き込んでみましょう。

## M5Stack を動かしてみる

起動してみると Launched! というメッセージが LINE Notify に送られます。

![ffa6fd07c788d8936ad3817e6f8b7bf9](https://i.gyazo.com/ffa6fd07c788d8936ad3817e6f8b7bf9.jpg)

光センサーを手で隠すと暗くなると Dark というメッセージを送ります。いまの場所の明暗に合わせて 5 %未満。

![44a66c2c13d72b6958d0229f6b66794a](https://i.gyazo.com/44a66c2c13d72b6958d0229f6b66794a.jpg)

暗くしてから、明るくすると Light というメッセージを送ります。

![bdbac37cd46d298071fb67fd4e85b76c](https://i.gyazo.com/bdbac37cd46d298071fb67fd4e85b76c.png)

LINE Notify はこのようにメッセージが送られています。

## 次は

左のメニューから、次に進みましょう。