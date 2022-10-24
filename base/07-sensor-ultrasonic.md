# Grove 超音波距離センサ を動かす

![image](https://i.gyazo.com/10d6754abbe13d1e7bec436dc3c2d15a.jpg)

## 今回のプログラムはどのように動くか

![image](https://i.gyazo.com/bd1d04513fd60ee4739023ac8929f653.jpg)

書き込むと障害物とセンサーの距離データが表示されます。

## Grove への Grove ケーブルのつなぎかた

Grove と Grove ケーブルのツメを合わせるように差し込みます。

![image](https://i.gyazo.com/3e283b2790f9ca7ee3fd06e51ba6c294.jpg)

このように差し込みました。

## M5Stack への Grove ケーブルのつなぎかた

![image](https://i.gyazo.com/581886e629731f2469336f0becc14eb0.jpg)

裏面右側のピン番号を合わせて以下のようにつなぎます。

- 赤ケーブル
  - 5V
- 黒ケーブル
  - G
- 白ケーブル
  - つながない
- 黄ケーブル
  - 2

## ライブラリをインストール

GitHub の [Grove 超音波距離センサセンサーのライブラリページ](https://github.com/Seeed-Studio/Seeed_Arduino_UltrasonicRanger) にアクセスします。

![image](https://i.gyazo.com/b239ed35340ebfaf0dcadf614b46313d.png)

Code ボタンをクリックして Download ZIP ボタンからダウンロードします。

![image](https://i.gyazo.com/854ed3b2de05ebb38e187135f9e58de2.png)

スケッチ > ライブラリをインクルード > .ZIP形式のライブラリをインストール をクリックします。

![image](https://i.gyazo.com/ad18eb8e7c692616c2d1d1abc507d292.png)

先ほどダウンロードした ZIP ファイルを選択してインストールします。こちらは Windows のファイル選択ですが、Mac の人は適宜読み替えてください。

これでインストール完了です。

![image](https://i.gyazo.com/0926b156ff8ee87a9a8b626d80bbeb92.png)

ツール > ライブラリを管理で、

![image](https://i.gyazo.com/012e66cc87a7748de1fad34821e44a92.png)

タイプをインストール済みで絞り込んで、Grove Ultrasonic Ranger がインストールされていればインストール成功です。

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-UltraSonicRanger-sensor` というファイル名で保存します。

```c
#include <M5Stack.h>

// 今回のセンサーライブラリの呼び出し
#include "Ultrasonic.h"
// 黄色いケーブルを挿すピンは 2 番ピン
Ultrasonic ultrasonic(2);

void setup() {
  // init lcd, serial, but don't init sd card
  M5.begin(true, false, true);

  Wire.begin();
  
  M5.Power.begin();

  M5.Lcd.clear(BLACK);
  M5.Lcd.setTextSize(3);
  M5.Lcd.println("UltraSonic");
  M5.Lcd.print("Ranger");
  M5.Lcd.setTextColor(WHITE);

}

void loop() {
  M5.update();

  M5.Lcd.clear(BLACK);
  M5.Lcd.setCursor(0, 100);

  long RangeInCentimeters;
  RangeInCentimeters = ultrasonic.MeasureInCentimeters();
  Serial.print(RangeInCentimeters); // 0 ~ 400cm
  Serial.println(" cm");

  M5.Lcd.print("Range : ");
  M5.Lcd.print(RangeInCentimeters);
  M5.Lcd.println(" cm");
  
  delay(1000); // データ取得のため 250 ms 以上のインターバルを持つ
}
```

## M5Stack に書き込んでみる

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/bd1d04513fd60ee4739023ac8929f653.jpg)

動かしてみると、このように障害物とセンサーの距離データが表示されます。

![image](https://i.gyazo.com/32056ffe80f0b86f1de9e6e41c27e167.jpg)

手で距離を縮めてみると変化します。

## LINE Notify と連携するソースコードを試す

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-UltraSonicRanger-sensor-LINENotify` というファイル名で保存します。

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

// 今回のセンサーライブラリの呼び出し
#include "Ultrasonic.h"
// 黄色いケーブルを挿すピンは 2 番ピン
Ultrasonic ultrasonic(2);

// カウントダウン用変数 メッセージを送った時間
long messageSentAt = 0;

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

  // 以前の時間から現在の時間 millis() でどれだけ経過したかを計算
  long spanTime = millis() - messageSentAt;

  // 5秒 = 5000ミリ秒に1回送る
  // センサー取得時間は3秒くらいは確保する
  if (spanTime > 5000) {
    // 送った時間を更新
    messageSentAt = millis();
    
    M5.Lcd.clear(BLACK);
    M5.Lcd.setCursor(0, 100);
    M5.Lcd.setTextSize(3);

    // センサーデータを取得
    long RangeInCentimeters;
    RangeInCentimeters = ultrasonic.MeasureInCentimeters();
    Serial.print(RangeInCentimeters); // 0 ~ 400cm
    Serial.println(" cm");

    M5.Lcd.print("Range : ");
    M5.Lcd.print(RangeInCentimeters);
    M5.Lcd.println(" cm");

    // 50 cm 以下は接近とする
    if(RangeInCentimeters < 50){
      // JSON 形式のメッセージを送る
      send_message("(o_o)< ALERT!!");
    } else {
      // JSON 形式のメッセージを送る
      send_message("(-_-)");
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

  delay(1000);
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

## M5Stack で動かしてみる

起動してみると Launched! というメッセージが Gitpod 経由で LINE Notify のほうに送られます。

![image](https://i.gyazo.com/100d5f685229110090f0735a02faf6d5.jpg)

5秒に1回のタイミングで、センサーからデータが取得され距離データが表示されます。

![image](https://i.gyazo.com/977328619a0518c1f117c955bc63e6f3.jpg)

同時に、50cm 以下に近づいたら `(o_o)< ALERT!!` というメッセージ、それ以上距離が離れていたら `(-_-)` というメッセージが Gitpod 経由で LINE Notify のほうに送られます。

![dd155d1c7c85abe3502f7239ac4f0cf7](https://i.gyazo.com/dd155d1c7c85abe3502f7239ac4f0cf7.png)

LINE Notify はこのようにメッセージが送られています。

## 次は

左のメニューから、次に進みましょう。