---
title: Arch Linux 系统相关
date: 2021-01-18 15:57:41
tags: Linux
---

## 桌面

- 安装X-org相关软件

- 安装DWM DMENU相关

- 安装Simple-Terminal。

## 亮度调节**(**需要权限**)**

首先查看系统支持的最大亮度

```shell
cat /sys/class/backlight/$VIDEO_CARD/max_brightness

echo $BRIGHTNESS_VAULE > /sys/class/backlight/$VIDEO_CARD/brightness
```

| 参数              | 描述                     |
| ----------------- | ------------------------ |
| $VIDEO_CARD       | 显卡品牌不同，文件夹不同 |
| $BRIGHTNESS_VAULE | 根据最大亮度进行区间调节 |



---



## 音量调节


- 使用Alsamixer软件进行调节

```shell
sudo pacman -S alsa-utils

alsamixer
```



---



## 时间调节

- 查看当前时间状态

```shell
timedatectl status
```

- 设置系统时间

```shell
timedatectl set-time $TIME
```

- 列出所有时区

```shell
timedatectl list-timezones
```

- 设置时区

```shell
timedatectl set-timezone $ZONE
```

- 是否打开时间同步

```shell
timedatectl set-ntp $BOOL
```


| 参数  | 描述        |
| ----- | ----------- |
| $TIME | 时间        |
| $ZONE | 时区        |
| $BOOL | true或false |



---



## 网络

- 安装iwd

```shell
sudo pacman -S iwd
```

- 启动iwd服务

```shell
systemctl start iwd
```

- 打开iwctl

```shell
iwctl
```

- 在iwctl中操作

- 查看机器的网卡设备