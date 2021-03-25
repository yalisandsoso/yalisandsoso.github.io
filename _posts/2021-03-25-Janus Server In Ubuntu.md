---
bg: "tools.jpg"
layout: post
title:  Janus Server In Ubuntu
crawlertitle: Janus one to many video server
summary: Linux server
date:   2021-03-25
categories: 后端
tags: ['Niu 03']
author: hekun
bg: "african-penguins.jpg"
---

Janus server 用于多人视频通信，本文不解释任何原理性问题，只解释如何在Ubuntu上搭建它，完成多人视频通信。注: Ubuntu16.04, janus 0.10.10

## code

工具

```bash
sudo apt-get install git
sudo apt-get install make
sudo apt-get install nginx
sudo apt-get install python
sudo apt-get install net-tools
sudo apt-get install aptitude

sudo apt-get update && sudo apt-get upgrade
```

依赖库

```bash
sudo aptitude install libmicrohttpd-dev libjansson-dev libnice-dev \libssl1.0.1-dev libsrtp-dev libsofia-sip-ua-dev libglib2.3.4-dev \libopus-dev libogg-dev libcurl4-openssl-dev pkg-config gengetopt \libtool automake

sudo apt install cmake
 sudo aptitude install libconfig-dev
 sudo aptitude install libssl-dev
 sudo aptitude install doxygen graphviz
 
# ffmpeg库 支持--enable-post-processing
sudo aptitude install libavcodec-dev libavformat-dev libswscale-dev libavutil-dev
```

依赖库(源码安装，不可以跳过)

WebSocket

```bash
git clone https://github.com/warmcat/libwebsockets.git
cd libwebsockets
git branch -a 查看选择最新的稳定版本，目前的是remotes/origin/v3.2-stable
git checkout v3.2-stable 切换到最新稳定版本
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_C_FLAGS="-fpic" ..
make && sudo make install
```

libstrp

```bash
wget https://github.com/cisco/libsrtp/archive/v2.2.0.tar.gz
tar xfv v2.2.0.tar.gz
cd libsrtp-2.2.0
./configure --prefix=/usr --enable-openssl
make shared_library && sudo make install
```

libusrsctp

```bash
git clone https://github.com/Kurento/libusrsctp.git
cd libusrsctp
./bootstrap
./configure
make
sudo make install
```

libmicrohttpd

```bash
wget https://ftp.gnu.org/gnu/libmicrohttpd/libmicrohttpd-0.9.71.tar.gz
tar zxf libmicrohttpd-0.9.71.tar.gz
cd libmicrohttpd-0.9.71/
./configure
make
sudo make install
```

编译Janus

```bash
git clone https://github.com/meetecho/janus-gateway.git
git tag 查看当前的 tag，选择最新稳定的版本v0.10.4
git checkout v0.10.4
sh autogen.sh
./configure --prefix=/opt/janus --enable-websockets --enable-post-processing --enable-docs --enable-rest --enable-data-channels
make
sudo make install
```

coturn穿透

```bash
wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
tar zxvf libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable && ./configure
make && make install

git clone https://github.com/coturn/coturn
cd coturn 
./configure 
make 
make install

which turnserver
```

配置和运行coturn(注:服务器开启3478端口)

```bash
#添加

firewall-cmd --zone=public --add-port=3478/udp --permanent
firewall-cmd --zone=public --add-port=3478/tcp --permanent
#重新载入

firewall-cmd --reload
#重启防火墙

systemctl restart firewalld

cd /usr/local/etc/
cp turnserver.conf.default turnserver.conf
ifconfig

yum install openssl 
openssl req -x509 -newkey rsa:2048 -keyout /etc/turn_server_pkey.pem -out /etc/turn_server_cert.pem -days 99999 -nodes 

vi /usr/local/etc/turnserver.conf
:
relay-device=eth0   #与前ifconfig查到的网卡名称一致
listening-ip=x.x.x.x   #内网IP

listening-port=3478
tls-listening-port=5349
relay-ip=x.x.x.x

external-ip=0.0.0.xxx    #公网IP

relay-threads=50
lt-cred-mech
cert=/etc/turn_server_cert.pem
pkey=/etc/turn_server_pkey.pem
pidfile=”/var/run/turnserver.pid”
min-port=49152
max-port=65535
user=yyy:123456    #用户名密码，创建IceServer时用

turnserver -o -a -f -user=yyy:123456 -r Guangdong
```

测试(内网ip:3478)



配置和运行janus(注: 开启端口8089, 8088, 8188, 8989)

```bash
# 进到对应的目录
cd /opt/janus/etc/janus
# 拷贝文件
sudo cp janus.jcfg.sample janus.jcfg
sudo cp janus.transport.http.jcfg.sample janus.transport.http.jcfg
sudo cp janus.transport.websockets.jcfg.sample janus.transport.websockets.jcfg
sudo cp janus.plugin.videoroom.jcfg.sample janus.plugin.videoroom.jcfg
sudo cp janus.transport.pfunix.jcfg.sample janus.transport.pfunix.jcfg
sudo cp janus.plugin.streaming.jcfg.sample janus.plugin.streaming.jcfg
sudo cp janus.plugin.recordplay.jcfg.sample janus.plugin.recordplay.jcfg
sudo cp janus.plugin.voicemail.jcfg.sample janus.plugin.voicemail.jcfg
sudo cp janus.plugin.sip.jcfg.sample janus.plugin.sip.jcfg
sudo cp janus.plugin.nosip.jcfg.sample janus.plugin.nosip.jcfg
sudo cp janus.plugin.textroom.jcfg.sample janus.plugin.textroom.jcfg
sudo cp janus.plugin.echotest.jcfg.sample janus.plugin.echotest.jcfg
```

配置janus.jcfg

```bash
# 大概237行
stun_server = "0.0.0.xxx" (服务器外网ip)
        stun_port = 3478
        nice_debug = false

#大概274行
# credentials to authenticate...
        turn_server = "0.0.0.xxx"
        turn_port = 3478
        turn_type = "udp"
        turn_user = "yyy"
        turn_pwd = "123456"
        
certificates: {
        cert_pem = "/path/ssl/cert.pem"

        cert_key = "/path/ssl/key.pem"

        cert_pwd = "123456"
        #dtls_accept_selfsigned = false
        #dtls_ciphers = "your-desired-openssl-ciphers"
        #rsa_private_key = false
}
```

配置janus.transport.http.jcfg

```bash
general: {
        #events = true                                  # Whether to notify event handlers about transport events (default=true)
        json = "indented"                               # Whether the JSON messages should be indented (default),
                                                                        # plain (no indentation) or compact (no indentation and no spaces)
        base_path = "/janus"                    # Base path to bind to in the web server (plain HTTP only)
        threads = "unlimited"                   # unlimited=thread per connection, number=thread pool
        http = true                                             # Whether to enable the plain HTTP interface
        port = 8088                                             # Web server HTTP port
        #interface = "eth0"                             # Whether we should bind this server to a specific interface only
        #ip = "192.168.0.1"                             # Whether we should bind this server to a specific IP address (v4 or v6) only
        https = true                                    # Whether to enable HTTPS (default=false)
        secure_port = 8089                              # Web server HTTPS port, if enabled
        #secure_interface = "eth0"              # Whether we should bind this server to a specific interface only
        #secure_ip = "192.168.0.1"              # Whether we should bind this server to a specific IP address (v4 or v6) only
        #acl = "127.,192.168.0."                # Only allow requests coming from this comma separated list of addresses
}

certificates: {
        cert_pem = "/path/ssl/cert/cert.pem"
        cert_key = "/path/ssl/cert/key.pem"
        #cert_pwd = "secretpassphrase"
        #ciphers = "PFS:-VERS-TLS1.0:-VERS-TLS1.1:-3DES-CBC:-ARCFOUR-128"
}
```

配置janus.transport.websockets.jcfg

```bash
general: {
        #events = true                                  # Whether to notify event handlers about transport events (default=true)
        json = "indented"                               # Whether the JSON messages should be indented (default),
                                                                        # plain (no indentation) or compact (no indentation and no spaces)
        #pingpong_trigger = 30                  # After how many seconds of idle, a PING should be sent
        #pingpong_timeout = 10                  # After how many seconds of not getting a PONG, a timeout should be detected

        ws = true                                               # Whether to enable the WebSockets API
        ws_port = 8188                                  # WebSockets server port
        #ws_interface = "eth0"                  # Whether we should bind this server to a specific interface only
        #ws_ip = "192.168.0.1"                  # Whether we should bind this server to a specific IP address only
        wss = true                                              # Whether to enable secure WebSockets
        wss_port = 8989                         # WebSockets server secure port, if enabled
        #wss_interface = "eth0"                 # Whether we should bind this server to a specific interface only
        #wss_ip = "192.168.0.1"                 # Whether we should bind this server to a specific IP address only
        #ws_logging = "err,warn"                # libwebsockets debugging level as a comma separated list of things
                                                                        # to debug, supported values: err, warn, notice, info, debug, parser,
                                                                        # header, ext, client, latency, user, count (plus 'none' and 'all')
        #ws_acl = "127.,192.168.0."             # Only allow requests coming from this comma separated list of addresses
}

certificates: {
        cert_pem = "/path/ssl/cert/cert.pem"
        cert_key = "/path/ssl/cert/key.pem"
        #cert_pwd = "secretpassphrase"
}
```

运行测试

```bash
/opt/janus/bin/janus --debug-level=7
```

## support

Janus: [https://github.com/meetecho/janus-gateway](https://github.com/meetecho/janus-gateway)

coturn: [https://www.jianshu.com/p/915eab39476d](https://www.jianshu.com/p/915eab39476d)


