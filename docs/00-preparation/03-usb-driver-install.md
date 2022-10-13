# USB ドライバのインストール

今回の M5Stack V2.6 は USB ドライバのインストールが必要です。

USB ドライバのインストールをします。

## USB ドライバのインストール（Windows）

<a href="https://mag.switch-science.com/2021/11/01/m5stack-v2-6-changes/" target="_blank">M5Stack v2.6の変更点とCH9102Fのドライバについて – スイッチサイエンス マガジン</a>

こちらの記事を参考にします。

https://docs.m5stack.com/en/download

にアクセスします。

![fd9e63f36ac977b6b419243a7752fe36](https://i.gyazo.com/fd9e63f36ac977b6b419243a7752fe36.png)

USB DRIVER & OPEN SOURCE LIBRARY の欄から CH9102_VCP_SER_Windows をクリックしてダウンロードします。

ダウンロードしたexeファイルを実行することでドライバのインストールができます。

![61b62193ac55e0de8e086dc0ecd40c74](https://i.gyazo.com/61b62193ac55e0de8e086dc0ecd40c74.png)

このようなダイアログがでたら実行ボタンをクリックします。

![95ee3d7870a698264169ced599131eab](https://i.gyazo.com/95ee3d7870a698264169ced599131eab.png)

インストールが進みこのようなウィンドウが表示されるので INSTALL ボタンをクリックします。

![0dac1cb0b0f28902b27b732adb66aece](https://i.gyazo.com/0dac1cb0b0f28902b27b732adb66aece.png)

このような画面が出れば成功です。OK ボタンをクリックします。

![868d208146025dfb8218ad730dfbd7a5](https://i.gyazo.com/868d208146025dfb8218ad730dfbd7a5.png)

右上の × ボタンをクリックして閉じれば終了です。

## USB ドライバのインストール（Mac）

<a href="https://mag.switch-science.com/2021/11/01/m5stack-v2-6-changes/" target="_blank">M5Stack v2.6の変更点とCH9102Fのドライバについて – スイッチサイエンス マガジン</a>

こちらの記事を参考にします。

https://docs.m5stack.com/en/download

にアクセスします。

![fd9e63f36ac977b6b419243a7752fe36](https://i.gyazo.com/fd9e63f36ac977b6b419243a7752fe36.png)

USB DRIVER & OPEN SOURCE LIBRARY の欄から CH9102_VCP_SER_MacOS v1.7 をクリックしてダウンロードします。

インストールは

> Macの場合はCH9102_VCP_SER_MacOSをダウンロードします。ダウンロードしたdmgファイルを開き、中に入っているCH34xVCPDriver.appをApplicationsフォルダにコピーし、そのappファイルを実行することでインストールすることができます。

を参考して進めます。

## 次は

USB ドライバのインストールができたら、[M5Stack へ書き込み](04-arduino-write.md) にすすみましょう。