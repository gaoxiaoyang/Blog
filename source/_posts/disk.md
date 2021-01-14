---
title: Linux 下测试磁盘读写io
date: 2020-12-02 14:53:34
tags:
- disk
- linux
---

### 一.  hdparm

1.   使用方法如下:

    ~~~bash
      [root@cn13 ls]# hdparm -Tt /dev/vdc1 

      /dev/vdc1:
      Timing cached reads:   13200 MB in  2.00 seconds = 6605.18 MB/sec
      Timing buffered disk reads: 296 MB in  3.01 seconds =  98.43 MB/sec
    ~~~


### 二. dd

1. 首先了解两特殊设备

- /dev/null 伪设备, 回收站, 写该文件不会产生IO
- /dev/zero 伪设备, 会产生空字符流, 对它不会产生IO
  
    
2. 测试方法

- 测试磁盘的IO写速度, 如果要测试实际速度 还要在末尾加上 oflag=direct测到的才是真实的IO速度

     ~~~bash
     [root@lbs13 ls]# time dd if=/dev/zero of=test.dbf bs=8k count=300000
      300000+0 records in
     300000+0 records out
     2457600000 bytes (2.5 GB) copied, 5.06596 s, 485 MB/s
     
     real	0m5.095s
     user	0m0.034s
     sys	    0m4.785s
    ~~~
   
- 测试磁盘的IO读速度
  
  ```bash
  [root@cnsqwllbs13 lbs]# dd if=test.dbf bs=8k count=300000 of=/dev/null 
  300000+0 records in
  300000+0 records out
  2457600000 bytes (2.5 GB) copied, 1.89748 s, 1.3 GB/s
  ```
  
  
  