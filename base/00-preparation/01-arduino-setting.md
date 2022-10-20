# Arduino の設定

Arduino をインストールできたら、Arduino の設定をします。

Arduino を起動して進めます。

## 環境設定

![fc0e601a5188008571d271e7f29f3d14](https://i.gyazo.com/fc0e601a5188008571d271e7f29f3d14.png)

https://docs.m5stack.com/en/arduino/arduino_development を参考にメニューから ファイル > 環境設定 をクリックします。

![fc0e601a5188008571d271e7f29f3d14](https://i.gyazo.com/fc0e601a5188008571d271e7f29f3d14.png)

環境設定が起動しました。

![9bb6202649e74b640d8f180a7fc5b244](https://i.gyazo.com/9bb6202649e74b640d8f180a7fc5b244.png)

こちらのボタンをクリックします。

![beab8efcd4ef726c905cd925d1cc26ba](https://i.gyazo.com/beab8efcd4ef726c905cd925d1cc26ba.png)

追加のボードマネージャの URL ウィンドウが表示されます。

![ce9c4e9e6e52201c7155d0bd86ae6647](https://i.gyazo.com/ce9c4e9e6e52201c7155d0bd86ae6647.png)

入力エリアに `https://m5stack.oss-cn-shenzhen.aliyuncs.com/resource/arduino/package_m5stack_index.json` を入力して OK ボタンをクリックします。

![03b26200dcd91f6df94b2d5bd07a512b](https://i.gyazo.com/03b26200dcd91f6df94b2d5bd07a512b.png)

ウィンドウが閉じた後に「追加のボードマネージャの URL：」の部分が先ほどの URL になっていることを確認します。

![e2f5ff82c72b91d79722e7d44a295a88](https://i.gyazo.com/e2f5ff82c72b91d79722e7d44a295a88.png)

確認できたら、右下の OK ボタンをクリックして、環境設定ウィンドウを閉じます。

## M5Stack ボード設定のインストール

![03ab82cd3f3a66b28f19c05a068d7083](https://i.gyazo.com/03ab82cd3f3a66b28f19c05a068d7083.png)

メニューから ツール > ボード > ボードマネージャ をクリックします。

![46a1a7f380411e17345f3322a97bcd03](https://i.gyazo.com/46a1a7f380411e17345f3322a97bcd03.png)

ボードマネージャが表示されます。

![1279148f9af2f8e5d788505d4a387e41](https://i.gyazo.com/1279148f9af2f8e5d788505d4a387e41.png)

上部で M5Stack で検索します。

![77c2a471008c0716951a60909bb08d34](https://i.gyazo.com/77c2a471008c0716951a60909bb08d34.png)

M5Stack が一つ検索されました。

<img width="196" alt="image.png (1.0 kB)" src="https://img.esa.io/uploads/production/attachments/3062/2022/10/12/8131/813ff0c6-9d40-4454-bb77-af3f2dbc8ceb.png">

右下でバージョンを 2.0.1 を選択してインストールボタンをクリックします。

![3e072e3a8ef553f0ec27529c9a91948d](https://i.gyazo.com/3e072e3a8ef553f0ec27529c9a91948d.png)

ボードの定義がインストールされます。しばらく待ちます。

![93ce6a403a4a6ef253d7dd5b660eaa79](https://i.gyazo.com/93ce6a403a4a6ef253d7dd5b660eaa79.png)

インストールが成功すると、このように INSTALLED になります。インストールが確認出来たら閉じるボタンをクリックします。

## M5Stack ライブラリのインストール

![3198841600a43c5272c81d148307a0dc](https://i.gyazo.com/3198841600a43c5272c81d148307a0dc.png)

メニューから ツール > ライブラリを管理 をクリックします。

![0abf087fc87ba6c0266827ec480e0a1e](https://i.gyazo.com/0abf087fc87ba6c0266827ec480e0a1e.png)

ライブラリマネージャが表示されます。

![96ed4adfb8c47922e9dcc83eb65306d8](https://i.gyazo.com/96ed4adfb8c47922e9dcc83eb65306d8.png)

上部で M5Stack Core development kit で検索します。

![d2b92bfbcc50ae486cfb5d18d9dd85a5](https://i.gyazo.com/d2b92bfbcc50ae486cfb5d18d9dd85a5.png)

M5Stack を探します。

![0b7b3036ee8611a7a735b8d406510b33](https://i.gyazo.com/0b7b3036ee8611a7a735b8d406510b33.png)

右下でバージョンを 0.4.1 を選択してインストールボタンをクリックします。

![b03393f87085364c201a513dce70cce8](https://i.gyazo.com/b03393f87085364c201a513dce70cce8.png)

依存ライブラリのインストールが聞かれるので Install all をクリックします。

![743535157101a7925614450cbe25b8dc](https://i.gyazo.com/743535157101a7925614450cbe25b8dc.png)

インストールが進みます。

![56f1d586a8f3215f24dcfc1a1a554f56](https://i.gyazo.com/56f1d586a8f3215f24dcfc1a1a554f56.png)

このようにインストールが成功すると M5Stack が INSTALLED になります。

確認出来たら閉じるボタンをクリックします。

### no protocol になった人は一度閉じて再度ライブラリマネージャを表示してみましょう。

![6c5f7ae5c25de83045ed4548d90ea4d0](https://i.gyazo.com/6c5f7ae5c25de83045ed4548d90ea4d0.png)

まれに、インストールが終わったのに INSTALLED にならず左下に no protocol と出てしまう場合があります。

このときは右下の OK ボタンをクリックして、再度ライブラリマネージャを表示して再度 M5Stack Core development kit で検索します。

![56f1d586a8f3215f24dcfc1a1a554f56](https://i.gyazo.com/56f1d586a8f3215f24dcfc1a1a554f56.png)

このようにインストールが成功していれば M5Stack が INSTALLED になります。INSTALLED になっていなければ、再度インストールしてみましょう。

確認出来たら閉じるボタンをクリックします。

## 次は

Arduino の設定ができたら [Arduino でコンパイル検証](02-arduino-test.md) に進みます。