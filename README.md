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

`git clone https://ghproxy.com/git@github.com:BrentHuang/shadowsocks.git`

注意：在 python 高版本上有报错，需要改源码，我已改好。

```bash
python3 setup.py build  # 依赖 setuptools，安装：python3 -m pip install setuptools

sudo python3 setup.py install
```

client 配置文件：/etc/shadowsocks/shadowsocks.json，配置参考 config.json.example 文件，如下：

{
  "server": "127.0.0.1",  # 改为你的服务器地址
  "server_port": 8388,    # 改为你的服务器端口
  "local_address": "127.0.0.1",
  "local_port": 1080,     # 本地端口，一般不用变
  "password": "mypassword",  # 改为你的密码
  "timeout": 600,
  "method": "aes-256-cfb"  # 加密方式，一般不用变
}

启动 client：sudo sslocal -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start

停止：sudo sslocal -d stop

help 信息：sslocal -h

## 安装 privoxy（<https://www.privoxy.org/>）

sudo apt-get install -y privoxy

编辑配置文件

sudo cp /etc/privoxy/config /etc/privoxy/config.bak
sudo vim /etc/privoxy/config

找到 listen-address 127.0.0.1:8118 行，取消注释

## GFWList2Privoxy 安装配置

sudo pip3 install gfwlist2privoxy

获取在线 gfwlist 文件，并生成 actionfile 文件

```
cd /tmp
wget https://ghproxy.com/https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
gfwlist2privoxy -i gfwlist.txt -f gfwlist.action -p 127.0.0.1:1080 -t socks5
sudo cp gfwlist.action /etc/privoxy/
```

在 /etc/privoxy/gfwlist.action 文件中添加需要通过 proxy 访问的域名（常用的已经在里面了，不需要改）,

在 /etc/privoxy/config 文件中加上：actionsfile gfwlist.action。

## 重启 privoxy

sudo service privoxy restart

## 设置系统代理 Network Proxy

将系统代理设置为手动 Manual，http 代理和 https 代理 ip 均为 127.0.0.1，port 均为 8118

浏览器代理设置为：使用系统代理 Use system proxy settings。
