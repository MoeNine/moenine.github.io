---
title: arch_linux_install
date: 2021-01-19 13:02:20
tags: Linux
---

# ArchLinux软件安装

将软件移动到/usr/bin 下 即可被DMenu检测到

或使用ln创建软连接 即#

```shell 
ln -s $SOFTWARE_LOCAL /usr/bin
```


| 参数            | 描述     |
| --------------- | -------- |
| $SOFTWARE_LOCAL | 软件路径 |

## Pacman

- 安装软件包

```shell
pacman -S
```

- 查看软件包详细信息

```shell
pacman -Si
```

