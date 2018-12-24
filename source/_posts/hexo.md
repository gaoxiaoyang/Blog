---
title: Hexo
date: 2018-12-21 10:44:49
tags: hexo
---
使用 [Hexo](https://hexo.io/) 搭建自己博客

<!--more-->

### install Node

- Windows 安装 Node，进入 [nodejs](https://nodejs.org/en/) 下载安装

### Node 的淘宝镜像

 ```bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
 ```

### Install Hexo and initialize Pages

```bash
$ npm install hexo-cli -g
$ hexo init Blog
$ cd Blog
$ npm install
$ hexo server
```



### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to git

- 在 Github 创建一个名字为 `xxx.github.io` 的仓库(xxx 修改为你的 Github 名字)

- 博客地址自动为 `xxx.github.io`

- 安装`hexo` git 插件

  ```bash
  $ npm install hexo-deployer-git --save
  ```

- 在 `Blog/_config.yml` 中配置 git

  ```bash
  deploy:
      type: git
      repo: git@github.com:xxx/xxx.github.io.git
  ```

- 发布 `注意记得添加github ssh key`

  ```bash
  $ hexo clean
  $ hexo generate
  $ hexo deploy
  ```

  More info: [Deployment](https://hexo.io/docs/deployment.html)

### 自定义域名

1.  在万网的域名解析中把 `52bg.org` 解析到 `192.30.252.154`
2. 在 `Hexo` 博客的 `source` 目录下创建文件 `CNAME`，其内容为 **`52bg.org`**
3.  发布到`github` ，就可以用`52bg.org` 访问博客了

### 主页显示摘要

在 md 中，摘要内容的后面跟上 `<!--more-->`，否则主页会显示文章的全部内容