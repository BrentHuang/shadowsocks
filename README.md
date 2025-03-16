# README

## 理论介绍

1. Shadowsocks 的作用
  
    Shadowsocks 是一个基于 SOCKS5 协议的代理工具，主要用于加密和转发网络流量，帮助用户绕过网络封锁（如 GFW）。它的核心功能包括：

    - 加密传输：将用户的网络流量加密，防止被中间人监听或干扰。
    - 代理转发：将加密后的流量通过远程服务器转发，从而绕过网络封锁。
    - 高效性：Shadowsocks 设计轻量，适合低延迟和高吞吐量的场景。

    局限性：

    - Shadowsocks 本身只支持 SOCKS5 协议，无法直接处理 HTTP/HTTPS 流量。
    - 某些应用程序（如浏览器）可能不支持 SOCKS5 代理，需要额外的工具来适配。

2. Privoxy 的作用

    Privoxy 是一个基于 HTTP/HTTPS 的代理工具，主要用于过滤和转发 HTTP 流量。它的核心功能包括：

    - HTTP 代理：将 HTTP/HTTPS 流量转发到指定的代理服务器（如 Shadowsocks）。
    - 流量过滤：支持广告拦截、隐私保护等功能。
    - 协议转换：将 HTTP 流量转换为 SOCKS5 协议，从而与 Shadowsocks 配合使用。

    局限性：

    - Privoxy 本身不支持加密，需要与其他工具（如 Shadowsocks）结合使用以实现安全传输。

3. Shadowsocks 和 Privoxy 的组合可以实现什么功能？

    协议适配：

    - Privoxy 作为 HTTP/HTTPS 代理，接收来自浏览器或其他应用程序的 HTTP 流量。
    - Privoxy 将这些流量转换为 SOCKS5 协议，并转发给 Shadowsocks 客户端。

    智能分流：

    - Privoxy 可以根据规则（如 GFWList）判断哪些流量需要通过 Shadowsocks 代理，哪些可以直接连接。
    - 只有被封锁的流量通过 Shadowsocks 转发，其他流量直接访问，从而提高效率。

    增强兼容性：

    - 某些应用程序（如浏览器）可能不支持 SOCKS5 代理，但支持 HTTP 代理。通过 Privoxy，这些应用程序可以间接使用 Shadowsocks。

4. Shadowsocks 和 Privoxy 组合使用的典型工作流程

    用户发起请求：用户在浏览器或应用程序中配置 HTTP 代理，指向 Privoxy（例如 127.0.0.1:8118）。

    Privoxy 处理请求：Privoxy 接收到 HTTP 请求后，根据规则判断是否需要通过代理。如果需要代理，Privoxy 将请求转换为 SOCKS5 协议，并转发给 Shadowsocks 客户端（例如 127.0.0.1:1080）。

    Shadowsocks 加密并转发：Shadowsocks 客户端将流量加密，并通过远程 Shadowsocks 服务器转发。

    远程服务器解密流量并访问目标网站。

    返回响应：

    - 远程服务器将响应数据返回给 Shadowsocks 客户端。
    - Shadowsocks 客户端解密数据并返回给 Privoxy。
    - Privoxy 将响应数据返回给用户的浏览器或应用程序。

5. 优势

    兼容性强：通过 Privoxy，支持 HTTP 代理的应用程序都可以间接使用 Shadowsocks。

    智能分流：只有被封锁的流量通过 Shadowsocks，其他流量直接连接，提高效率。

    隐私保护：Shadowsocks 提供加密，Privoxy 提供广告过滤和隐私保护，双重保障。

6. 配置示例

    Shadowsocks 配置：启动 Shadowsocks 客户端，监听本地 SOCKS5 端口（如 127.0.0.1:1080）。

    Privoxy 配置：编辑 Privoxy 配置文件（如 /etc/privoxy/config），添加以下内容：
forward-socks5 / 127.0.0.1:1080 .

    启动 Privoxy，监听本地 HTTP 端口（如 127.0.0.1:8118）。

    浏览器配置：在浏览器中设置 HTTP 代理为 127.0.0.1:8118。

## 源码编译安装（不推荐 python 版本，推荐 rust 版本）

`git clone https://github.com/BrentHuang/shadowsocks.git`

注意：在 python 高版本（3.12）上有报错，需要改源码，我已改好。

```bash
python3 setup.py build  # 依赖 setuptools，安装：python3 -m pip install setuptools
sudo python3 setup.py install  # sslocal 和 ssserver 可执行程序会被安装到 /usr/local/bin 目录下
```

追求性能的话可以使用 rust 版本：<https://github.com/shadowsocks/shadowsocks-rust>，其中也有 sslocal 和 ssserver 可执行程序，更新也比较活跃。

## 服务器配置、启停

购买一台海外服务器：<https://www.digitalocean.com/>，<https://www.vultr.com/>，<https://www.akamai.com/zh>

新建服务器配置文件：/etc/shadowsocks/shadowsocks.json，参考 config.json.example 文件，内容如下：

```json
{
  "server": "0.0.0.0",  # 你的服务器地址，默认为 0.0.0.0
  "server_port": 8388,  # 你的服务器端口，默认为 8388
  "password": "mypassword",  # 改为你的密码
  "timeout": 300,  # 超时，默认为 300 秒
  "method": "aes-256-gcm"  # 加密方式，默认为 aes-256-cfb，推荐 aes-256-gcm
}
```

启动服务器：

```bash
ssserver -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start  # python 版本
ssserver -c /etc/shadowsocks/shadowsocks.json -d  # rust 版本，没有 --log-file 选项
```

停止：

```bash
ssserver -d stop   # python 版
# rust 版本没有停止命令
```

help 信息：ssserver -h

服务端性能调优：<https://shadowsocks.org/doc/advanced.html>

## Linux 客户端配置、启停

新建 client 配置文件：/etc/shadowsocks/shadowsocks.json，参考 config.json.example 文件，内容如下：

```json
{
  "server": "127.0.0.1",  # 改为你的服务器地址
  "server_port": 8388,    # 改为你的服务器端口
  "password": "mypassword",  # 改为你的密码
  "timeout": 300,  # 超时，默认为 300 秒
  "method": "aes-256-gcm",  # 加密方式，默认为 aes-256-cfb，推荐 aes-256-gcm
  "local_address": "127.0.0.1",  # 本地地址，一般不用变
  "local_port": 1080  # 本地 socks5 端口，一般不用变
}
```

启动 client：

```bash
sslocal -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start  # python 版本
sslocal -c /etc/shadowsocks/shadowsocks.json -d  # rust 版本，没有 --log-file 选项
```

停止：

```bash
sslocal -d stop  # python 版本
# rust 版本没有停止命令
```

help 信息：`sslocal -h`

### 安装 privoxy

privoxy: <https://www.privoxy.org/>

`sudo apt install -y privoxy`

编辑配置文件

```bash
sudo cp /etc/privoxy/config /etc/privoxy/config-bak
sudo vim /etc/privoxy/config
```

找到 listen-address 127.0.0.1:8118 行（有两行），取消注释。

### GFWList2Privoxy 安装配置

GFWList2Privoxy 的主要作用是将 GFWList（Great Firewall List，即中国防火长城屏蔽的网站列表）转换为 Privoxy 的配置文件格式，从而帮助用户通过 Privoxy 实现网络流量的过滤和代理。

<https://pypi.org/project/gfwlist2privoxy/>

```bash
sudo apt-get install python3-venv
python3 -m venv ~/.venv
source ~/.venv/bin/activate
pip3 install gfwlist2privoxy
```

获取在线 gfwlist 文件，并生成 actionfile 文件：

```bash
cd /tmp
wget https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
gfwlist2privoxy -i gfwlist.txt -f gfwlist.action -p 127.0.0.1:1080 -t socks5
sudo cp gfwlist.action /etc/privoxy/
```

在 /etc/privoxy/gfwlist.action 文件中添加需要通过 proxy 访问的域名（常用的已经在里面了，不需要改）,

在 /etc/privoxy/config 文件中加上：actionsfile gfwlist.action。

### 重启 privoxy

`sudo service privoxy restart`

如果某网址不能访问，可将其加到 /etc/privoxy/gfwlist.action 文件中，并重启 privoxy。

### 设置系统代理 Network Proxy

将系统代理设置为手动 Manual，http 代理和 https 代理 ip 均为 127.0.0.1，port 均为 8118（上述 privoxy 的监听端口）

浏览器代理设置为：使用系统代理 Use system proxy settings。

### ssh 方式拉 github 代码

在 Linux 系统上，通过以上设置后，在命令行通过 https 是可以拉取 github 代码的，但是不能通过 ssh 拉取代码，需要：

1. 安装 netcat（`sudo apt-get install netcat-openbsd`）
2. 在 ~/.ssh/config 文件中增加如下内容：

    ```text
    Host github.com
    ProxyCommand nc -x 127.0.0.1:1080 %h %p
    ```

说明：SSH 是 TCP 协议，可以直接使用 SOCKS5 代理（Shadowsocks 的 1080 端口）。nc 默认也使用 socks5 协议，如果不通，就改用 http 协议：`-X connect`。`-X` 选项的说明如下：

```text
-X proxy_protocol
    Use proxy_protocol when talking to the proxy server.  Supported protocols are 4 (SOCKS v.4), 5 (SOCKS v.5) and connect (HTTPS proxy).  If the protocol is not specified, SOCKS version 5 is used.
```

SSH 不支持 HTTP 代理（Privoxy 的 8118 端口），因此不能直接使用 Privoxy，需要加上 `-X connect` 才能走 http 协议。
