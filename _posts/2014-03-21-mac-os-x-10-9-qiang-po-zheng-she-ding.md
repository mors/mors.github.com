---
layout: post
title: Mac OS X 10.9 強迫症設定
---


我個人有非常多的強迫症地方，比方現在要說的 Dock 設定大小 Dock 圖示正常時的大小設定為 32px。

```
$ defaults write com.apple.dock tilesize -float 48
```

滑鼠滑入後放大的大小要是 48px。

```
$ defaults write com.apple.dock largesize -float 68
```

Launchpad 應用程式排列方式

```
$ defaults write com.apple.dock springboard-columns -int 10
$ defaults write com.apple.dock springboard-rows -int 8
```

重新啟動 Launchpad

```
$ Killall Dock
```

在重新安裝 Mac OSX 後，我也會把 Time Machine 在本機端的備份關閉。

```
$ sudo tmutil disable-local
```

另外 Mac OS X 10.8 本身就已經安裝好 Apache 2.2.22 和 PHP 5.3.13，所以也會將它們全開啓。

```
$ sudo apachectl start
```

預設 localhost 位置在 /Library/WebServer/Documents/，所以可以繼續將 Users 增加進去。 至 /etc/apache2/users/ 資料夾中新增 UserName.conf 檔案。

```
$ cd /etc/apache2/users
$ sudo nano UserName.conf
```

接著輸入：

```
<Directory "/Users/UserName/Sites/">
    Options Indexes MultiViews
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

重新啓動 Apache 後就完成，並且可以在 http://localhost/~UserName/ 瀏覽。

```
$ sudo apachectl restart
```

再來就是開啟 PHP 5.3.13 支援。前往 /etc/apache2/ 編輯 httpd.conf 檔案，將 ```LoadModule php5_module libexec/apache2/libphp5.so``` 這行前面的 # 字號拿掉即可。（在 nano 環境裡可以使用 Control+W 來搜尋關鍵字）。

最後一步也是要重新啟動 Apache。

```
$ sudo apachectl restart
```
	
