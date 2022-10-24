# M5Stack のボタンやディスプレイの実装

## ボタンの詳しい情報

![image](https://i.gyazo.com/adbd8fa5ffc5450b2598126907d912da.png)

ボタンの詳しい情報は、公式ドキュメントを見ると良いです。

[m5\-docs/button\.md at master · m5stack/m5\-docs](https://github.com/m5stack/m5-docs/blob/master/docs/en/api/button.md)

![image](https://i.gyazo.com/921820f0f9fbf88df72acc76d7ee43f9.png)

基本、以前のコードにもあった `M5.BtnA.wasPressed()` の「押されたかどうか」で大体のことはできます。

英語ドキュメントから翻訳ツールを通して整えた内容ですが、以下のようなものがあります。

- `read()`
  - ボタンの状態の読み取りを直接返します。1：押された、0：解放された。
- `isPressed()`
  - Button.read() が最後に呼び出されたときのボタンの状態を返します。1：押された、0：解放された。
- `releasedFor(ミリ秒)`
  - ボタンが押されている（または離されている）かどうかを確認し、ミリ秒単位で指定された時間その状態になっていることを確認します。それに応じてfalse（0）またはtrue（1）を返します。
- `pressedFor(ミリ秒)`
  - ボタンが指定された時間以上押された場合に1を返します。1：押された、0：解放された。
- `lastChange()`
  - ボタンごとの最後の状態を返します。
- `wasReleasefor()`
  - ボタンが離されているかどうかを確認し、ミリ秒単位で指定された時間その状態になっていることを確認します。それに応じてfalse（0）またはtrue（1）を返します。
- `wasReleased()`
  - ボタンが押されるたびに1回だけ1を返します。1：押された、0：解放された。

このように、いろいろ便利な関数があります。

とくに、`pressedFor` のように、ボタンが指定された時間以上押されたかは、自分でイチから組んだ場合はタイマーの実装や、処理が他と重なった時の対応など、たくさんの注意点があるので、すぐに使える関数があるのはありがたいですね。

## ディスプレイの詳しい情報

![image](https://i.gyazo.com/58b30ff54d4fd99dcbb798f3c82147db.png)

ディスプレイの詳しい情報は、公式ドキュメントを見ると良いです。

[m5\-docs/lcd\.md at master · m5stack/m5\-docs](https://github.com/m5stack/m5-docs/blob/master/docs/en/api/lcd.md)

![image](https://i.gyazo.com/ec47767fe7f4f2ada63568a42e7edd96.png)

### すぐ使える定義済みカラーがある

なんといっても、すぐ使える定義済みカラーがあるのは、視認性を高めたり装飾したりと表現に幅が出るのでおススメです。

![image](https://i.gyazo.com/e782a07b4a7ce5356d6c1903384c9166.png)

`M5.Lcd.clear(TFT_ORANGE);` のように使います。一部の定義済みカラーには `TFT_BLACK` が `BLACK` として使えたりするのですが、こういう省略形の文献がハッキリと探せなかったので、試しながら使ってみてください。WHITE , BLACK , BLUE , RED , YELLOW あたりは良く使います。

### そのほかにもいろいろ

そのほかにも

- 丸・三角・ラインの描画
- 読み込んだ JPEG のビットマップ描画
- QR コードの自動生成
- テキストの描画
- プログレスバーの描画などができます

詳しくは以下の文献が分かりやすいので見てみましょう。

- [【M5Stack】第二回 LCDの使い方 全集 \- とある科学の備忘録](https://shizenkarasuzon.hatenablog.com/entry/2020/05/21/012555)
- [【初心者おすすめ】M5Stackライブラリ APIまとめ。公式サイトへのリンク付き。 \| ラズパイの実](https://knt60345blog.com/m5stack_api_list/#toc4)
  - ディスプレイまわりの関数も日本語訳されて分かりやすいです

## テキストのフォントサイズ

正確なレイアウトのためにはフォントサイズの把握は大切です。

[M5Stack Basic と M5Stack Core2 のデフォルトフォントのサイズステップが分かったメモ – 1ft\-seabass\.jp\.MEMO](https://www.1ft-seabass.jp/memo/2021/02/12/m5stack-basic-and-core2-default-fontsize-maybe-7px-knowledge/)

![image](https://i.gyazo.com/c117d8d217095775455aa64fa8fb7df8.jpg)

私の記事を見てみてください。

## 今回のプログラムはどのように動くか

ということで、上記の機能をもう少し踏み込んで作ったプログラムがこちらです。

![image](https://i.gyazo.com/663630ae47c17ce53715f4facbbd0553.jpg)

起動すると PUSH BUTTON! という文字が出てきます。下部には、ボタンの使い方を示すレイアウトが表示されています。

![image](https://i.gyazo.com/1bb9d7a9f43bf616f62bf75b1b1a6bf0.jpg)

A ボタンは OK 、 B ボタンは CANCEL 、 C ボタンは RESET です。

## ソースコードを反映＆保存

Arduino IDE で新規ファイルを作成し、以下のコードをコピーアンドペーストします。こちらを `handson-Button-Display` というファイル名で保存します。

```c
#include <M5Stack.h>


void setup() {
  M5.begin(true, false, true);
  
  M5.Power.begin();

  M5.Lcd.clear(TFT_BLACK);

  // 線を引いてみた目を区切る ちなみに画面サイズは 320 x 240
  M5.Lcd.drawLine(0,200,320,200,WHITE);
  
  // 下部にボタンナビゲーションをつける
  // 描画順は背景から書いて文字を載せる

  // A ボタンの背景
  M5.Lcd.fillRect(28, 204, 80, 26, BLUE);
  M5.Lcd.drawRect(28, 231, 80, 7, BLUE);

  // B ボタンの背景
  M5.Lcd.fillRect(28 + 80 + 15 , 204, 80, 26, RED);
  M5.Lcd.drawRect(28 + 80 + 15, 231, 80, 7, RED);

  // C ボタンの背景
  M5.Lcd.fillRect(28 + 80 + 15 + 80 + 15 , 204, 75, 26, TFT_GREEN);
  M5.Lcd.drawRect(28 + 80 + 15 + 80 + 15, 231, 75, 7, TFT_GREEN);
  
  // A ボタンの説明
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.setTextSize(2);
  M5.Lcd.setCursor(55, 210);  // 結構合ってるけど何度も書き出して目視で合わせている
  M5.Lcd.print("OK");

  // B ボタンの説明
  M5.Lcd.setCursor(127, 210);
  M5.Lcd.print("CANCEL");

  // C ボタンの説明
  M5.Lcd.setTextColor(BLACK);
  M5.Lcd.setCursor(227, 210);
  M5.Lcd.print("RESET");

  // テキストを真ん中あたりに出す
  M5.Lcd.setTextSize(4);
  M5.Lcd.setCursor(20, 85);
  M5.Lcd.setTextColor(WHITE);
  M5.Lcd.print("PUSH BUTTON!");

}

void loop() {
  M5.update();
  
  if (M5.BtnA.wasReleased()) {
    // 上部だけ背景色で塗りつぶす
    M5.Lcd.fillRect(0, 0, 320, 199, BLUE);
    // テキストを真ん中あたりに出す
    M5.Lcd.setTextSize(5);
    M5.Lcd.setCursor(60, 80);
    M5.Lcd.setTextColor(BLACK);
    M5.Lcd.print("OK ^_^/");
    // 下部の選択状態
    M5.Lcd.fillRect(0, 231, 320, 7, BLACK); // 下部を細く黒で塗りつぶし
    M5.Lcd.fillRect(28, 231, 80, 7, WHITE);  // 選択ホワイト
    M5.Lcd.drawRect(28 + 80 + 15, 231, 80, 7, RED);
    M5.Lcd.drawRect(28 + 80 + 15 + 80 + 15, 231, 75, 7, TFT_GREEN);
  } else if (M5.BtnB.wasReleased()) {
    // 上部だけ背景色で塗りつぶす
    M5.Lcd.fillRect(0, 0, 320, 199, RED);
    // テキストを真ん中あたりに出す
    M5.Lcd.setTextSize(5);
    M5.Lcd.setCursor(80, 80);  // わかる。ちょっと中央に寄ってないよね。
    M5.Lcd.setTextColor(BLACK);
    M5.Lcd.print("CANCEL");
    // 下部の選択状態
    M5.Lcd.fillRect(0, 231, 320, 7, BLACK); // 下部を細く黒で塗りつぶし
    M5.Lcd.drawRect(28, 231, 80, 7, BLUE);
    M5.Lcd.fillRect(28 + 80 + 15, 231, 80, 7, WHITE);  // 選択ホワイト
    M5.Lcd.drawRect(28 + 80 + 15 + 80 + 15, 231, 75, 7, TFT_GREEN);
  } else if (M5.BtnC.wasReleased()) {
    // 上部だけ背景色で塗りつぶす
    M5.Lcd.fillRect(0, 0, 320, 199, TFT_GREEN);
    // テキストを真ん中あたりに出す
    M5.Lcd.setTextSize(5);
    M5.Lcd.setCursor(90, 80);
    M5.Lcd.setTextColor(BLACK);
    M5.Lcd.print("RESET");
    // 下部の選択状態
    M5.Lcd.fillRect(0, 231, 320, 7, BLACK); // 下部を細く黒で塗りつぶし
    M5.Lcd.drawRect(28, 231, 80, 7, BLUE);
    M5.Lcd.drawRect(28 + 80 + 15, 231, 80, 7, RED);
    M5.Lcd.fillRect(28 + 80 + 15 + 80 + 15, 231, 75, 7, WHITE);  // 選択ホワイト
    // 2 秒後、表示リセットする /////////////////////////
    delay(2000);
    // 上部だけ背景色で塗りつぶす
    M5.Lcd.fillRect(0, 0, 320, 199, TFT_BLACK);
    // テキストもリセット
    M5.Lcd.setTextSize(4);
    M5.Lcd.setCursor(20, 85);
    M5.Lcd.setTextColor(WHITE);
    M5.Lcd.print("PUSH BUTTON!");
    // 下部もリセット
    M5.Lcd.fillRect(0, 231, 320, 7, BLACK); // 下部を細く黒で塗りつぶし
    M5.Lcd.drawRect(28, 231, 80, 7, BLUE);
    M5.Lcd.drawRect(28 + 80 + 15, 231, 80, 7, RED);
    M5.Lcd.drawRect(28 + 80 + 15 + 80 + 15, 231, 75, 7, WHITE);
  }
}
```

## M5Stack に書き込んでみる

![image](https://i.gyazo.com/45b0fd6ce672dc9a0055d45aa290e235.png)

M5Stack に書き込んでみましょう。

## 動かしてみる

![image](https://i.gyazo.com/663630ae47c17ce53715f4facbbd0553.jpg)

起動すると PUSH BUTTON! という文字が出てきます。下部には、ボタンの使い方を示すレイアウトが表示されています。

![image](https://i.gyazo.com/1bb9d7a9f43bf616f62bf75b1b1a6bf0.jpg)

A ボタンは OK 、 B ボタンは CANCEL 、 C ボタンは RESET です。

このように、ディスプレイの表示も Web におけるスマートフォンでの表示までは自由に行かなくとも、同じような配慮でつくることができ、人が迷いなく触れるナビゲーションを作れることを体験してみてください。

もちろん、絵づくりは大変なところもありますが、楽しんでやってみましょう～。

## 次は

左のメニューから、次に進みましょう。