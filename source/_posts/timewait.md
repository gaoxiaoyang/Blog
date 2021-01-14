---
title: TIME_WAIT 过多的解决办法
date: 2020-11-24 10:21:41
tags: 
- linux
- TCP
- TIME_WAIT
---

1.  `TIME_WAIT`的作用:

   ```
   TIME_WAIT状态存在的理由:
   可靠地实现TCP全双工连接的终止
   在进行关闭连接四次挥手协议时，最后的ACK是由主动关闭端发出的，如果这个最终的ACK丢失，服务器将重发最终的FIN，
   因此客户端必须维护状态信息允许它重发最终的ACK。如果不维持这个状态信息，那么客户端将响应RST分节，服务器将此分节解释成一个错误（在java中会抛出connection reset的SocketException)。
   因而，要实现TCP全双工连接的正常终止，必须处理终止序列四个分节中任何一个分节的丢失情况，主动关闭的客户端必须维持状态信息进入TIME_WAIT状态。
   
   ```

2.  大量`TIME_WAIT`造成的影响:

   ```
   在高并发短连接的TCP服务器上，当服务器处理完请求后立刻主动正常关闭连接。
   这个场景下会出现大量socket处于TIME_WAIT状态。如果客户端的并发量持续很高，
   此时部分客户端就会显示连接不上。
   主动正常关闭TCP连接，都会出现TIME_WAIT
   高并发可以让服务器在短时间范围内同时占用大量端口，而端口有个0~65535的范围，并不是很多，刨除系统和其他服务要用的，剩下的就更少了
   ```

- 查看TCP状态：`netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'`

  <!--more-->

  状态描述:

  ```
  CLOSED：无连接是活动的或正在进行
  LISTEN：服务器在等待进入呼叫
  SYN_RECV：一个连接请求已经到达，等待确认
  SYN_SENT：应用已经开始，打开一个连接
  ESTABLISHED：正常数据传输状态
  FIN_WAIT1：应用说它已经完成
  FIN_WAIT2：另一边已同意释放
  ITMED_WAIT：等待所有分组死掉
  CLOSING：两边同时尝试关闭
  TIME_WAIT：另一边已初始化一个释放
  LAST_ACK：等待所有分组死掉
  ```

- 查看 SOCKET状态: `cat /proc/net/sockstat`

- 内核优化参数处理`TIME_WAIT`过多

  编辑内核文件`/etc/sysctl.conf`

  ```
  net.ipv4.tcp_syncookies = 1 表示开启SYN Cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
  net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
  net.ipv4.tcp_tw_recycle = 1 表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭。
  net.ipv4.tcp_fin_timeout 修改系默认的 TIMEOUT 时间
  ```

  执行 `sysctl -p` 生效

