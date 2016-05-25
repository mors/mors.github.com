---
layout: post
title: 簡單安裝 AMAZON EC2 + S3
---


使用 Amazon EC2 Linux 快速安裝 LAMP（Linux, Apache, MySQL, PHP）並串聯 S3 環境。

首先是設定時區時間：

```
$ tzselect
```

如果需要，當然可以更改伺服器名稱。

```
$ sudo hostname serv
$ sudo vim /etc/sysconfig/network
$ sudo /etc/init.d/network restart
$ sudo reboot
```

安裝前建議先更新系統，並使用安裝包來進行。

```
$ sudo yum update -y
$ sudo yum groupinstall -y "Web Server"
$ sudo yum groupinstall -y "MySQL Database"
$ sudo yum groupinstall -y "PHP Support"
$ sudo yum install -y php-mysql
```

接著，啓動 Apache 並加入開機程序。

```
$ sudo service httpd start
$ sudo chkconfig httpd on
```

增加 www 的群組，也讓自己 ec2-user 加入。

```
$ sudo groupadd www
$ sudo usermod -a -G www ec2-user
```

把 Apache 預設資料夾加入 www 群組使用權限。

```
$ sudo chown -R root:www /var/www
$ sudo chmod 2775 /var/www
$ find /var/www -type d -exec sudo chmod 2775 {} +
$ find /var/www -type f -exec sudo chmod 0664 {} +
```

再來就是進行 MySQL 的初步設定，並且把 MySQL 也加入開機程序。

```
$ sudo service mysqld start
$ sudo mysql_secure_installation
$ sudo chkconfig mysqld on
```

以上的步驟就已經完成 LAMP 的安裝。是不是很簡單 :) 接著要安裝設定與 S3 串連起來。

安裝所需的檔案：

```
$ sudo yum install -y gcc libstdc++-devel gcc-c++ fuse fuse-devel curl-devel libxml2-devel openssl-devel mailcap
```

我選用的是 S3FS，並在 User 內開個 Softw 的資料夾，把些安裝檔案放裡面，便於我整理和查詢。

```
$ mkdir softw
$ wget http://s3fs.googlecode.com/files/s3fs-1.74.tar.gz
$ tar zxf s3fs-1.74.tar.gz
$ cd s3fs-1.74
$ ./configure --prefix=/usr
$ make
$ sudo make install
```

在 /etc 內建立 passwd-s3fs 檔案鍵入 S3 的帳號密碼。

```
$ sudo vim /etc/passwd-s3fs
```

檔案內的規則是 ```bucketName:accessKeyId:secretAccessKey``` 或 ```accessKeyId:secretAccessKey```。儲存檔案後，為了安全性，必須更改 passwd-s3fs 的檔案權限。

```
$ sudo chmod 640 /etc/passwd-s3fs
```

最後步驟，是在 /mnt 內建立你要串連的資料夾名稱，並設定它。

```
$ sudo mkdir /mnt/s3static
$ sudo s3fs bucketName -o use_cache=/tmp -o allow_other /mnt/s3static
```

當然，你也可以在其他資料夾內設定串連，比如是某些會繼續成長變大的資料夾。