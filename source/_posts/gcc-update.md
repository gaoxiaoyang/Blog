---
title: CentOS 6 升级GCC 
date: 2021-01-11 15:24:41
tags:
- gcc
- Linux
- Centos6
---

####    获取安装包并解压

~~~bash
wget http://ftp.gnu.org/gnu/gcc/gcc-6.1.0/gcc-6.1.0.tar.bz2

tar -jxvf gcc-6.1.0.tar.bz2
~~~

当然，[http://ftp.gnu.org/gnu/gcc ](http://ftp.gnu.org/gnu/gcc) 里面有所有的gcc版本供下载

#### 下载编译所需的依赖

~~~bash
cd gcc-6.1.0
./contrib/download_prerequisites
~~~

<!--more-->

#### 建立一个目录供编译出的文件存放

   ~~~bash
   mkdir gcc-build-6.1.0
   cd gcc-build-6.1.0
   ~~~

#### 生成`Makefile`文件

   ~~~bash
   ../configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
   ~~~

#### 编译

~~~bash
[root@148bbbc1b745 /]# make -j4
~~~

#### 安装

~~~bash
[root@148bbbc1b745 /]# make install
~~~

#### 调整`gcc`相关路径

~~~bash
mv /usr/bin/gcc /usr/bin/gcc4.4.7
ln -s /usr/local/bin/gcc /usr/bin/gcc
/usr/bin/gcc -v
mv /usr/bin/g++ /usr/bin/g++4.4.7
ln -s /usr/local/bin/g++ /usr/bin/g++
mv /usr/bin/c++ /usr/bin/c++4.4.7
ln -s /usr/local/bin/c++ /usr/bin/c++
~~~

#### 查看`gcc` 版本

~~~bash
[root@148bbbc1b745 /]# gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/local/libexec/gcc/x86_64-pc-linux-gnu/6.1.0/lto-wrapper
Target: x86_64-pc-linux-gnu
Configured with: /gcc-6.1.0/configure -enable-checking=release -enable-languages=c,c++ -disable-multilib
Thread model: posix
gcc version 6.1.0 (GCC)
~~~

#### 写c++11代码测试 编译

~~~bash
[root@148bbbc1b745 /]# gcc -std=c++11 -o test1 test1.cpp
~~~

~~~c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> var_ptr(new int(5));
    std::cout << *var_ptr << std::endl;
    return 0;
}
~~~



