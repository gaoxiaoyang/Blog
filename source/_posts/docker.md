---
title: Docker
date: 2019-11-29 15:30:00
tags: Docker
---
## docker 参考资料

[Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## 安装 docker

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo service docker start
sudo usermod -aG docker $USER
newgrp docker # 添加到启动指令中
docker image pull ubuntu:16.04
```
<!--more-->

## 安装 ctop

[ctop](https://github.com/bcicen/ctop) 可以监听 docker 容器的计算机资源占用信息。

```bash
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.2/ctop-0.7.2-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop
```

## 安装 CUDA 支持

[启用Docker虚拟机GPU，加速深度学习](https://cloud.tencent.com/developer/article/1453411)

### 建立系统服务

建立目录`sudo mkdir /usr/lib/systemd/system`，新增文件`/usr/lib/systemd/system/nvidia-persistenced.service`：

```ini
[Unit]
Description=NVIDIA Persistence Daemon
Wants=syslog.target[Service]
Type=forking
PIDFile=/var/run/nvidia-persistenced/nvidia-persistenced.pid
Restart=always
ExecStart=/usr/bin/nvidia-persistenced --verbose
ExecStopPost=/bin/rm -rf /var/run/nvidia-persistenced[Install]
WantedBy=multi-user.target
```

启用服务：`sudo systemctl enable nvidia-persistenced`

### 禁用 UDEV 规则中的 memory 子系统规则

修改`/udev/rules.d/40-vm-hotadd.rules`，注释如下语句：

```
# SUBSYSTEM=="memory", ACTION=="add", DEVPATH=="/devices/system/memory/memory[0-9]*", TEST=="state", ATTR{state}="online"
```

### 安装 Nvidia Docker

``` bash
# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd
```

### 下载 Docker Image

```bash
docker image pull nvidia/cuda
docker image pull tensorflow/tensorflow:latest-gpu
```

### 建立容器

```bash
docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
```
## docker image 指令

## 拉取 image 文件

```bash
docker image pull group/image[:tag]
```
镜像列表详见官方 [Docker Hub](https://hub.docker.com/)。

## 列出本机的所有 image 文件
```bash
docker image ls
```
## 删除 image 文件

```bash
docker image rm [imageName]
```

## 提交镜像修改

```bash
docker commit -m "message" -a "user" containerID group/image:tag
```

可自定义新的仓库名和镜像名。

## 保存镜像到文件

```bash
docker save -o FILE IMAGE
```

## 从文件载入镜像

```bash
docker load --input FILE
```

## docker container 指令

### 新建容器实例

```bash
docker container run [--name containerName] [-p container:host] [-idt] [image][:tag] [cmd]
```

该指令若在本地未找到对应的 image，则会从服务器自动拉取。

- `--name` 指定容器名称
- `-p` 将容器的端口映射到本机端口。
- `-i` 保持容器`STDIN`开启，即使并未连接至容器。
- `-d` 令容器的`cmd`在后台运行。
- `-t` 将当前终端连接至容器。
- `-it` 容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
- `cmd` 容器启动后默认执行的程序，一般为 Shell，如`/bin/bash`。

docker容器的`run`指令的`cmd`进程必须是前台程序，或者持续挂起（如`top`），否则运行完毕后会容器会自动关闭，使用`-id`将非挂起进程置于后台持续监听`STDIN`，可以避免容器自动关闭。

## 列出本机正在运行的容器

``` bash
docker container ls [-a]
```

- `-a` 显示所有容器，包括已关闭的

## 列出本机所有容器，包括终止运行的容器
```bash
docker container ls --all
```

## 停止指定的容器运行

```bash
docker container kill containerID
```

## 删除指定的容器文件

```bash
docker container rm containerID
```

## 启动现存容器

```bash
docker container start containerID
```

## 停止现存容器

```bash
docker container stop containerID
```

## 查看容器 shell 标准输出

```bash
docker container logs containerID
```

## 进入正在运行的容器

``` bash
docker container exec -it containerID /bin/bash
```

## 从正在运行的容器中拷贝文件到本机当前目录

```bash
docker container cp containID:/path/to/file localPath
```

# 构建镜像
## 参考资料

[Docker Documentation - Dockerfile Reference]( https://docs.docker.com/engine/reference/builder/ )

## 编写 Dockerfile

在项目根目录下，建立` .dockerignore `文件，记录不打包到镜像中的内容：

```
.git
```
在项目根目录下，建立`Dockerfile`文件：

```dockerfile
FROM <image>[:<tag>] [AS <name>]
WORKDIR /path/to/workdir
SHELL ["executable", "parameters"]
COPY [--chown=<user>:<group>] <src>... <dest>
RUN <command>
EXPOSE <port> [<port>/<protocol>...]
ENV <key> <value>
ADD [--chown=<user>:<group>] <src>... <dest>
ENTRYPOINT ["executable", "param1", "param2"]
CMD command param1 param2
LABEL <key>=<value> <key>=<value> <key>=<value> ...
VOLUME ["/data"]
```

- `FROM` 使用的父镜像源。
- `WORKDIR` 镜像构建过程中的工作路径。
- `SHELL` 指定运行`RUN`、`CMD` 和`ENTRYPOINT`的 shell指令，如`SHELL ["/bin/bash", "-c"]`。Linux 中默认为`/bin/sh -c`，Windows 中默认为`cmd /S /C`。
- `COPY` 将文件或目录复制到镜像中的目的地址，目的路径会自动创建，只可使用项目目录下的内容。
  - `COPY [--chown=<user>:<group>] <src>... <dest>` 默认格式。
  - `COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]` 若路径中包含空格。
- `RUN` 镜像构建过程中执行的指令，可编写多条顺序执行。每条指令会在新的容器层执行，可使用`;`在同一层容器中执行多条指令，并可使用`\`分行。
- ` RUN <command> ` shell 模式，使用`SHELL`运行。
  - `RUN ["executable", "param1", "param2"]` exec 模式，使用 json 格式，不会启动 shell。
- `EXPOSE` 告知该容器可能暴露的端口，协议可使用`tcp`或`udp`，默认为`tcp`。仅作提示使用，实际启动容器时需通过`-p`指令转发端口。
- `ENV`设置容器的环境变量，带空格的值可用引号包含，或使用`\`转义。

  - `ENV <key> <value>`

  - `ENV <key>=<value> ...
- `ADD` 将文件或目录复制到镜像中的目的地址，目的路径会自动创建，也可编写多条`ADD`顺序执行，只可使用项目目录下的内容。
- `ADD [--chown=<user>:<group>] <src>... <dest>` 默认格式。
  - `ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]` 若路径中包含空格。
- `ENTRYPOINT` 让镜像模拟为一个可执行程序进行启动。
  - `ENTRYPOINT ["executable", "param1", "param2"]` exec 模式，使用 json 格式，为默认使用模式，该模式下，会将`CMD`作为运行参数进行追加。如果用户启动容器时在`docker run <image>`之后追加参数，则会覆盖`CMD`参数。
  - `ENTRYPOINT command param1 param2` shell 模式，使用`SHELL`执行，但此方式启动的程序不包含`PID 1`，无法接收 Unix 信号，如在执行`docker stop <container>`时，用户程序不会接收到`SIGTERM`信号。
- `CMD ` 镜像启动后执行的指令，可编写多条顺序执行。
- `CMD ["executable","param1","param2"]` 推荐使用，exec 模式，使用 json 格式，不会启动 shell。
  - `CMD ["param1","param2"]` 使用 json 格式，会作为初始化参数追加到`ENTRYPOINT`之后。
  - `CMD command param1 param2` shell 格式。
- `LABEL` 容器元信息，多条标签可用`\`换行，也可使用多条`LABEL`指令，遵循 json 格式。
- `VOLUME` 创建一个或多个挂载点，在容器创建时自动将其挂载至创建者（原生系统或另一容器），挂载点路径需已存在。

## 执行 docker image build

`Dockerfile`中的指令会被顺序执行。

# 容器内相关
## 查看容器中的系统版本
```bash
cat /etc/issue
```

##  Docker 执行 GUI 程序

[x11docker](https://github.com/mviereck/x11docker)
[x11docker：让你的Docker跑图形程序](https://zhuanlan.zhihu.com/p/60073751)