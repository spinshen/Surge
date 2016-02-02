你可以下载官方的原始的配置文件: http://surge.run/config-example/ios.conf， 自己修改，然后通过 iTunes 或 URL 添加到你的 iOS 设备里。


下面是 Surge 配置文件的简明手册

```
### 通用配置
[General]
# Surge 输出的日志信息级别 verbose（详细）／ info（信息）/ notify（通知，默认）／ warning（警告）
loglevel = warning

# skip-proxy 用于指定哪些服务器的域名和IP不被 Proxy server 代理，解决一些应用的兼容性问题
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local

#  bypass-tun 用于指定哪些服务器的域名/IP不被 TUN interface 转发，配置的方式同 skip-proxy
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12

# 如果您网络情况不理想，可以自定义 DNS 解析，屏蔽运营商的DNS
dns-server = 119.29.29.29,223.5.5.5,114.114.114.114,8.8.8.8


### 代理配置
[Proxy]
#代理的类型：http / https / socks5 /（ custom ），服务器的地址／IP ，端口，用户名或加密方式，密码
Proxy = https, 1.2.3.4, 443, rc4-md5, password

### 过滤规则
# 注意：规则按序存储，匹配时从上到下，先匹配先生效，因此对于常用的规则应该放在前头，以提高性能
[Rule]
// 设置 DIRECT（直连）的示例
# 基于域名判断请求走直连（DIRECT）
DOMAIN,www.jd.com,DIRECT
# 基于域名后缀判断请求走直连（DIRECT）
DOMAIN-SUFFIX,taobao.com,DIRECT
# 基于关键词后缀判断请求走直连（DIRECT）
DOMAIN-KEYWORD,Alipay,DIRECT

// 设置 Proxy（代理）的示例
# 基于域名判断请求走代理（Proxy）
DOMAIN,www.google.com,Proxy
# 基于域名后缀判断请求走代理（Proxy）
DOMAIN-SUFFIX,youtube.com,Proxy
# 基于关键词后缀判断请求走代理（Proxy）
DOMAIN-KEYWORD,gmail,Proxy

// 设置 REJECT（屏蔽）的示例，主要用于屏蔽广告
# 基于域名判断并屏蔽（REJECT）请求
DOMAIN,ads.mopub.com,REJECT
# 基于域名后缀判断并屏蔽（REJECT）请求
DOMAIN-SUFFIX,adchina.com,REJECT
# 基于关键词后缀判断并屏蔽（REJECT）请求
DOMAIN-KEYWORD,baidu,REJECT

// 基于 TCP 协议的应用程序在 TUN interface 模式中需要请求服务时，首先会发送域名解析请求，之后再发送 TCP 数据，如果域名无法在本地被解析，可以通过 force-remote-dns 配置域名在远程代理上进行解析
# 基于域名判断走代理（Proxy），强制使用远程的 DNS 解析
DOMAIN,www.twitter.com,Proxy,force-remote-dns
# 基于域名后缀判断走代理（Proxy），强制使用远程的 DNS 解析
DOMAIN-SUFFIX,t.co,Proxy,force-remote-dns
# 基于关键词后缀判断走代理（Proxy），强制使用远程的 DNS 解析
DOMAIN-KEYWORD,facebook,Proxy,force-remote-dns

// 当有 Rule 中有 GEOIP 和 IP-CIDR 配置时，Surge 将对所有基于域名的请求流量进行域名解析代理，通过可选配置no-resolve 可以指定特定流量不运行域名解析代理
# Telegram.app 指定“no-resolve”Surge 忽略这个规则与域的请求。 
IP-CIDR,91.108.56.0/22,Proxy,no-resolve 

// 判断是否是局域网
# 判断是否是局域网，如果是，走直连
IP-CIDR,192.168.0.0/16,DIRECT
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,127.0.0.0/8,DIRECT


// 判断服务器所在地
# 判断服务器所在地，如果是国内，走直连
GEOIP,CN,DIRECT

# 最后，其他的走代理
FINAL,Proxy

```