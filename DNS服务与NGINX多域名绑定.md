# DNS服务器
## 设备
orangepi4lts 4g+64g
## 操作系统
Ubuntu22.04
## DNS服务器
dnsmasq
## 防火墙管理器
UFW

## 操作流程
1. 安装dnsmasq
   ```shell
   apt install dnsmasq
   ```
2. 配置文件
   新建文件dns-record.conf和dns-upstream.conf分别用于存储自定义dns解析记录与上游dns配置。
	```shell
	# /etc/dnsmasq.d/dns-record.conf
	address=/domain/ipaddress
	# /etc/dnsmasq.d/dns-upstream.conf
	server=114.114.114.114
	
	```
	
3. 防火墙放行
   ```bash
   ufw allow 53/udp
   ufw allow 53/tcp
	```
4. 路由器配置
   虽然直接在设备上指定DNS服务器有着同样效果，但是在DHCP服务器上，针对DHCP请求直接配置下发DNS记录，不需要每台设备手动配置。这种适合小规模局域网，如果是大型网络有着过多设备发起DNS请求，考虑DNS设备性能是否足够。


# Nginx 配置

对于nginx配置如下

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```



对于这种情况，就是当命中了这个域名后，就会代理这个流量到本机的8080端口，同理，可以配置不同的域名，当通过不同的域名走ng时候，就能实现一个设备可以通过多个不同的域名访问不同端口运行的服务。



如果说需要一个禁止ip进行访问的话，可以添加以下的配置

```nginx
server {
    listen 80 default_server;
    listen 443 default_server ssl;

    server_name _;

    return 444;
}

```

