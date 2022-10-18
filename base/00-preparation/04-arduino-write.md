# M5Stack へ書き込み

[Arduino でコンパイル検証](02-arduino-test.md) で試したプログラムをいよいよ M5Satck に書き込みます。

## PC と M5Stack をつなぐまえにシリアルポートを確認

![4c17ab5e62bb0a097a5fd09bafbce963](https://i.gyazo.com/4c17ab5e62bb0a097a5fd09bafbce963.png)

PC と M5Stack をつなぐまえにシリアルポートを確認しておきましょう。

このあと M5Stack つないで、新しく登場したものが M5Stack のシリアルポートです。

## M5Stack と PC をつなぐ

![image](https://i.gyazo.com/903d4cda78f3cf10c84f036cad08fe03.jpg)

M5Stack に付属していた USB ケーブルを用意します。

![image](https://i.gyazo.com/2a86c7f1b721555abfaf0105f161ea9f.jpg)

PC と M5Stack をつなぎます。

## M5Stack のポートを見つけて設定

![image](https://i.gyazo.com/595100dc326e18141cc74ab745a3475a.png)

Windows 10 の場合、Arduino IDE の ツール > シリアルポート に、`COM3` や `COM1` のような `COM` が頭にあるシリアルポート名が表示されていれば無事 M5Stack 用の USB ドライバがインストールされて M5Stack が認識されています。

![image](https://i.gyazo.com/9336adb21f951ddeb5f706b54b8b3923.png)

Mac の場合は、Arduino IDE の ツール > シリアルポート に `/dev/tty.SLAB_USBtoUART` のような `/dev/tty.SLAB_` が頭にあるシリアルポート名が表示されていれば無事 M5Stack 用の USB ドライバがインストールされて M5Stack が認識されています。

## 書き込む

![0b43e6f6729f5dc077bcfc197179a321](https://i.gyazo.com/0b43e6f6729f5dc077bcfc197179a321.png)

マイコンボードを書き込むボタンをクリックします。

![image](https://i.gyazo.com/bbbda50bc7f265d291cb3a803e7924b1.png)

コンパイルを待ちます。

![image](https://i.gyazo.com/459cb6b9ad74028375743347a5d6a5af.png)

このようなログと共に書き込まれます。

以下がログの全文です。参考までに。

```
最大1310720バイトのフラッシュメモリのうち、スケッチが347205バイト（26%）を使っています。
最大327680バイトのRAMのうち、グローバル変数が17596バイト（5%）を使っていて、ローカル変数で310084バイト使うことができます。
esptool.py v3.0-dev
Serial port COM3
Connecting.....
Chip is ESP32-D0WDQ6-V3 (revision 3)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: 08:3a:f2:44:60:74
Uploading stub...
Running stub...
Stub running...
Changing baud rate to 921600
Changed.
Configuring flash size...
Auto-detected Flash size: 16MB
Compressed 8192 bytes to 47...
Writing at 0x0000e000... (100 %)
Wrote 8192 bytes (47 compressed) at 0x0000e000 in 0.0 seconds (effective 10922.6 kbit/s)...
Hash of data verified.
Flash params set to 0x024f
Compressed 17392 bytes to 11186...
Writing at 0x00001000... (100 %)
Wrote 17392 bytes (11186 compressed) at 0x00001000 in 0.2 seconds (effective 909.4 kbit/s)...
Hash of data verified.
Compressed 347328 bytes to 154525...
Writing at 0x00010000... (10 %)
Writing at 0x00014000... (20 %)
Writing at 0x00018000... (30 %)
Writing at 0x0001c000... (40 %)
Writing at 0x00020000... (50 %)
Writing at 0x00024000... (60 %)
Writing at 0x00028000... (70 %)
Writing at 0x0002c000... (80 %)
Writing at 0x00030000... (90 %)
Writing at 0x00034000... (100 %)
Wrote 347328 bytes (154525 compressed) at 0x00010000 in 2.6 seconds (effective 1074.1 kbit/s)...
Hash of data verified.
Compressed 3072 bytes to 128...
Writing at 0x00008000... (100 %)
Wrote 3072 bytes (128 compressed) at 0x00008000 in 0.0 seconds (effective 3510.9 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```

## 動かしてみる

書き込まれたら M5Stack を確認しましょう。

![image](https://i.gyazo.com/8b62e0c3bf9dad23c0e9ca6362aea085.jpg)

無事に書き込まれると `Hello World` の文が表示されます。成功です！

![image](https://i.gyazo.com/a7c051278279d9fb57ca6ce2e10bcb76.jpg)

そう、最小のフォントサイズ 1 だとこんなに小さいんですが、キレイに表示されるのがすごいですね。

## 次は

M5Stack へ書き込みができたら、[Wi-Fi につなぐ前に周辺の Wi-Fi を確認](05-wifi-list.md) にすすみましょう。