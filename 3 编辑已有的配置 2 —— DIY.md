#编辑已有的配置 2 —— DIY

作为 Power User ，默认的太简单，别人的又不符合自己的需求，那就自己动手重新发明轮子好啦！

通过 Surge 配置文件的简明手册，我们知道每一个配置文件（.conf，纯文本）主要就是包括三个部分`[General]`、`[Proxy]`和`[Rule]`。



[General]部分

```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
dns-server = 119.29.29.29, 223.5.5.5, 114.114.114.114
loglevel = notify
```

[Proxy]部分

```
[Proxy]
Proxy = custom,You Server,You Port,You Username,You Password,http://surge.pm/ss.module
```

[Rule]部分


```
[Rule]

// 需要的调试规则放在这里



// LAN
// 局域网 调试规则应放置在这行上面
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,127.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,192.168.0.0/16,DIRECT

GEOIP,CN,DIRECT
FINAL,DIRECT
```

---

规则配置说明，根据你的需求自由选择。

- [General]为通用部分，或者说是共有的部分。一般不需要更改
- [Proxy]为代理服务器的信息。如实填写即可，没有科学上网需求的不用写
- [Rule]为过滤规则部分。基本的规则不多，但可组合使用，功能非常强大。我们的 DIY 工作多数都是在这里进行的


示例 1：网络提速

```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
dns-server = 119.29.29.29, 223.5.5.5, 114.114.114.114
loglevel = notify

[Rule]
FINAL,DIRECT

```


示例 2：屏蔽广告

```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
dns-server = 119.29.29.29, 223.5.5.5, 114.114.114.114
loglevel = notify

[Rule]
// AD Block
DOMAIN-KEYWORD,analytics,REJECT
DOMAIN-KEYWORD,cnzz,REJECT
DOMAIN-KEYWORD,openx,REJECT
DOMAIN-KEYWORD,trace,REJECT
DOMAIN-KEYWORD,track,REJECT
DOMAIN-KEYWORD,traffic,REJECT
DOMAIN-KEYWORD,usage,REJECT

// LAN
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,127.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,192.168.0.0/16,DIRECT

FINAL,DIRECT
```




示例 3：全局代理

```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
loglevel = notify

[Proxy]
Proxy = custom,1.2.3.4,443,user,mima,http://surge.pm/ss.module

[Rule]
// LAN
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,127.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,192.168.0.0/16,DIRECT

FINAL,Proxy
```

示例 4：科学上网

```
[General]
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
dns-server = 119.29.29.29, 223.5.5.5, 114.114.114.114
loglevel = notify

[Proxy]
Proxy = custom,1.2.3.4,443,user,mima,http://surge.pm/ss.module

[Rule]
// iOS
DOMAIN-SUFFIX,lcdn-registration.apple.com,DIRECT
DOMAIN-SUFFIX,ls.apple.com,DIRECT

// Top cn sites
DOMAIN-KEYWORD,360buy,DIRECT
DOMAIN-KEYWORD,alipay,DIRECT
DOMAIN-KEYWORD,baidu,DIRECT
DOMAIN-KEYWORD,taobao,DIRECT

// Top blocked sites with tcp
DOMAIN-KEYWORD,facebook,Proxy,force-remote-dns
DOMAIN-KEYWORD,gmail,Proxy,force-remote-dns
DOMAIN-KEYWORD,google,Proxy,force-remote-dns
DOMAIN-KEYWORD,instagram,Proxy,force-remote-dns
DOMAIN-KEYWORD,twitter,Proxy,force-remote-dns
DOMAIN-KEYWORD,youtube,Proxy,force-remote-dns

DOMAIN-SUFFIX,t.co,Proxy,force-remote-dns
DOMAIN-SUFFIX,twimg.com,Proxy,force-remote-dns

// Telegram（一些应用程序的解决办法）
IP-CIDR,91.108.4.0/22,Proxy,no-resolve
IP-CIDR,91.108.56.0/22,Proxy,no-resolve
IP-CIDR,109.239.140.0/24,Proxy,no-resolve
IP-CIDR,149.154.160.0/20,Proxy,no-resolve

// LAN
IP-CIDR,192.168.0.0/16,DIRECT
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,127.0.0.0/8,DIRECT

GEOIP,CN,DIRECT
FINAL,Proxy

```

---






