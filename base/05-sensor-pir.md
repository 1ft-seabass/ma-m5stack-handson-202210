# Grove 人感センサー（PIR センサー）を動かす

![image](https://i.gyazo.com/9fcf63bc6544379ec184829112da96a4.jpg)

## 今回のプログラムはどのように動くか

![image](https://i.gyazo.com/bc2eb4c34f1e5c361b585a28512b324b.jpg)

書き込みすると、センサーに向けて手をかざすと人の気配を感知（人感）して Sensor ON という大文字が赤背景で出てきます。

![image](https://i.gyazo.com/f0dab9fdedb02b2520208f37cd8a6f0f.jpg)

手をかざすのをやめて、しばらく待っていると、 Sensor OFF に戻ります。

## Grove への Grove ケーブルのつなぎかた

![image](https://i.gyazo.com/f543983e1e18d468f2b155090a24328c.jpg)

機材リストで購入した GROVE 4ピン ジャンパオスケーブル と Grove 人感センサーを用意します。

![image](https://i.gyazo.com/cec530b6f80baed72d7991d269bab922.jpg)

Grove と Grove ケーブルのツメを合わせるように差し込みます。

![image](https://i.gyazo.com/76ad5acfd93bd875b3136ff19d3d4246.jpg)

このように差し込みました。

## 外すときはツメを上げてから取りましょう

![image](https://i.gyazo.com/ba514e766c4081d9f5c1042cdd3f7fe6.jpg)

GROVE 4ピン ジャンパオスケーブル は外すときはツメを上げてから取りましょう。

ツメが噛んでいる状態無理やり抜こうとすると、最悪、Grove コネクタやセンサーが破損したりします。

## M5Stack への Grove ケーブルのつなぎかた

![image](https://i.gyazo.com/f647ce7a0fce3c02fd6ef24b8521aa83.jpg)

裏面右側のピン番号を合わせて以下のようにつなぎます。

- 赤ケーブル
  - 5V
- 黒ケーブル
  - G
- 白ケーブル
  - つながない
- 黄ケーブル
  - 2

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-PIR-sensor` というファイル名で保存します。

```c
#include <M5Stack.h>

// 最新のボタンの状態
int currentButtonState = LOW;

// 記録しているボタンの状態（前の状態を比較する）
int buttonState = LOW;

// 黄色いケーブルを差し込む M5Stack ピン番号
int digitalPin = 2;

void setup() {
  
  // LCD ディスプレイとシリアルは動かして、SDカードは動かさない設定
  M5.begin(true, false, true);

  M5.Power.begin();

  M5.Lcd.clear(BLACK);
  M5.Lcd.setTextSize(3);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.print("PIR Digital");

}

void loop() {
  M5.update();

  // 値を取得
  int currentButtonState = digitalRead(digitalPin);

  // 値に変化があれば通知
  if( currentButtonState != buttonState ){
    // 現在の状態を記録
    buttonState = currentButtonState;
    if (buttonState == HIGH) {
      // 人感検出 ON
      M5.Lcd.setTextSize(5);
      M5.Lcd.clear(RED);
      M5.Lcd.setCursor(10, 100);  // 良い感じに真ん中に出すカーソル移動
      M5.Lcd.setTextColor(BLACK);
      M5.Lcd.print("Sensor ON");
    } else {
      // 人感検出 OFF
      M5.Lcd.setTextSize(3);
      M5.Lcd.clear(BLACK);
      M5.Lcd.setCursor(10, 100);  // 良い感じに真ん中に出すカーソル移動
      M5.Lcd.setTextColor(WHITE);
      M5.Lcd.print("Sensor OFF");
    }
  }
  
  delay(500);
}
```

## M5Stack に書き込んでみる

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/bc2eb4c34f1e5c361b585a28512b324b.jpg)

書き込みすると、センサーに向けて手をかざすと人の気配を感知（人感）して Sensor ON という大文字が赤背景で出てきます。

![image](https://i.gyazo.com/f0dab9fdedb02b2520208f37cd8a6f0f.jpg)

手をかざすのをやめて、しばらく待っていると、 Sensor OFF に戻ります。

## LINE Notify と連携するソースコードを試す

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-PIR-sensor-LINENotify` というファイル名で保存します。

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

// 最新のボタンの状態
int currentButtonState = LOW;

// 記録しているボタンの状態（前の状態を比較する）
int buttonState = LOW;

// 黄色いケーブルを差し込む M5Stack ピン番号
int digitalPin = 2;

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
  send_message("Launched!");
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

  // センサーから値を取得
  int currentButtonState = digitalRead(digitalPin);

  // 値に変化があれば通知
  if( currentButtonState != buttonState ){
    // 現在の状態を記録
    buttonState = currentButtonState;
    if (buttonState == HIGH) {
      // 人感検出 ON
      M5.Lcd.setTextSize(5);
      M5.Lcd.clear(RED);
      M5.Lcd.setCursor(10, 100);  // 良い感じに真ん中に出すカーソル移動
      M5.Lcd.setTextColor(BLACK);
      M5.Lcd.print("Sensor ON");
      // JSON 形式のメッセージを飛ばす
      send_message("Sensor ON");
    } else {
      // 人感検出 OFF
      M5.Lcd.setTextSize(3);
      M5.Lcd.clear(BLACK);
      M5.Lcd.setCursor(10, 100);  // 良い感じに真ん中に出すカーソル移動
      M5.Lcd.setTextColor(WHITE);
      M5.Lcd.print("Sensor OFF");
      // JSON 形式のメッセージを飛ばす
      send_message("Sensor OFF");
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

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## M5Stack を動かしてみる

起動してみると Launched! というメッセージが LINE Notify に送られます。

![image](https://i.gyazo.com/bc2eb4c34f1e5c361b585a28512b324b.jpg)

センサーに向けて手をかざすと人の気配を感知（人感）して Sensor ON という大文字が赤背景で出たうえで Sensor ON というメッセージが送られます。

![021bf2dcb0cb1f78bfbb32989c652360](https://i.gyazo.com/021bf2dcb0cb1f78bfbb32989c652360.png)

LINE Notify はこのようにメッセージが送られています。

## 次は

左のメニューから、次に進みましょう。