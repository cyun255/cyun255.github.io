---
title: ArchLinux安装教程
date: 2021-1-9
tags:
	- ArchLinux
	- Linux
categories:
	- [Linux, ArchLinux]
description: ArchLinux 跟其他发行版有所不同，无法直接通过图形界面安装，live 系统还是一个 Shell 终端，必须在命令行一步一步的完成整个安装系统的过程。
---

## 连接网络

> 有线网络

```shell
dhcpcd &
```

> 无线网络

```shell
wifi-menu
```

> 测试

```shell
ping www.rescld.cn
```

> 同步时间

```shell
timedatectl set-ntp true
```

## 磁盘分区

> 查看现有磁盘

```shell
lsblk
```

> 进入磁盘分区

```shell
cfdisk /dev/sda
```

- `sda` 改成你需要分区的磁盘名称

> 分区示例

- `BIOS` 和 `MBR`

| 挂载点 | 分区      | 分区类型   | 建议大小 |
| ------ | --------- | ---------- | -------- |
| /mnt   | /dev/sda1 | Linux      | 剩余空间 |
| [SWAP] | /dev/sda2 | Linux swap | 512MB    |

- `UEFI` 和 `GPT` 

| 挂载点    | 分区      | 分区类型   | 建议大小  |
| --------- | --------- | ---------- | --------- |
| /mnt/boot | /dev/sda1 | EFI        | 260-512MB |
| /mnt      | /dev/sda2 | Linux      | 剩余大小  |
| [SWAP]    | /dev/sda3 | Linux swap | 512MB     |

> 格式化分区

```shell
mkfs.fat -F32 /dev/sda1    # EFI分区
mkfs.ext4 /dev/sda2    # 主分区
mkswap /dev/sda3    # 交换分区
```

> 开启 `SWAP`

```shell
swapon /dev/sda3
```

## 配置 `pacman`

- `Color` 的注释

```shell
vim /etc/pacman.conf
----------------
# Color
Color
```

- 将 `China` 的源提前

```shell
vim /etc/pacman.d/mirrorlist
```

## 挂载磁盘

> 查看现有磁盘

```shell
lsblk
```

> 挂载

```shell
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

## 安装系统

```shell
pacstrap /mnt base linux linux-firmware
```

### 生成挂载文件

```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

### 系统配置

#### 进入安装好的系统

```shell
arch-chroot /mnt
```

#### 设置时区和时间

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

#### 安装编辑器

```shell
pacman -S vim
```

#### 编辑本地化文件

```shell
vim /etc/locale.gen
--------------------------
# en_US.UTF-8 UTF-8
en_US.UTF-8 UTF-8

# zh_CN.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

#### 本地化

```shell
locale-gen
```

#### 系统语言

```shell
vim /etc/locale.conf
-------------------------
LANG=en_US.UTF-8
```

#### 网络配置

- 主机名

```shell
echo 主机名 > /etc/hostname
```

- 解析

```shell
vim /etc/hosts
-----
127.0.0.1    localhost
::1    localhost
127.0.1.1    主机名.localdomain 主机名
```

#### 修改root密码

```shell
passwd
```

#### 安装引导

> UEFI

```shell
pacman -S grub efibootmgr intel-ucode os-prober
mkdir /boot/grub
grub-mkconfig -o /boot/grub/grub.cfg
uname -m
grub-install --target=x86_64-efi --efi-directory=/boot
```

- `intel-ucode`厂家更新CPU驱动用，如果是`AMD`，则安装`amd-ucode`
- 使用`uname -m`查看系统架构，我的系统架构是`x86_64`
- `os-prober`可以检测多系统

> BIOS

```shell
pacman -S grub efibootmgr intel-ucode os-prober parted
grub-install --recheck /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg

-------WARNING处理-----------
parted /dev/sda unit s print
parted /dev/sda set 1 bios_grub on
partprobe /dev/sda
grub-install --recheck /dev/sda --efi-directory=/boot
grub-mkconfig -o /boot/grub/grub.cfg
```

#### 安装联网工具

```shell
pacman -S wpa_supplicant dhcpcd
```

### 安装完成

```shell
exit
umount -R /mnt
killall wpa_supplicant dhcpcd
reboot
```
