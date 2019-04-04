---
title: homebridge部署
comments: true
categories:
  - Lifestyle
tags:
  - null
abbrlink: '42251019'
date: 2019-04-04 11:10:35
---

# homebridge部署

## 安装 Node.js

```bash
wget https://nodejs.org/dist/v4.3.2/node-v4.3.2-linux-armv6l.tar.gz
tar -xvf node-v4.3.2-linux-armv6l.tar.gz
cd node-v4.3.2-linux-armv6l
sudo cp -R * /usr/local/
```

完成之后，输入 node -v 检查一下是否安装完成，如果显示 v4.3.2 ，则表示 Node.js 安装成功。

## 安装 Avahi 和相关软件包

需要安装这些包：

**libavahi-client3_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-client3_0.7-3_armhf.deb`

**libavahi-client-dev_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-client-dev_0.7-3_armhf.deb`

**libavahi-common3_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-common3_0.7-3_armhf.deb`

**libavahi-common-dev_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-common-dev_0.7-3_armhf.deb`

**libavahi-compat-libdnssd1_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-compat-libdnssd1_0.7-3_armhf.deb`

**libavahi-compat-libdnssd-dev_0.7-3_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/a/avahi/libavahi-compat-libdnssd-dev_0.7-3_armhf.deb`

**libdbus-1-3_1.10.22-0+deb9u1_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/d/dbus/libdbus-1-3_1.10.22-0+deb9u1_armhf.deb`

**libdbus-1-dev_1.10.22-0+deb9u1_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/d/dbus/libdbus-1-dev_1.10.22-0+deb9u1_armhf.deb`

最主要是安装**libavahi-compat-libdnssd-dev_0.7-3_armhf.deb**，其他都是依赖，需要按照依赖树来安装

从下往上安装。

安装方法是先 wget，然后 `sudo dpkg -i xx.deb`

![部署步骤](15205633704502.png)

安装完成后还有两个因为更新了包需要更新的

**dbus_1.10.22-0+deb9u1_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/d/dbus/dbus_1.10.22-0+deb9u1_armhf.deb`

**dbus-x11_1.10.22-0+deb9u1_armhf.deb**
`https://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/pool/main/d/dbus/dbus-x11_1.10.22-0+deb9u1_armhf.deb`

参考资料
Debian官方`https://packages.debian.org/jessie/libavahi-compat-libdnssd-dev`

Tsinghua镜像`https://mirrors.tuna.tsinghua.edu.cn/raspbian/`

## 安装 homebridge 和依赖包

```bash
sudo npm install -g --unsafe-perm homebridge hap-nodejs node-gyp
cd /usr/local/lib/node_modules/homebridge/
sudo npm install --unsafe-perm bignum
cd /usr/local/lib/node_modules/hap-nodejs/node_modules/mdns
sudo node-gyp BUILDTYPE=Release rebuild
```

## 安装和配置 yeelight

```bash
sudo npm install -g homebridge-yeelight
cd /home/pi/.homebridge/
vi config.json
```

在这里是创建一个新文件，内容是

```json
{
    "bridge": {
        "name": "YeeBridge",
        "username": "B8:27:EB:CC:BF:AC",
        "port": 51825,
        "pin": "031-45-154"
    },

    "platforms": [
        {
            "platform" : "yeelight",
            "name" : "yeelight"
        }
    ]
}
```

输入 homebridge 就可以启动了

## 设置成后台服务以及开机自启

1. 在`/etc/init.d/`下新建名字是`homebridge`文件,内容是：

    ```bash
    #!/bin/sh
    ### BEGIN INIT INFO
    # Provides: homebridge
    # Required-Start:    $network $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: Start daemon at boot time
    # Description:       Enable service provided by daemon.
    ### END INIT INFO

    dir="/home/pi"
    cmd="DEBUG=* /usr/local/bin/homebridge"
    user="pi"

    name=`basename $0`
    pid_file="/var/run/$name.pid"
    stdout_log="/var/log/$name.log"
    stderr_log="/var/log/$name.err"

    get_pid() {
        cat "$pid_file"
    }

    is_running() {
        [ -f "$pid_file" ] && ps -p `get_pid` > /dev/null 2>&1
    }

    case "$1" in
        start)
        if is_running; then
            echo "Already started"
        else
            echo "Starting $name"
            cd "$dir"
            if [ -z "$user" ]; then
                sudo $cmd >> "$stdout_log" 2>> "$stderr_log" &
            else
                sudo -u "$user" $cmd >> "$stdout_log" 2>> "$stderr_log" &
            fi
            echo $! > "$pid_file"
            if ! is_running; then
                echo "Unable to start, see $stdout_log and $stderr_log"
                exit 1
            fi
        fi
        ;;
        stop)
        if is_running; then
            echo -n "Stopping $name.."
            kill `get_pid`
            for i in 1 2 3 4 5 6 7 8 9 10
            # for i in `seq 10`
            do
                if ! is_running; then
                    break
                fi

                echo -n "."
                sleep 1
            done
            echo

            if is_running; then
                echo "Not stopped; may still be shutting down or shutdown may have failed"
                exit 1
            else
                echo "Stopped"
                if [ -f "$pid_file" ]; then
                    rm "$pid_file"
                fi
            fi
        else
            echo "Not running"
        fi
        ;;
        restart)
        $0 stop
        if is_running; then
            echo "Unable to stop, will not attempt to start"
            exit 1
        fi
        $0 start
        ;;
        status)
        if is_running; then
            echo "Running"
        else
            echo "Stopped"
            exit 1
        fi
        ;;
        *)
        echo "Usage: $0 {start|stop|restart|status}"
        exit 1
        ;;
    esac
    exit 0
    ```

2. 然后更改 homebridge 文件的权限

    `sudo chmod +777 /etc/init.d/homebridge`

3. 然后加入服务和系统启动

    `sudo update-rc.d homebridge defaults`

然后就可以通过 service homebridge start /stop/status 来控制服务开启和关闭，查看状态了
重启一下同样就可以自动启动了

输出的内容在/var/log/下的 homebridge.log 中

如果 IOS 端出现重置要求，把/home/pi/.homebridge/下的 persist 和 accessories 两个文件夹删除

参考资料：
开机自启添加方法 `http://blog.csdn.net/hshl1214/article/details/47109041`

服务和自启 Demo `https://raw.githubusercontent.com/fhd/init-script-template/master/template`

## 定时重启

因为 yeelight 构件长时间以及路由器断开后会 unreachable，所以需要定时重启 homebridge 服务

`sudo crontab -e`

加入：

`0,30 * * * * sudo service homebridge restart`

然后

`sudo /etc/init.d/cron restart`

参考资料：

[树莓派脚本](http://blog.csdn.net/lan120576664/article/details/50364606)
[crontab使用详解](https://www.cnblogs.com/intval/p/5763929.html)

## 参考资料

`https://sspai.com/post/39851`

`https://sspai.com/post/36492`
