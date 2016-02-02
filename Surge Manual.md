Surge is a web developer tool and proxy utility for iOS 9. This app is designed for developers and may require some level of professional knowledge to use.




---
##Features

- Capture all HTTP/HTTPS/TCP traffic from any applications on your device, and redirect the traffic to an HTTP/HTTPS/SOCKS5 proxy server following highly configurable rules;
- Override system DNS settings even on cellular network. And boost performance by sending DNS query to all DNS servers simultaneously.
- Record and display HTTP request and response header sent from your iOS devices;
- Configure rules using domain match, domain suffix, domain keyword, CIDR IP range, and/or GeoIP lookup;
- Measure traffic usage and network speed on WiFi, cellular, and proxy connections.
- Import rules from URL or iTunes File Sharing;
- High performance and suitable for intensive use;
- Block ads by domain rules;
- Work on cellular network.



---
##Known Issues

- It is unable to view request and response body. This feature will be added in a future update soon.
- The HTTP/HTTPS proxy server must support CONNECT method, even when accessing plain HTTP resources.
- HTTP pipelining is not supported.
- IPv6 is not supported.
- SOCKS5 authentication is not supported.
- There might be a little inaccuracy in traffic usage statistics while using an HTTPS proxy.
- HTTPS and raw TCP traffic cannot be inspected.
- Only TCP based protocol is supported.


---

##Architecture

There are two main components in Surge: **Surge proxy server and Surge TUN interface**.

After started, Surge sets itself as the default HTTP/HTTPS proxy server to handle all HTTP/HTTPS traffic, which allows Surge to boost performance by using HTTP connections' keep-alive mechanism globally. But some apps do not obey system proxy setting (such as Mail.app), because they need to use a raw TCP socket. This kind of traffic is handled by Surge TUN interface.

![](https://surge.run/manual/Surge-Architecture.png)

Currently, Surge TUN interface can only process TCP protocol. Other protocol packet (such as UDP, ICMP) will be dropped. You can use 'bypass-tun' option as a workaround.

---

##Configuration

###General Section

```
[General]
loglevel = verbose
bypass-system = true
skip-proxy = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12, localhost, *.local
bypass-tun = 192.168.0.0/16, 10.0.0.0/8, 172.16.0.0/12
dns-server = 8.8.8.8, 8.8.4.4
```

###loglevel (Default: notify)

One of verbose, info, notify or warning. It's not recommended to enable verbose in daily use because this will slow down performance significantly.

###bypass-system

This option will add some special rules. First, these rules below are added to allow domains to bypass Surge proxy server and use raw TCP:

```
api.smoot.apple.com
configuration.apple.com
xp.apple.com
smp-device-content.apple.com
guzzoni.apple.com
captive.apple.com
*.ess.apple.com
*.push.apple.com
*.push-apple.com.akadns.net
```

Second, this rule is added with the highest priority:

```
IP-CIDR, 17.0.0.0/8, DIRECT, no-resolve
```

If you disable this option, it may lead to some system problems, such as delays in push notifications.

>
Notice: Entire 17.0.0.0/8 address block is assigned to Apple.


###skip-proxy

This option forces connections to these domain/IP ranges to be handled by Surge TUN interface, instead of Surge proxy server. This option is used to fix some compatibility problems with some apps.

- To specify a single domain, enter the domain name - for example, apple.com.
- To specify all websites on a domain, use an asterisk before the domain name - for example, *apple.com.
- To specify a specific part of a domain, specify each part - for example, store.apple.com.
- To specify hosts or networks by IP addresses, enter a specific IP address such as 192.168.2.11 or an address range, such as 192.168.2.* or 192.168.2.0/24.

>
Notice: If you enter an IP address or address range, you are only able to bypass the proxy when you connect to that host using that address, not when you connect to the host by a domain name that resolves to that address.

    
###bypass-tun

Surge TUN interface can only process TCP protocol. Use this option to bypass specify IP ranges to allow other protocols to pass through.

>
Notice: This option only works for Surge TUN interface. Request handled by Surge Proxy Server will not be affected. Combine 'skip-proxy' and 'bypass-tun' to make sure that certain HTTP traffic bypasses Surge.


###dns-server

You can override the default DNS server with this option. iOS system does not allow you to modify DNS settings on cellular network. With Surge now you can achieve this easily.

To boost performance Surge will send DNS queries to all DNS servers defined in the config simultaneously. The query will be retried every 2 seconds if no response is received, up to 5 times.

###Proxy Section

Surge supports HTTP, HTTPS, and SOCKS5 proxy server. Example:

```
[Proxy]
ProxyA = socks5, example.server.com, 3129
ProxyB = http, example.server.com, 3128
ProxyC = https, example.server.com, 443, username, password
```
You can create multiple proxies for different rules.

- Currently only HTTP/HTTPS proxy supports authentication. The username and password are optional.

- You can force Surge to use a specific HTTPS cipher for HTTPS proxy. Ciphers in GCM mode are recommended to reduce memory usage and improve performance. See config interface for available values.

```
ProxyC = https, example.server.com, 443, username, password, cipher=TLS_RSA_WITH_AES_128_GCM_SHA256

```

- TLS session resumption: TLS False Start and Explicit Congestion Notification are enabled by default for HTTPS proxy, to improve performance. There will be options to turn them off in the future.

###Rule Section

Rules are matched from the first one to the last one, in the order they appear in the config file. In other words, rules in front have higher priority than rules in the back.

Example:

```
[Rule]
DOMAIN-SUFFIX,company.com,ProxyA
DOMAIN-KEYWORD,google,DIRECT
GEOIP,US,DIRECT
IP-CIDR,192.168.0.0/16,DIRECT
FINAL ProxyB
```

Surge supports 6 different types of rule. Each rule consists of 3 parts: rule type, a traffic matcher (except for FINAL rule), and a proxy policy. Rule type is one of DOMAIN, DOMAIN-SUFFIX, DOMAIN-KEYWORD, GEOIP, IP-CIDR, or FINAL. Proxy policy must be one of a proxy name, DIRECT, or REJECT.

- DOMAIN

```
DOMAIN,www.apple.com,Proxy
```

Rule matches if the domain of request matches exactly.

- DOMAIN-SUFFIX

```
DOMAIN-SUFFIX,apple.com,Proxy
```

Rule matches if the domain of the request matches the suffix. For example: 'google.com' matches 'www.google.com', 'mail.google.com' and 'google.com', but does not match 'content-google.com'.

- DOMAIN-KEYWORD

```
DOMAIN-KEYWORD,google,Proxy
```

Rule matches if the domain of the request contains the keyword.

- IP-CIDR

```
IP-CIDR,192.168.0.0/16,DIRECT
IP-CIDR,10.0.0.0/8,DIRECT
IP-CIDR,172.16.0.0/12,DIRECT
IP-CIDR,127.0.0.1/8,DIRECT
```

Rule matches if the IP address of the request matches the specified range.

- GEOIP

```
GEOIP,US,DIRECT
```
Rule matches if the GeoIP test result matches the specified country code.

- FINAL

```
FINAL,DIRECT
```
Rule section must end with a FINAL statement, to specify the default policy.

###Option: no-resolve

```
GEOIP,US,DIRECT,no-resolve
IP-CIDR,172.16.0.0/12,DIRECT,no-resolve
```

When a GEOIP or IP-CIDR rule is encountered, Surge will send a DNS query if the hostname of request is a domain. You can specify 'no-resolve' option to skip this rule for a request with domain.

###Option: force-remote-dns

```
DOMAIN,www.apple.com,Proxy,force-remote-dns
DOMAIN-SUFFIX,apple.com,Proxy,force-remote-dns
DOMAIN-KEYWORD,google,Proxy,force-remote-dns
```

When a raw TCP connection is handled by Surge TUN interface, the application will try to resolve the domain at first, then send packets to the IP address directly. If the domain can not be resolved locally,you can use this option to force the DNS resolution to happen remotely on the proxy.

Technically, when an application tries to resolve a domain which matches a rule with 'force-remote-dns' option. Surge will send a DNS answer with a fake IP address (240.1.x.x). When the application connects to the fake IP, Surge will remap it to a domain and send the request to the remote proxy.

>
Notice: This option only works for Surge TUN interface. Request handled by Surge Proxy Server will always be resolved in remote if the policy of rule is a proxy.


##Protected Configuration

If a configuration file starts with #!REQUIRE-PROTECTED, Surge will mark this configuration file as protected, which prevents users from viewing or editing the configuration file. This is useful when you share your own proxy server with others.

##URL Scheme
Surge supports 3 actions and one option.

Action:

- surge:///start

    Start with selected configuration

- surge:///stop

    Stop current session

- surge:///toggle

    Start or stop with selected configuration

Option:

- autoclose=true

    Auto close Surge after action completed.

    Example: surge:///toggle?autoclose=true
    
    
----
原文网址：<https://surge.run/manual>