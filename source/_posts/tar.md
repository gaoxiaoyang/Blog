---
title: tar 用法
date: 2020-12-04 10:09:26
tags:
- tar
- linux
---

- 压缩

  ~~~shell
  tar czvf 01.tar.gz 01/
  ~~~

- 使用多线程压缩 目录`01`，`02`，`03`

  ~~~shell
  tar cvf - 01/ 02/ 03/ | pigz -6 -p 8 > out.tar.gz
  ~~~

  `-p` 指定多核
  
  `-6` 指定`pigz`压缩比
  
- 压缩的同时删除原文件

  ~~~shell
  tar czvf 01.tar.gz 01/ --remove-files
  ~~~

  `--remove-files`指定压缩的同时，删除原文件

- 解压

  ~~~
  tar xzvf 01.tar.gz -C /tmp
  ~~~

  `-C`指定输出目录

