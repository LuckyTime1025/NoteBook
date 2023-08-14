# Arch Linux

## 启动盘制作工具

- [Ventoy](<https://www.ventoy.net/cn/download.html> "Ventoy")
- [Etcher](<https://www.balena.io/etcher> "Etcher")

## Arch Linux 安装

### 验证引导模式

``` bash
ls /sys/firmware/efi/efivars
```

### 连接到 WiFi

``` bash
# 查看网络接口
ip link
 
# 连接 WiFi
iwctl
# 列出所有 WiFi 设备
device list
# 扫描网络
station device scan
# 列出所有可用网络
station device get-networks
# 连接到一个网络
station device connect SSID
# 退出 iwd
exit
 
# 检查网络连接
ping baidu.com
```

### 更新系统时间

``` bash
# 确保系统时间是最新的
timedatectl set-ntp true
# 检查服务状态
timedatectl status
```

### 分区

#### 建立硬盘分区

|       |       |       |       |       |
| :---: | :---: | :---: | :---: | :---: |
|   /   | 100G  | /srv  |  15G  |
| /boot | 512M  | /home | 200G  |
| SWAP  |  8G   | /usr  | 200G  |
| /opt  | 100G  | /var  | 76.9G |
| /tmp  |  15G  |

#### 格式化硬盘分区

```bash
# 格式化分区( btrfs 文件系统 )
mkfs.btrfs /dev/root_partition
 
# 初始化交换分区
mkswap /dev/swap_partition
 
# 格式化EFI系统分区
mkfs.fat -F 32 /dev/efi_system_partition
```

#### 挂载分区

```bash
# 挂载根分区
mount /dev/root_partition /mnt
# 挂载 EFI 系统分区
mount /dev/efi_system_partition /mnt/boot
# 启用交换分区
swapon /dev/swap_partition
 
# 挂载到 btrfs 子卷
# 先挂载到 /mnt
mount /dev/root_partition /mnt
# 进入 /mnt 目录
cd /mnt
# 创建 rootfs 子卷
btrfs subvolume create rootfs
# 退出 /mnt 目录
cd
# 卸载 /mnt
umount /dev/root_partition
# 挂载 /mnt 到 rootfs 子卷

# 固态硬盘
mount -o subvol=rootfs,compress=lzo,autodefrag,noatime,discard,ssd,space_cache /dev/root_partition /mnt
# compress=lzo 压缩算法  
# autodefrag 自动碎片整理  
# noatime 不更新文件访问时间  
# discard 丢弃数据  
# ssd 只读 SSD 盘  
# space_cache 空间缓存  
# subvol=rootfs 挂载到 rootfs 子卷  

# 机械硬盘
mount -o subvol=rootfs,compress-force=lzo,autodefrag,noatime,space_cache /dev/root_partition /mnt
# compress-force=lzo 强制压缩算法  
```

#### 安装系统

```bash
pacstrap /mnt base base-devel linux linux-firmware linux-headers networkmanager vim

# base 定义基本 Arch Linux 安装的最小软件包集
# base-devel #编译工具
# linux Linux 内核
# linux-firmware Linux 内核固件
# networkmanager 网络管理器
# vim 文本编辑器
```

#### 生成 fstab 文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
# 查看 fstab 文件
cat /mnt/etc/fstab
```

#### Chroot 到新安装的系统

```bash
arch-chroot /mnt
```

#### 配置系统

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
 
# 生成 /etc/adjtime
hwclock --systohc
```

#### 本地化

编辑 /etc/locale.gen，然后取消掉 en_US.UTF-8 UTF-8 前的注释（#）。

接着执行 locale-gen 以生成 locale 信息。

```bash
locale-gen
```

然后创建 locale.conf 文件，并
编辑设定 LANG 变量：

```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

#### 网络配置

```bash
/etc/hostname
 
myhostname（主机名）
 
 
/etc/hosts
 
127.0.0.1        localhost
::1              localhost
127.0.1.1        myhostname.localdomain        myhostname
```

编辑 /etc/mkinitcpio.conf

```bash
nano /etc/mkinitcpio.conf
# 在 HOOK 中增加 usr
mkinitcpio -P
```

#### 设置 root 密码

```bash
passwd
```

#### 创建新用户

```bash
useradd -m -G wheel username
 
# 为新用户设置密码
passwd username
 
EDITOR=nano visudo
```

#### 安装引导程序

``` bash
pacman -S grub efibootmgr intel-ucode
# intel-ucode CPU微码
# GRUB 启动引导器
# efibootmgr 被 GRUB 脚本用来将启动项写入 NVRAM
 
# 安装 GRUB
grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
 
# 生成 /boot/grub/grub.cfg
grub-mkconfig -o /boot/grub/grub.cfg
```

重启  
输入 exit 或按 Ctrl+d 退出 chroot 环境。  
用 umount -R /mnt 手动卸载被挂载的分区  
执行 reboot 重启系统，systemd 将自动卸载仍然挂载的任何分区

## Arch Linux 源

编辑 /etc/pacman.d/mirrorlist

```conf
# 中科大
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
# 阿里云
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
# 清华大学
Serber = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

### archlinuxcn 源

编辑 /etc/pacman.conf 在文件末尾添加以下两行

```conf
[archlinuxcn]
Server = https://repo.archlinuxcn.org/$arch
```

之后安装 archlinuxcn-keyring 包导入 GPG key

```bash
pacman -S archlinuxcn-keyring
```

## Arch Linux Fonts

```bash
pacman -S wqy-microhei wqy-zenhei wqy-bitmapfont ttf-jetbrains-mono
```

## Arch Linux KDE

安装 xorg 和 plasma-desktop sddm

```bash
pacman -S xorg-server plasma sddm 
# plasma-desktop 最小安装
```

## 安装常用软件

```bash
sudo pacman -S dolphin discover konsole kate ark kcalc
 
# dolphin 文件管理器
# discover 软件管理中心 #需要安装后端程序 packagekit-qt5
# konsole KDE终端
# kate 文本编辑器
# ark 压缩包管理工具
# kcalc 科学计算器
# kmines 扫雷
# okular 文档查看
# sweeper 系统清理
# kcolorchooser 拾色器
# obs-studio OBS Studio
# flameshot 火焰截图
# firefox 火狐浏览器
```

> [KDE 应用程序](<https://apps.kde.org/zh-cn/>)

设置 sddm 开机自启

``` bash
systemctl enable sddm
```

## KDE 美化

主题：[WhiteSur-dark](<https://store.kde.org/p/1400424>) [WhiteSur](<https://store.kde.org/p/1398840>) [Moe](<https://store.kde.org/p/1338879>) [Apus](<https://store.kde.org/p/1737857>) [Otto](<https://store.kde.org/p/1360125>) [Lisa](<https://store.kde.org/p/1370894>)

工具：[Kvantum Manager](<>) [latte-dock](<https://github.com/KDE/latte-dock>)

可供参考：[GitHub - orangbus/Tool: Manjaro 从入门到爱不释手](<https://github.com/orangbus/Tool>)

## GRUB 美化

访问 [Gnome-look.org](<https://www.gnome-look.org/>) 下载 GRUB Themes

把解压后的主题文件放到 /boot/grub/themes 或 /usr/share/grub/themes 文件夹中

```bash
sudo cp /your/path/Vimix /usr/share/grub/themes/Vimix -rf
```

修改 /etc/default/grub 文件找到“#GRUB_THEME=”指向自定义的主题文件中的 theme.txt

```bash
GRUB_THEME="/usr/share/grub/themes/vimix/theme.txt"
```

重新生成 grub.cfg

```bash
grub-mkconfig -o /boot/grub/grub.cfg 
```

## 输入法安装——Fcitx5

```bash
# 安装 fcitx5-im 和 fcitx5-chinese-addons
sudo pacman -S fcitx5-im fcitx5-chinese-addons
```

编辑 ~/.pam_environment 写入以下内容

```conf
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE  DEFAULT=fcitx
XMODIFIERS    DEFAULT=\@im=fcitx
INPUT_METHOD  DEFAULT=fcitx
SDL_IM_MODULE DEFAULT=fcitx
```

## 输入法安装——IBus

```bash
# ibus-rime 依赖 IBus. 会自动安装
sudo pacman -S ibus-rime

# 编辑 ~/.xprofile 写入以下内容
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus

ibus-daemon -x -d
```

## NVIDIA 安装

### 查看显卡

```bash
lspci -k | grep -A 2 -E "(VGA|3D)"
```

### 安装显卡

```bash
pacman -S nvidia xf86-video-intel
```

### 双显卡

[NVIDIA Optimus](<https://wiki.archlinux.org/title/NVIDIA_Optimus#Using_optimus-manager>)

[Optimus](<https://github.com/Askannz/optimus-manager>)

## 安装 Steam

```bash
/etc/pacman.conf
 
[multilib]
Include = /etc/pacman.d/mirrorlist
 
 
pacman -S steam
```
