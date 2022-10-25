# Airtable にデータを書き込んでみよう

## Airtable というクラウド型データベース

Airtable https://www.airtable.com/ というクラウド型データベースを触ってみます。

![e5f34a332182a4e0a07abedd1354df68](https://i.gyazo.com/e5f34a332182a4e0a07abedd1354df68.png)

Google スプレッドシートのような見た目で Web から GUI でデータを作成・更新・削除・検索できます。また、API も充実していて使いやすくデータを操作することができます。

![3774deda3b412fb22b9e50b95d2e4a88](https://i.gyazo.com/3774deda3b412fb22b9e50b95d2e4a88.png)

2022 年 10 月現在 Pricing https://airtable.com/pricing を見てみると

　V 無制限の Base
　V Base あたり1,200レコード
　V Base あたり2GBの添付ファイル
　V グリッド、カレンダー、かんばん、フォーム、およびギャラリービュー

が無料枠で含まれるので、とりあえず触ってみても基本的な機能を体験しやすいものになっています。

## 参考文献

以下の記事を参考に進めていきます。

M5Stack から HTTPS で Airtable にデータを書き込むメモ – 1ft-seabass.jp.MEMO
https://www.1ft-seabass.jp/memo/2021/10/07/m5stack-airtable-api-connect/

WiFiClientSecure でなく HTTPClient を使って M5Stack から HTTPS で Airtable にデータを書き込むメモ – 1ft-seabass.jp.MEMO
https://www.1ft-seabass.jp/memo/2022/05/14/m5stack-airtable-api-connect-using-httpclient/

## こんな風に動きます

![8e887fd687c9fc61872cd58ef4665f8b](https://i.gyazo.com/8e887fd687c9fc61872cd58ef4665f8b.jpg)

https://twitter.com/1ft_seabass/status/1445907473429188613

このように M5Stack からボタンを押すと、Airtable に直接データが保存されます！アツい。

## Airtable の Base の準備

Airtable にログインします。

![8dd1852c028cfa78a1d0f69744b730e5](https://i.gyazo.com/8dd1852c028cfa78a1d0f69744b730e5.png)

https://airtable.com/shrIlpDKwspRHenHm

こちらの URL で今回使う Base の自動で作ってくれるひな形（テンプレート）を準備します。

![894d3fed4cf14837cc40a7da1c623f58](https://i.gyazo.com/894d3fed4cf14837cc40a7da1c623f58.png)

Use this data ボタンをクリックします。

![d877169f6e0222a296370243b37f18ed](https://i.gyazo.com/d877169f6e0222a296370243b37f18ed.png)

このひな形を自分のワークスペースにどう使うかが聞かれます。

![113b1e7f919520b5bd83ad1a9f23b913](https://i.gyazo.com/113b1e7f919520b5bd83ad1a9f23b913.png)

自分のワークスペースを選びます。

![d28cbda4d6a417b95636842b553675f8](https://i.gyazo.com/d28cbda4d6a417b95636842b553675f8.png)

Base は今回新しく作るので Create new base in Workspace をクリックします。

![69e514d64c6127caca7d23e968a6111b](https://i.gyazo.com/69e514d64c6127caca7d23e968a6111b.png)

設定できたら Create table ボタンをクリックします。

![2a5a28fa881b1990caab757e8322b5e0](https://i.gyazo.com/2a5a28fa881b1990caab757e8322b5e0.png)

今回の Base に移動して出来上がりました。これで作成完了です。

### もし自分でつくる場合は

もし自分でつくる場合は、以下の手順を行いましょう。（今回のハンズオン内では、上記のひな型からの作成です）

![8b5382feffb40991dd4adb70e2326aac](https://i.gyazo.com/8b5382feffb40991dd4adb70e2326aac.png)

Airtable には Add a base ボタンをクリックして Start from scratch ボタンで作られる、Base の内容をそのまま利用しています。

![cdc50243e590b6bdf331d02040109d64](https://i.gyazo.com/cdc50243e590b6bdf331d02040109d64.png)

Name , Notes , Attachments , Status がある初期状態のテーブルがあります。

- Notes , Attachments , Status のフィールドを削除
- 残った Name のフィールドの名前を Data に変更
- 3 行の初期の空データがあるので選択して削除

の対応をします。

![94256b33bcaf219e696e963dc783d47d](https://i.gyazo.com/94256b33bcaf219e696e963dc783d47d.png)

テーブルの名前は「Table 1」と、`Table` のあとにスペースが1つあったうえで `1`がついています。このスペースをどのように送るかが、ちょっとした注意ポイントとしてあるので覚えておいてください。

## Arduino ソースコード

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Airtable` というファイル名で保存します。

```c
#include <M5Stack.h>
#include <HTTPClient.h> // WiFiClientSecure から HTTPClient 変更

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

}

void send_message(String msg) {

  // Airtable の自分のトークン
  // https://airtable.com/account から自分の API Key を反映します。
  String airtableToken = "Airtable の自分のトークン";

  // Airtable の Base 名
  // https://airtable.com/api に各Baseでの HTTP 仕様が確認できるので、そこから Base ID を反映します。
  String airtableBaseName = "Airtable の Base 名";

  // AirTable の Table 名
  // Table 名は、AirTable の表示上では Table 1。
  // %20 はスペースを HTTP で送るためにエンコードしているので Table%201 と指定しています。
  String airtableTableName = "Table%201";
  
  M5.Lcd.fillScreen(BLACK);
  M5.Lcd.setCursor(10, 10);
  M5.Lcd.println("-> Airtable send data");
  Serial.println("-> Airtable send data");
  M5.Lcd.print("msg: ");
  Serial.print("msg: ");
  M5.Lcd.println(msg);
  Serial.println(msg);

  // 送るデータを JSON で。
  String queryString = "{\"records\": [{\"fields\": {\"Data\": \"" + msg + "\"}}]}";
  // Airtable URL 作成
  String url = "https://api.airtable.com/v0/" + airtableBaseName + "/" + airtableTableName;
  
  HTTPClient httpClient;
  
  // URL 設定
  httpClient.begin(url);
  // Content-Type
  httpClient.addHeader("Content-Type", "application/json");
  // Authorization
  httpClient.addHeader("Authorization", "Bearer " + airtableToken);
  // ポストする
  int status_code = httpClient.POST(queryString);
  if( status_code == 200 ){
    String response = httpClient.getString();

    M5.Lcd.println("sended.");
    Serial.println("sended.");
    
    M5.Lcd.println("response:");
    M5.Lcd.println(response);
  }
  httpClient.end();

  delay(2000);
}

void loop() {
  M5.update();

  if (M5.BtnA.wasReleased()) {
    // A ボタンを押したらテキストメッセージを送る
    send_message("Pushed A");
  } else if (M5.BtnB.wasReleased()) {
    // B ボタンを押したらテキストメッセージを送る
    send_message("Pushed B");
  } else if (M5.BtnC.wasReleased()) {
    // C ボタンを押したらテキストメッセージを送る
    send_message("Pushed C");
  }
}
```

## Wi-Fi を設定する

```c
// Wi-FiのSSID
char *ssid = "Wi-FiのSSID";
// Wi-Fiのパスワード
char *password = "Wi-Fiのパスワード";
```

こちらには M5Stack が接続する Wi-Fi を設定します。

## 自分の API Key を反映

```c
  // Airtable の自分のトークン
  // https://airtable.com/account から自分の API Key を反映します。
  const char* airtableToken = "AirTable の自分のトークン";
```

こちらは自分のアカウントページから https://airtable.com/account から API という項目を探して、自分の API Key をクリックしてコピーして反映します。

![61ff567c78ad275cfb7ad4c7705f9c51](https://i.gyazo.com/61ff567c78ad275cfb7ad4c7705f9c51.png)

## AirTable の Base ID を反映

```c
  // Airtable の Base ID
  // https://airtable.com/api に各Baseでの HTTP 仕様が確認できるので、そこから Base ID を反映します。
  const char* airtableBaseName = "Airtable の Base 名";
```

こちらは Airtable の Base ID を反映します。

![e3f97450f6866dca3c7ed4b9f218f414](https://i.gyazo.com/e3f97450f6866dca3c7ed4b9f218f414.png)

Airtable REST https://airtable.com/api からに各 Base での HTTP 仕様が確認できます。

![8713a9d2030e6ca13f11767639540d4f](https://i.gyazo.com/8713a9d2030e6ca13f11767639540d4f.png)

今回の Base を探してクリックします。

![fecf74d9b66006bedc3279e61a45a611](https://i.gyazo.com/fecf74d9b66006bedc3279e61a45a611.png)

今回の API のINTRODUCTION ページが表示されるので、こちらから Base ID を取得して反映します。最後のピリオドは、いりません。

## AirTable の Table 名 に注目

すでに設定済みですが、Table 名は、Airtable の表示上では Table 1。`Table` のあとにスペースが1つあったうえで `1`がついています。%20 はスペースを HTTP で送るためにエンコードしているので Table%201 と指定しています。

```c
  // AirTable の Table 名
  // Table 名は、Airtable の表示上では Table 1。
  // %20 はスペースを HTTP で送るためにエンコードしているので Table%201 と指定しています。
  const char* airtableTableName = "Table%201";
```

## 保存して M5Stack に書き込む

Airtable ホスト や API への URL については既にソースコードに書いてあるので、ここまでの設定ができたら保存して M5Stack に書き込みましょう。

![dc6c0318b2393b560c0ee1539529499d](https://i.gyazo.com/dc6c0318b2393b560c0ee1539529499d.jpg)

起動後、うまく Wi-Fi がつながったら、このように Connected と表示されます。

## 動かしてみる

![bb9d74211555f020237ec5a6c22409e9](https://i.gyazo.com/bb9d74211555f020237ec5a6c22409e9.png)

M5Stack の A B C ボタンを押すと、このように動作して、Airtable で今回の Base を見てみると押したキーのテキストが保存されます！

## 次は

左のメニューから、次に進みましょう。