---
layout: post
title: Ubuntu 14.04 安裝 PPTPD
---


首先安裝 pptpd。

```
$ apt-get install pptpd
```

開啓 VPN 端口防火牆，記得 SSH 端口也必須開啟。

```
$ ufw allow 1723/tcp
```

接著，配置 IP。

```
$ nano /etc/pptpd.conf

# 可以跟改為自己喜歡的配置。
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```

配置 DNS。

```
$ nano /etc/ppp/pptpd-options

# 我是將 DNS 指向給 GOOGLG，當然也可以改為自己習慣的配置。
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

設定帳號密碼。

```
$ nano /etc/ppp/chap-secrets

# 依序分別為帳號、伺服器、密碼、IP。
username	pptpd   password    *
```

設定完成後，重新啟動。

```
$ service pptpd restart
```

再來就是進行 NAT 需要的設定。

```
$ nano /etc/sysctl.conf

# 修改
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1

# 存檔案後執行
$ sysctl -p
```

設定防火牆 UFW。

```
$ nano /etc/default/ufw

# 修改
DEFAULT_FORWARD_POLICY="ACCEPT"
```

修改防火牆 UFW 規則。

```
$ nano /etc/ufw/before.rules

# 新增

# NAT table rules
*nat

:POSTROUTING ACCEPT [0:0]
# Allow forward traffic to eth0
-A POSTROUTING -s 192.168.0.1/24 -o eth0 -j MASQUERADE

# Process the NAT table rules
COMMIT
```

最後重新啟動 pptpd 和 ufw 服務。

```
$ service networking restart
$ service ufw restart
$ service pptpd restart
```
