# Arduino でコンパイル検証

Arduino の設定ができたら、Arduino で M5Stack のソースコードがちゃんとコンパイルできるかコンパイル検証してみます。

## 注意

macOS Monterey をお使いの方は、Python がの環境まわりの影響で、追加の設定が必要かもしれません。

## このページで分かること

- M5Stack のボード設定が正しくインストールできているか
- M5Stack のライブラリが正しくインストールできているか
- M5Stack に書き込むプログラムが正しくコンパイルできるか

## コンパイルできるか検証します

![88be6e0a8c72e7de4e606a43952a7260](https://i.gyazo.com/88be6e0a8c72e7de4e606a43952a7260.png)

メニューから ファイル > 新規ファイル をクリックします。

![f3de55bf803bf58e59d3be78ac4f1796](https://i.gyazo.com/f3de55bf803bf58e59d3be78ac4f1796.png)

あたらしく新規ファイルのウィンドウが開きました。

![9bb3b5f4977db6a4b07567174141cb58](https://i.gyazo.com/9bb3b5f4977db6a4b07567174141cb58.png)

あたらしく新規ファイルのウィンドウの既存コードをすべて選択して削除します。

```c
#include <M5Stack.h>

void setup() {
  M5.begin();

  M5.Lcd.println("Hello World");
}

void loop() {

}
```

こちらのソースコードをコピーアンドペーストします。

![b2b748a47bf14190ea34e6d67a31fd45](https://i.gyazo.com/b2b748a47bf14190ea34e6d67a31fd45.png)

このようになります。

![15bd321ce4ee6c70bb5e4160b3552e93](https://i.gyazo.com/15bd321ce4ee6c70bb5e4160b3552e93.png)

メニューから ファイル > 保存 をクリックします。

![f83083376f8808b1f545fd136824b6ea](https://i.gyazo.com/f83083376f8808b1f545fd136824b6ea.png)

 `handson-00-01-firststep` というファイル名で保存します。

![3cb0b0bb8ef662a84b422fd9dae3703f](https://i.gyazo.com/3cb0b0bb8ef662a84b422fd9dae3703f.png)

メニューから ツール > ボード > M5Stack Arduino > M5Stack-Core-ESP32 をクリックします。

![3cb0b0bb8ef662a84b422fd9dae3703f](https://i.gyazo.com/3cb0b0bb8ef662a84b422fd9dae3703f.png)

もう一度、メニューから ツール をクリックして、このようにボードで M5Stack-Core-ESP32 が設定されているか確認します。

![1c563a06d93c49554e1cee5b0d249d4a](https://i.gyazo.com/1c563a06d93c49554e1cee5b0d249d4a.png)

ボード設定ができたら検証ボタンをクリックします。

![b04e04c0a066039bc4b040ce17254e1d](https://i.gyazo.com/b04e04c0a066039bc4b040ce17254e1d.png)

スケッチをコンパイルしていますとなって表示されます。通常のパワーの PC であれば 1 ～ 2 分程度でコンパイルできます。

![e96859fb5d37f1909b74aab297bac42f](https://i.gyazo.com/e96859fb5d37f1909b74aab297bac42f.png)

コンパイルが完了しましたと表示されたら成功です。

> 最大1310720バイトのフラッシュメモリのうち、スケッチが345025バイト（26%）を使っています。
最大327680バイトのRAMのうち、グローバル変数が18700バイト（5%）を使っていて、ローカル変数で308980バイト使うことができます。

このようなメッセージが出ているはずです。

## 次は

Arduino でコンパイル検証ができたら [USB ドライバのインストール](03-usb-driver-install.md) を進めましょう。