##默认配置
软件自带的配置“Default”，主要用于捕获监听 iOS 设备上所有的 HTTP / HTTPS / TCP 请求流量，观察各种 APP 的联网情况、监视 HTTP / TCP 请求、网络调试、流量跟踪、流量统计、屏蔽指定网址等。


```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
loglevel = notify

[Rule]
FINAL,DIRECT
```
