# Linux 学习笔记

## 查看文件内容

### 查看前二十行内容

```bash
head -n 20 <file name>
```

### 查看后二十行内容

```bash
tail -n 20 <file name>
```

## 软连接

```bash
ln -s info myinfo
```

## 硬链接

```bash
ln a.txt b.txt
```

## 历史命令

### 查看历史命令

```bash
history
```

### 清空历史命令

```bash
history -c
```

### 执行历史命令

```bash
!730
```

## 日期

```bash
date +%Y # 年
date +%y # 年份后两位
date +%m # 月
date +%d # 日
date +%D # 月/日/年
date +%H # 时
date +%h # 几月
date +%M # 分
date +%S # 秒
date +%s # 当前时间戳
date -d "1 days ago"  # 前一天
date -d "-1 days ago" # 后一天
```

|        |     |
| ------ | --- |
| year   | 年  |
| month  | 月  |
| day    | 日  |
| hour   | 时  |
| minute | 分  |
| second | 秒  |

## 日历

|               |                          |
| ------------- | ------------------------ |
| -1, --one     | 只显示当前月份 (默认)     |
| -3, --three   | 显示上个月、当月和下个月 |
| -s, --sunday  | 周日作为一周第一天       |
| -m, --monday  | 周一用为一周第一天       |
| -j, --julian  | 输出儒略日               |
| -y, --year    | 输出整年，可以指定年份   |
| -V, --version | 显示版本信息并退出       |
| -h, --help    | 显示此帮助并退出         |

## 进程管理

### ps

```bash
ps aux # 查看系统中所有进程
ps -ef # 可以查看子父进程之间的关系
```

### top

```bash
top
```

|         |                                             |
| ------- | ------------------------------------------- |
| -d 秒数 | 指定 top 命令每隔几秒更新。默认 3 秒            |
| -i      | 使 top 不显示任何限制或者僵尸进程             |
| -p      | 通过指定监控进程 PID 来仅仅监控某个进程的状态 |
| P       | 以 CPU 使用率排序。默认                       |
| M       | 以内存使用率排序                            |
| N       | 以 PID 排序                                   |
| q       | 退出 top                                     |

### 定时任务

```bash
crontab #系统定时任务
```

|     |                               |
| --- | ----------------------------- |
| -e  | 编辑 crontab 定时任务           |
| -l  | 查询 crontab 任务               |
| -r  | 删除当前用户所有的 crontab 任务 |

## 修改 linux root 密码

### 修改 GRUB 引导

```bash
rw rd.break init=/sysroot/bin/sh

```

### 重新挂载

```bash
mount -o remount,rw /sysroot/
```

### 改变根

```bash
chroot /sysroot/
```

### 重置密码

```bash
echo 1|passwd --stdin root
```

### 忽略 selinux 检查本次密码修改

```bash
touch /.autorelabel
```

## 配置静态 IP

### 进入目录 `/etc/sysconfig/network-scripts`

```shell
cd /etc/sysconfig/network-scripts
```

### 编辑 `ifcfg-ens33` 文件

```conf
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static # 设置静态 IP
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33 # 网卡名称
UUID=8c86febe-9507-4a6b-8980-7ceb488d724e
DEVICE=ens33
ONBOOT=yes # 开机自启
IPADDR=xxx.xxx.xxx.xxx #新增：配置 IP 地址
NETMASK=xxx.xxx.xxx.xxx # 新增：配置子网掩码
GATEWAY=xxx.xxx.xxx.xxx # 新增：配置网关
DNS1=xxx.xxx.xxx.xxx # 新增：配置 DNS
```

### 重启 network

```shell
service network restart
```
