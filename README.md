shadowsocks
===========

[![PyPI version]][PyPI]
[![Build Status]][Travis CI]

A fast tunnel proxy that helps you bypass firewalls.

Features:

- TCP & UDP support
- User management API
- TCP Fast Open
- Workers and graceful restart
- Destination IP blacklist

Server
------

### Install

Debian / Ubuntu:

    apt-get install python-pip
    pip install git+https://github.com/shadowsocks/shadowsocks.git@master

CentOS:

    yum install python-setuptools && easy_install pip
    pip install git+https://github.com/shadowsocks/shadowsocks.git@master

For CentOS 7, if you need AEAD ciphers, you need install libsodium

```
dnf install libsodium python34-pip
pip3 install  git+https://github.com/shadowsocks/shadowsocks.git@master
```

Linux distributions with [snap](http://snapcraft.io/):

    snap install shadowsocks

Windows:

See [Install Shadowsocks Server on Windows](https://github.com/shadowsocks/shadowsocks/wiki/Install-Shadowsocks-Server-on-Windows).

### Usage

    ssserver -p 443 -k password -m aes-256-cfb

To run in the background:

    sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start

To stop:

    sudo ssserver -d stop

To check the log:

    sudo less /var/log/shadowsocks.log

Check all the options via `-h`. You can also use a [Configuration] file
instead.

If you installed the [snap](http://snapcraft.io/) package, you have to prefix the commands with `shadowsocks.`,
like this:

    shadowsocks.ssserver -p 443 -k password -m aes-256-cfb

### Usage with Config File

[Create configuration file and run](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)

To start:

    ssserver -c /etc/shadowsocks.json

Documentation
-------------

You can find all the documentation in the [Wiki](https://github.com/shadowsocks/shadowsocks/wiki).

License
-------

Apache License

[Build Status]:      https://img.shields.io/travis/shadowsocks/shadowsocks/master.svg?style=flat
[PyPI]:              https://pypi.python.org/pypi/shadowsocks
[PyPI version]:      https://img.shields.io/pypi/v/shadowsocks.svg?style=flat
[Travis CI]:         https://travis-ci.org/shadowsocks/shadowsocks

###############################################################################

## 源码编译安装

`git clone https://github.com/BrentHuang/shadowsocks.git`

注意：在 python 高版本（3.12）上有报错，需要改源码，我已改好。

```bash
python3 setup.py build  # 依赖 setuptools，安装：python3 -m pip install setuptools
sudo python3 setup.py install  # sslocal 和 ssserver 可执行程序会被安装到 /usr/local/bin 目录下
```

追求性能的话可以使用 rust 版本：<https://github.com/shadowsocks/shadowsocks-rust>，其中也有 sslocal 和 ssserver 可执行程序，更新也比较活跃。

### 服务器配置、启停

购买一台海外服务器：<https://www.digitalocean.com/>，<https://www.vultr.com/>，<https://www.akamai.com/zh>

新建服务器配置文件：/etc/shadowsocks/shadowsocks.json，参考 config.json.example 文件，内容如下：

```json
{
  "server": "0.0.0.0",  # 你的服务器地址，默认为 0.0.0.0
  "server_port": 8388,  # 你的服务器端口，默认为 8338
  "password": "mypassword",  # 改为你的密码
  "timeout": 300,  # 超时，默认为 300 秒
  "method": "aes-256-gcm"  # 加密方式，默认为 aes-256-cfb，推荐 aes-256-gcm
}
```

启动服务器：

```bash
sudo ssserver -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start  # python 版本
sudo ssserver -c /etc/shadowsocks/shadowsocks.json -d  # rust 版本，没有 --log-file 选项
```

停止：

```bash
sudo ssserver -d stop   # python 版
# rust 版本没有停止命令
```

help 信息：ssserver -h

服务端性能调优：<https://shadowsocks.org/doc/advanced.html>

### Linux 客户端配置、启停

新建 client 配置文件：/etc/shadowsocks/shadowsocks.json，参考 config.json.example 文件，内容如下：

```json
{
  "server": "127.0.0.1",  # 改为你的服务器地址
  "server_port": 8388,    # 改为你的服务器端口
  "password": "mypassword",  # 改为你的密码
  "timeout": 300,  # 超时，默认为 300 秒
  "method": "aes-256-gcm"  # 加密方式，默认为 aes-256-cfb，推荐 aes-256-gcm
  "local_address": "127.0.0.1",  # 本地地址，一般不用变
  "local_port": 1080,  # 本地端口，一般不用变
}
```

启动 client：

```bash
sudo sslocal -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start  # python 版本
sudo sslocal -c /etc/shadowsocks/shadowsocks.json -d  # rust 版本，没有 --log-file 选项
```

停止：

```bash
sudo sslocal -d stop  # python 版本
# rust 版本没有停止命令
```

help 信息：`sslocal -h`

#### 安装 privoxy

privoxy: <https://www.privoxy.org/>

`sudo apt-get install -y privoxy`

编辑配置文件

```bash
sudo cp /etc/privoxy/config /etc/privoxy/config.bak
sudo vim /etc/privoxy/config
```

找到 listen-address 127.0.0.1:8118 行，取消注释

#### GFWList2Privoxy 安装配置

<https://pypi.org/project/gfwlist2privoxy/>

`sudo pip3 install gfwlist2privoxy`

获取在线 gfwlist 文件，并生成 actionfile 文件：

```bash
cd /tmp
wget https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
gfwlist2privoxy -i gfwlist.txt -f gfwlist.action -p 127.0.0.1:1080 -t socks5
sudo cp gfwlist.action /etc/privoxy/
```

在 /etc/privoxy/gfwlist.action 文件中添加需要通过 proxy 访问的域名（常用的已经在里面了，不需要改）,

在 /etc/privoxy/config 文件中加上：actionsfile gfwlist.action。

#### 重启 privoxy

`sudo service privoxy restart`

如果某网址不能访问，可将其加到 /etc/privoxy/gfwlist.action 文件中，并重启 privoxy。

#### 设置系统代理 Network Proxy

将系统代理设置为手动 Manual，http 代理和 https 代理 ip 均为 127.0.0.1，port 均为 8118（上述 privoxy 的监听端口）

浏览器代理设置为：使用系统代理 Use system proxy settings。
