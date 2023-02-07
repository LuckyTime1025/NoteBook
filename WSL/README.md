# Wsl 备忘

## wsl 默认安装位置

C:\Users\THINK\AppData\Local\Packages\

## wsl 安装

1. 开启 Hyper-V 和适用于 Linux 的 Windows 子系统
2. [下载 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)
3. 更新 wsl ```wsl --update```
4. 设置 wsl 默认版本 ```wsl --set-default-version 2```
5. 查看 wsl 版本 ```wsl --version```
6. [下载 ArchLinux 发行版](https://wsldl-pg.github.io/ArchW-docs/)

## 配置静态 IP

1. 在 Hyper-V 里创建外部虚拟交换机
2. 在 ```%USERPROFILE%``` 中创建 .wslconfig 并进行如下配置

```conf
[wsl2]
networkingMode=bridged # 桥接模式
vmSwitch=WSLBridge # 你想使用的网卡
ipv6=true # 启用 IPv6
```

3. 在 /etc/systemd/network 中创建 ```10-static-eth0.network``` 并进行如下配置

```conf
[Match]
Name=eth0 # 网卡名称

[Network]
Address=192.168.100.100 # IP 地址
Gateway=192.168.100.2 # 网关地址
DNS=8.8.8.8 8.8.4.4 223.5.5.2 223.6.6.6 # DNS
```

## Arch 国内源

/etc/pacman.d/mirrorlist

```conf
# 中科大
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
# 阿里云
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
```

/etc/pacman.conf

```conf
[archlinuxcn]
Server = https://repo.archlinuxcn.org/$arch
```

安装 archlinuxcn-keyring 包以导入 GPG key

```shell
pacman -S archlinuxcn-keyring
```

## Arch 中文

1. 本地化配置

编辑 /etc/locale.gen 去掉 ```en_US.UTF-8``` 及 ```zh_CN.UTF-8``` 前的 ```#```
2. 执行 ```locale-gen```
3. ```echo 'LANG=en_US.UTF-8' > /etc/locale.conf```
4. 在 ~/.bashrf 开头设置

```shell
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```

## 一些软件

```shell
pacman -S --needed --noconfirm vim git base-devel
# --needed 不重复安装
# --noconfirm 不询问
```

## 一些问题

### 安装软件报错

> ldconfig: /usr/lib/wsl/lib/libcuda.so.1 不是符号链接

```bash
cd /usr/lib/wsl
sudo mkdir lib2
sudo ln -s lib/* lib2
sudo ldconfig
```

编辑 ```/etc/ld.so.conf.d/ld.wsl.conf``` 将 ```/usr/lib/wsl/lib``` 改为 ```/usr/lib/wsl/lib2```

> 更改链接路径后,更新驱动之后需要重新链接,否则 lib2 中和 lib 中不一致,从而导致wsl中不可使用windows下的驱动

重启之后 wsl 会自动还原,不还原需要修改 /etc/wsl.conf

```conf
[automount]
ldconfig = fasle
```

Actually this is not relate to Arch, nor ArchWSL. It's caused by libcuda.so in your C:\Windows\System32\lxss\lib\ folder not a symbolic link, which is installed by nvidia driver. One solution to fuck the warning is delete libcuda.so and libcuda.so.1 and use
make symbolic link to libcuda.so.1.1. Command line: mklink <target file> <source file>. Note the command not work in powershell, you shall use cmd.exe.
:)
