权威域解析器
递归解析器

## 注意事项：
一下两条命令为什么返回数据不同？
```bash
dig ns www.apple.com

; <<>> DiG 9.18.41 <<>> ns www.apple.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61706
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;www.apple.com.                 IN      NS

;; ANSWER SECTION:
www.apple.com.          13      IN      CNAME   www-apple-com.v.aaplimg.com.

;; AUTHORITY SECTION:
v.aaplimg.com.          92      IN      SOA     a.gslb.aaplimg.com. hostmaster.apple.com. 1715289020 1800 300 60480 300

;; Query time: 49 msec
;; SERVER: 10.255.255.254#53(10.255.255.254) (UDP)
;; WHEN: Mon Nov 17 14:46:48 CST 2025
;; MSG SIZE  rcvd: 157

```


```bash
dig ns apple.com

; <<>> DiG 9.18.41 <<>> ns apple.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18771
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;apple.com.                     IN      NS

;; ANSWER SECTION:
apple.com.              3358    IN      NS      c.ns.apple.com.
apple.com.              3358    IN      NS      d.ns.apple.com.
apple.com.              3358    IN      NS      b.ns.apple.com.
apple.com.              3358    IN      NS      a.ns.apple.com.

;; Query time: 23 msec
;; SERVER: 10.255.255.254#53(10.255.255.254) (UDP)
;; WHEN: Mon Nov 17 14:46:31 CST 2025
;; MSG SIZE  rcvd: 150
```
因为dns的ns记录针对域，而不是域名，apple.com本身是域名也是域，但是www.apple.com是域名，他没有ns记录，委派父级返回。

## DNS的TLD
tld本身是具有多种，除了常见的com,cn还有如com.cn之类的复合域名，对于rdap-cli的实现，他的实现就是直接从顶级分割lable，一级一级减少进行查询





# CH 查询

dns查询拥有IN（Internet）类型，也是最常见的类型。然后就是CH类型，也就是Chaonet协议设计的一种类型，但是现在以及不再使用。主要用于查询DNS服务器元信息。默认是拒绝CH类型查询





# HAPROXY Protocol

haproxy protocol 是由haproxy开发的一个传递原始客户端信息的一种协议。在现在分布式的环境下，对于http的请求，会有一些比如x-forwarded-for或自定义头部去携带一些关于客户端原ip的信息。但是比如在四层下面，这个事情就不好做了，当经过了网关代理后，很难获取到真正的客户端的信息，haproxy protocol的解决方案是，在包头部添加一个头部，携带了关于网关获取到的真正的客户端原ip。

`Haproxy Protocol V1(text format)`

```plaintext
+-------------------------------------------------------------------------------+
|                                TCP Payload (L7)                               |
+-----------+-------+---------+---------+---------+---------+---------+---------+
| Signature | Space | Proto   | Src Addr| Dst Addr| Src Port| Dst Port| CR LF   |
| "PROXY"   | " "   | "TCP4"  | string  | string  | string  | string  | "\r\n"  |
+-----------+-------+---------+---------+---------+---------+---------+---------+
| 5 bytes   | 1 byte| 4-5 B   |  var    |  var    |  var    |  var    | 2 bytes |
+-------------------------------------------------------------------------------+
| <---                    This is the Proxy Header                        ---> |
+-------------------------------------------------------------------------------+
| [Original Application Data (e.g., HTTP Request / DNS Query)]                  |
+-------------------------------------------------------------------------------+
```

`Haproxy Protocol V2(binary format)`

```

```

