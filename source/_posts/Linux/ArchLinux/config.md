---
title: ArchLinux初始配置
date: 2021-1-9
tags:
	- ArchLinux
	- Linux
categories:
	- [Linux, ArchLinux]
description: 安装好 ArchLinux 之后，还需要做一些通用的配置。
---

## 连接网络

> 有线网络

```shell
dhcpcd &
```

> 无线网络

```shell
wpa_passphrase ESSID password > interface.file
wpa_supplicant -B -c interface.file
dhcpcd &
```

## 创建一个普通用户

```shell
useradd -m -G wheel username
passwd username
```

## 配置sudo

```shell
pacman -S sudo vi
visudo
----------
# %wheel ALL=(ALL) ALL
%wheel ALL=(ALL) ALL
```

退出root用户，使用普通用户登入

## 添加Archlinuxcn源

> 修改配置文件 `/etc/pacman.conf`

```ini
# [multilib]
# Include = /etc/pacman.d/mirrorlist
[multilib]
Include = /etc/pacman.d/mirrorlist
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

> 更新签名

```shell
sudo rm -rf /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate
sudo pacman -Syyu archlinuxcn-keyring
```

> 安装AUR

```shell
sudo pacman -S yay
```

## 安装中文字体/表情

> Emoji

```shell
yay -S ttf-droid ttf-symbola ttf-ubraille ttf-joypixels ttf-liberation ttf-inconsolata noto-fonts-emoji ttf-linux-libertine
```

> Chinese

```shell
yay -S wqy-zenhei wqy-microhei wqy-bitmapfont wqy-microhei-lite adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```

## 安装图形界面

> 安装X服务

```shell
yay -S xorg xorg-server xorg-xinit
```

### 安装i3wm桌面

```shell
yay -S i3
cp /etc/X11/xinit/xinitrc ~/.xinitrc
vim ~/.xinitrc
---------
exec xterm -geometry 80x66+0+0 -name login
# exec xterm -geometry 80x66+0+0 -name login
exec i3
```

> 启动桌面

```shell
startx
```

### 安装KDE桌面

```shell
yay -S plasma kde-applications-meta sddm networkmanager
# 开机自启
systemctl enable sddm dhcpcd
# 启动桌面
systemctl start sddm dhcpcd
```

## 美化终端

```shell
yay -S zsh
git clone https://github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
chsh -s $(which zsh)
```

> 登入自启wm桌面

- zshrc添加以下内容

```ini
if [[ ! $DISPLAY && $XDG_VTNR -eq 1 ]]; then
    exec startx
fi
```

## 安装应用程序

> Chrome or Chromium

```shell
yay -S google-chrom
yay -S chromium
```

> 输入法

```shell
yay -S fcitx5 fcitx5-configtool fcitx5-qt fcitx5-gtk
# 输入法
yay -S fcitx5-rime  #中文输入法
yay -S fcitx5-anthy #日语输入法

# 配置文件
vim ~/.xprofile
----------
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS="@im=fcitx5"
----------

vim ~/.pam_environment
----------
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE  DEFAULT=fcitx
XMODIFIERS    DEFAULT=\@im=fcitx
SDL_IM_MODULE DEFAULT=fcitx
----------

reboot
```

> wm背景

```shell
yay -S feh compton
feh --randomize --bg-fill imagePath
```

> 社交软件

- QQ

```shell
yay -S deepin.com.qq.im
```

- TIM

```shell
yay -S deepin.com.qq.office gnome-settings-daemon
/usr/lib/gsd-xsettings & # 使用TIM需要运行该脚本
```

- TIM无法加载图片

```shell
# TIM无法使用IPv6，禁用IPv6即可
# 我是使用IPv4代理
yay -S privoxy # 安装代理工具
systemctl start privoxy # 启动代理
systemctl enable privoxy # 开机自启
# 查看代理IP与端口号
cat /etc/privoxy/config | grep listen-address
```

- WeChat

```shell
yay -S deepin-wine-wechat
```

> MySQL

```shell
yay -S mariadb
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
systemctl start mysqld
mysql_secure_installation
```

> 音乐播放器

```shell
yay -S netease-cloud-music
yay -S deepin.com.qq.qqmusic
yay -S kreogist-mu
```

> 系统声音

```shell
yay -S alsa-utils
```

> 笔记本屏幕背光

```shell
yay -S acpilight
```

> Markdown

```shell
yay -S typora
```
