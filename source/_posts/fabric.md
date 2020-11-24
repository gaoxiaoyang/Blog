---
title: python 远程部署 Fabric
date: 2020-11-24 11:10:00
tags:
- Fabric
- python
---

- ####  安装`Fabric`

  Fabric的官网是 [www.fabfile.org](http://www.fabfile.org/)，源码托管在[Github](https://github.com/fabric/fabric)上。你可以clone源码到本地，然后通过下面的命令来安装。但是在源码安装前，你必须先将Fabric的依赖包[Paramiko](http://www.paramiko.org/)装上

  ```bash
  python setup.py develop
  ```

  可以使用pip安装

  ```bash
  pip install fabric
  ```

  `python3` 安装时使用`fabric3` （安装`fabric3`之前，需要先卸载`fabric`）

  ```bash
  pip uninstall fabric
  pip3 install fabric3
  ```

  也可以使用`yum`安装

  ```bash
  yum install fabric
  ```

  <!--more-->

- #### `Fabric` 示例

  ```python
  from fabric.api import *
  
  #env.hosts = ['96.11.1.58', '96.11.1.59']
  env.password = 'admin'
  env.user = 'root'
  
  with open('ip.conf') as fp:
      lines = fp.readlines()
      env.hosts = lines
      print(lines)
  
  def test():
      run('uname -a')
  
  def test2():
      with cd('/opt/log'):
          run("tail -n 5 debug.log")
  
  def test3(name = 'world'):
      print("hello " + name)
  ```


- #### 执行任务 `test`

    ```bash
    fab -f fab.py test
    ```

- #### 执行任务有参数任务`test3`

  ```bash
  fab -f fab.py test3:name=test3
  ```
