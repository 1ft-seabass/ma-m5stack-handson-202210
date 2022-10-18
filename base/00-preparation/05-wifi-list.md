# Wi-Fi につなぐ前に周辺の Wi-Fi を確認

## 今回のプログラムはどのように動くか

![image](https://i.gyazo.com/8656356ef8d3754cb5eba0aa4d99b58f.jpg)

書き込みと同時に周辺の Wi-Fi リストを表示します。

## ソースコードを反映

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-00-01-wifi-list` で保存します。

```c
#include <M5Stack.h>
#include "WiFi.h"

// Factory Test を抜粋
// https://github.com/m5stack/M5Stack/blob/master/examples/Basics/FactoryTest/FactoryTest.ino

void wifi_test() {
    WiFi.mode(WIFI_STA);
    WiFi.disconnect();
    delay(100);

    Serial.println("scan start");
    M5.Lcd.println("scan start");

    // WiFi.scanNetworks will return the number of networks found
    int n = WiFi.scanNetworks();
    Serial.println("scan done");
    M5.Lcd.println("scan done");
    if (n == 0) {
        Serial.println("no networks found");
        M5.Lcd.println("no networks found");
    } else {
        Serial.print(n);
        M5.Lcd.print(n);
        Serial.println(" networks found");
        M5.Lcd.println(" networks found");
        for (int i = 0; i < n; ++i) {
            // Print SSID and RSSI for each network found
            Serial.print(i + 1);
            M5.Lcd.print(i + 1);
            Serial.print(": ");
            M5.Lcd.print(": ");
            Serial.print(WiFi.SSID(i));
            M5.Lcd.print(WiFi.SSID(i));
            Serial.print(" (");
            M5.Lcd.print(" (");
            Serial.print(WiFi.RSSI(i));
            M5.Lcd.print(WiFi.RSSI(i));
            Serial.print(")");
            M5.Lcd.print(")");
            Serial.println((WiFi.encryptionType(i) == WIFI_AUTH_OPEN)?" ":"*");
            M5.Lcd.println((WiFi.encryptionType(i) == WIFI_AUTH_OPEN)?" ":"*");
            delay(5);
        }
    }
    Serial.println("");
    M5.Lcd.println("");
}


void setup() {
  
    // initialize the M5Stack object
    M5.begin();
    
    M5.Power.begin();
    
    M5.Lcd.setBrightness(100);
    M5.Lcd.fillScreen(BLACK);
    M5.Lcd.setCursor(10, 10);
    M5.Lcd.setTextColor(WHITE);
    M5.Lcd.setTextSize(2);
    M5.Lcd.printf("WiFi Test!");

    M5.Lcd.setCursor(0, 0);
    M5.Lcd.fillScreen(BLACK);
    wifi_test();
    
}

void loop(){
    M5.update();
}
```

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

書き込みと同時に周辺の Wi-Fi リストを表示します。

![image](https://i.gyazo.com/8656356ef8d3754cb5eba0aa4d99b58f.jpg)

自分の作業場の Wi-Fi 名が表示されてますでしょうか。

これが今後使う Wi-Fi の SSID になります。Wi-Fi につなげるパスワードを思い出しておきましょう。

これでチェックできること

- Wi-Fi が M5Stack で動作していることがわかる
- 自分の作業場の Wi-Fi が M5Stack から認識していることがわかる
  - 実際につながないとわからないことも多いが、つながる確率はかなり高まりました！

## 次は

Wi-Fi につなぐ前に周辺の Wi-Fi を確認できたら、[Wi-Fi をつないでみる](06-wifi-connect.md) にすすみましょう。