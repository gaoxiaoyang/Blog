---
title: ram disk on Linux
date: 2018-12-24 16:26:58
tags:
 - Linux
 - ram
---

#### Mount ram disk

```bash
$ mkdir /mnt/tmpfs
$ mount -t tmpfs -o size=100m tmpfs /mnt/tmpfs
$ mount -o remount,size=200m /mnt/tmpfs
```

#### Umount

```bash
$ umount /mnt/tmpfs
```

