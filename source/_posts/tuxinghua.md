---
title: 解决ubuntu 16.04图形化
abbrlink: f140992e
date: 2022-10-19 17:19:53
tags:
---
```shell
sudo vim /etc/default/grub
GRUB_GFXMODE=1920x1080
GRUB_GFXPAYLOAD_LINUX=1920x1080
sudo update-grub
```

