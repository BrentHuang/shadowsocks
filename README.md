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

git clone https://ghproxy.com/git@github.com:BrentHuang/shadowsocks.git

注意：在python高版本上有报错，需要改源码。

python3 setup.py build

sudo python3 setup.py install

client配置：{ 省略 }

启动client：sudo sslocal -c /etc/shadowsocks/shadowsocks.json --log-file=/var/log/shadowsocks.log -d start

## 安装privoxy

sudo apt-get install -y privoxy

编辑配置文件

sudo cp /etc/privoxy/config /etc/privoxy/config.bak
sudo vim /etc/privoxy/config

找到listen-address 127.0.0.1:8118行并取消注释

## 配置环境变量使终端也能通过proxy访问

/etc/profile, ~/.bashrc, ~/.bash_profile, 在任意一个文件末尾添加下面行：

```
proxy="http://127.0.0.1:8118"
export https_proxy=$proxy
export http_proxy=$proxy
export ftp_proxy=$proxy
```

## GFWList2Privoxy安装配置

sudo pip3 install gfwlist2privoxy

获取在线gfwlist文件, 并生成actionfile文件

```
cd /tmp
wget https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
gfwlist2privoxy -i gfwlist.txt -f gfwlist.action -p 127.0.0.1:1080 -t socks5
sudo cp gfwlist.action /etc/privoxy/
```

在生成的gfwlist.action文件中添加需要通过proxy访问的域名（常用到已经在里面了，不需要改）, 同时在/etc/privoxy/config文件中加上actionsfile gfwlist.action即可使终端也通过pac访问。

## 重启

sudo service privoxy restart

## 浏览器代理

在浏览器代理中配置http和https代理ip：127.0.0.1，端口：8118
