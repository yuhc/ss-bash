ss-bash
=======

Shadowsocks流量管理脚本

* 目前只支持python版Shadowsocks
* 目前只支持统计ipv4流量

# 系统要求
* shadowsocks-python
* Linux（推荐Debian 7，其它系统未测试）

# 工作原理
不同的用户分配不同端口，使用iptables规则获取各端口的流量，脚本循环运行，在固定时间间隔根据iptables结果统计流量使用情况，并在流量超过限制时，添加对应端口的iptables reject规则以禁用端口。

# 使用说明

## 首次使用
### 下载软件
    git clone https://github.com/hellofwy/ss-bash

或者：

    wget https://github.com/hellofwy/ss-bash/archive/v1.0-beta.3.tar.gz

### 首次运行时，先新建用户
例如新用户端口为8388，密码为passwd，流量限制为10GB，执行：

    sudo ss-bash/ssadmin.sh add 8388 passwd 10G

### 启动ssserver
    sudo ss-bash/ssadmin.sh start

### 其它命令请查看帮助，执行命令：
    sudo ss-bash/ssadmin.sh help

或者点击链接：https://github.com/hellofwy/ss-bash/blob/master/sshelp

## 自定义ssserver的配置
打开文件ssmlt.template，添加相关选项。

请注意每个选项后必需有逗号（','）

默认选项为：

    "server": "0.0.0.0",
    "timeout": 60,
    "method": "aes-256-cfb",

比如添加fastopen和worker选项后：

    "server": "0.0.0.0",
    "timeout": 60,
    "method": "aes-256-cfb",
    "fast_open": true,
    "workers": 5,

修改之后，如果ssserver正在运行，请执行下面命令，重新加载文件并启动：

    sudo ss-bash/ssadmin.sh soft_restart

## 修改流量统计间隔
默认的流量采样间隔为5分钟

流量间隔可根据实际需求调整，但最好不要太小，比如小于10秒

打开文件sslib.sh，修改INTERVEL的值，单位为秒。比如设置流量间隔为10分钟：

    INTERVEL=600

## 修改ssserver文件位置
如果shadowsocks不是使用apt-get或者pip安装，无法自动找到ssserver文件时，请手动指定程序的具体位置。

打开文件sslib.sh，修改SSSERVER的值，比如ssserver的路径为/usr/local/bin/ssserver时，修改为

    SSSERVER=/usr/local/bin/ssserver

## 文件夹中的相关文件
* ssadmin.sh - 管理程序，所有命令通过该程序执行

* sscounter.sh - 流量统计程序。由ssadmin.sh自动调用执行，注意：不要手动运行该程序

* sslib.sh - 包含一些参数配置和流量统计函数。由ssadmin.sh自动调用执行，注意：不要手动运行该程序

* ssmlt.template - ssserver的配置文件

程序运行后，会产生以下文件：
* ssmlt.json - 根据用户列表和ssmlt.template生成的ssserver实际使用的配置文件

* ssusers - 用户列表，包括端口、密码、流量限制参数。ssadmin.sh showpw 命令，显示该文件内容。

* sstraffic - 用户流量使用情况，包括流量限制，已用流量，剩余流量等。ssadmin.sh show 命令，显示该文件内容。

* traffic.log - 用户流量记录，供程序内部使用。

* 其它文件 - .tmp、.lock、.pid等文件、文件夹tmp及其中文件为程序内部使用文件，请不要手动删除。




