---
title: Docker入门（二）——容器
date: 2019-11-28 13:30:20
tags:
    - Docker
categories:
    - Docker
---

# Docker容器简介

容器是 Docker 是一核心概念。

简单的说，容器是独立运行的一个或一组应用，以及它们的运行态环境。对应的，虚拟机可以理解为模拟运行的一整套操作系统（提供了运行态环境和其他系统环境）和跑在上面的应用。

<!-- more -->

接下来看看容器的操作使用。

# 容器操作

## 启动容器

启动容器有两种方式，一种是基于镜像新建一个容器并启动，另外一个是将在终止状态（`stopped`）的容器重新启动。

### 新建并启动

所需要的命令主要为 `docker run`。

例如，下面的命令输出一个 “Hello World”，之后终止容器。

``` bash
$ docker run ubuntu:18.04 /bin/echo 'Hello world'
Unable to find image 'ubuntu:18.04' locally
18.04: Pulling from library/ubuntu
7ddbc47eeb70: Pull complete 
c1bbdc448b72: Pull complete 
8c3b70e39044: Pull complete 
45d437916d57: Pull complete 
Digest: sha256:6e9f67fa63b0323e9a1e587fd71c561ba48a034504fb804fd26fd8800039835d
Status: Downloaded newer image for ubuntu:18.04
Hello world
```

这跟在本地直接执行 `/bin/echo 'hello world'` 几乎感觉不出任何区别。

### 启动交互式终端

我们通过 docker 的两个参数 -i -t，让 docker 运行的容器实现**"对话"**的能力：

``` bash
$ docker run -t -i ubuntu:18.04 /bin/bash
root@349a1edaa615:/# 
```

各个参数解析：

- **-t:** 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
- **-i:** 则让容器的标准输入保持打开。
- **ubuntu:18.04**: ubuntu 镜像。
- **/bin/bash**：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。

在交互模式下，用户可以通过所创建的终端来输入命令，例如

``` bash
root@349a1edaa615:/# pwd
/
root@349a1edaa615:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@349a1edaa615:/# exit
exit
```

> exit退出交互式 Shell

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

### 启动已终止容器

可以利用 `docker container start` 命令，直接将一个已经终止的容器启动运行。

容器的核心为所执行的应用程序，所需要的资源都是应用程序运行所必需的。除此之外，并没有其它的资源。可以在伪终端中利用 `ps` 或 `top` 来查看进程信息。

``` bash
root@181e9d1236d5:/# ps
  PID TTY          TIME CMD
    1 pts/0    00:00:00 bash
   11 pts/0    00:00:00 ps
```

## 守护态运行容器

如果需要让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。此时，可以通过添加 `-d` 参数来实现。

``` bash
$ docker run -d -i -t ubuntu:18.04 /bin/sh
edae8d9d6311b344b3971bafc2a698625242bcf3300e2c850ba1e4ef51d88707
```

此时容器启动后会进入后台。如果想要进入容器可以使用`docker exec`命令，后面会逐渐讲解到。

>  **注：**容器是否会长久运行，是和 `docker run` 指定的命令有关，和 `-d` 参数无关。

使用 `-d` 参数启动后会返回一个唯一的 id，也可以通过 `docker container ls` 命令来查看容器信息。

``` bash
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
edae8d9d6311        ubuntu:18.04        "/bin/sh"           4 minutes ago       Up 4 minutes                            suspicious_pike
```

> 要获取容器的输出信息，可以通过 `docker container logs` 命令。如`docker container logs [container ID or NAMES]`

## 	终止容器

可以使用 `docker container stop` 来终止一个运行中的容器。

此外，当 Docker 容器中指定的应用终结时，容器也自动终止。

例如对于上一章节中只启动了一个终端的容器，用户通过 `exit` 命令或 `Ctrl+d` 来退出终端时，所创建的容器立刻终止。

终止状态的容器可以用 `docker container ls -a` 命令看到。例如

``` bash
$ docker container stop edae
edae
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
edae8d9d6311        ubuntu:18.04        "/bin/sh"                7 minutes ago       Exited (137) 36 seconds ago                       suspicious_pike
181e9d1236d5        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                             condescending_feistel
349a1edaa615        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                             suspicious_lehmann
7d6482342da5        ubuntu:18.04        "/bin/echo 'Hello wo…"   2 days ago          Exited (0) 2 days ago                             affectionate_knuth
```

处于终止状态的容器，可以通过 `docker container start` 命令来重新启动。

此外，`docker container restart` 命令会将一个运行态的容器终止，然后再重新启动它。

> 指定容器ID时可以不需要输入完整ID，可以输入ID前几位都可以，只要能够辨识到该容器即可。

## 进入容器

在使用 `-d` 参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，包括使用 `docker attach` 命令或 `docker exec` 命令，推荐大家使用 `docker exec` 命令。

### `attach`命令

``` bash
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
edae8d9d6311        ubuntu:18.04        "/bin/sh"           9 minutes ago       Up 4 seconds                            suspicious_pike
$ docker attach 243c
# pwd
/
# exit
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

*注意：* 如果从这个 stdin 中 exit，会导致容器的停止。

### `exec `命令

`docker exec` 后边可以跟多个参数，这里主要说明 `-i` `-t` 参数。

只用 `-i` 参数时，由于没有分配伪终端，界面没有我们熟悉的 Linux 命令提示符，但命令执行结果仍然可以返回。

当 `-i` `-t` 参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。

``` bash
$ container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
edae8d9d6311        ubuntu:18.04        "/bin/sh"           13 minutes ago      Up 5 seconds                            suspicious_pike
$ docker exec -i edae bash
ls
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
va
$ docker exec -i -t edae bash
root@edae8d9d6311:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@edae8d9d6311:/# exit
exit
```

如果从这个 stdin 中 exit，不会导致容器的停止。这就是为什么推荐大家使用 `docker exec` 的原因。

更多参数说明请使用 `docker exec --help` 查看。

## 导出和导入容器

### 导出容器

如果要导出本地某个容器，可以使用 `docker export` 命令。

``` bash
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                            PORTS               NAMES
edae8d9d6311        ubuntu:18.04        "/bin/sh"                18 minutes ago      Exited (137) About a minute ago                       suspicious_pike
181e9d1236d5        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                                 condescending_feistel
349a1edaa615        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                                 suspicious_lehmann
7d6482342da5        ubuntu:18.04        "/bin/echo 'Hello wo…"   2 days ago          Exited (0) 2 days ago                                 affectionate_knuth
$ docker export edae > ubuntu.zip
```

### 导入容器快照

可以使用 `docker import` 从容器快照文件中再导入为镜像，例如

``` bash
$ cat ubuntu.zip | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```

此外，也可以通过指定 URL 或者某个目录来导入，例如

``` bash
$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

> *注：用户既可以使用 `docker load` 来导入镜像存储文件到本地镜像库，也可以使用 `docker import` 来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。*

## 删除容器

可以使用 `docker container rm` 来删除一个处于终止状态的容器。例如

```bash
$ docker container rm  edae
edae
```

如果要删除一个运行中的容器，可以添加 `-f` 参数。Docker 会发送 `SIGKILL` 信号给容器。

**清理所有处于终止状态的容器**

用 `docker container ls -a` 命令可以查看所有已经创建的包括终止状态的容器，如果数量太多要一个个删除可能会很麻烦，用命令`docker container prune`可以清理掉所有处于终止状态的容器。

``` bash
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS               NAMES
181e9d1236d5        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                       condescending_feistel
349a1edaa615        ubuntu:18.04        "/bin/bash"              2 days ago          Exited (0) 2 days ago                       suspicious_lehmann
7d6482342da5        ubuntu:18.04        "/bin/echo 'Hello wo…"   2 days ago          Exited (0) 2 days ago                       affectionate_knuth
$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
181e9d1236d54ee898f8f0ebc5349dbcc0cd2368c34f427f7963c9014c9e1dc8
349a1edaa61597acb426a0961059bf5a73ae0b23350774d5cd5c33963c8b2b12
7d6482342da5f695a5351c15ef17acb521f893548db0891154b5c1268227ce7d

Total reclaimed space: 20B
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

# 数据管理

在容器中管理数据主要有两种方式：

- 数据卷（Volumes）
- 挂载主机目录 (Bind mounts)

## 数据卷

`数据卷` 是一个可供一个或多个容器使用的特殊目录，它绕过 UFS，可以提供很多有用的特性：

- `数据卷` 可以在容器之间共享和重用
- 对 `数据卷` 的修改会立马生效
- 对 `数据卷` 的更新，不会影响镜像
- `数据卷` 默认会一直存在，即使容器被删除

> 注意：`数据卷` 的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会隐藏掉，能显示看的是挂载的 `数据卷`。

### 创建数据卷

``` bash
$ docker volume create my-vol
my-vol
```

查看所有的 `数据卷`

``` bash
$ docker volume ls
DRIVER              VOLUME NAME
local               my-vol
```

在主机里使用以下命令可以查看指定 `数据卷` 的信息

``` bash
$ docker volume inspect my-vol
[
    {
        "CreatedAt": "2019-12-02T03:25:01Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

### 启动一个挂载数据卷的容器

在用 `docker run` 命令的时候，使用 `--mount` 标记来将 `数据卷` 挂载到容器里。在一次 `docker run` 中可以挂载多个 `数据卷`。

下面创建一个名为 `web` 的容器，并加载一个 `数据卷` 到容器的 `/webapp` 目录。

``` bash
$ docker run -d -P \
    --name web \
    # -v my-vol:/wepapp \
    --mount source=my-vol,target=/webapp \
    training/webapp \
    python app.py
```

> **-P:** 随机端口映射，容器内部端口**随机**映射到主机的高端口

### 查看数据卷的具体信息

在主机里使用以下命令可以查看 `web` 容器的信息

```bash
$ docker inspect web
```

`数据卷` 信息在 "Mounts" Key 下面

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "my-vol",
        "Source": "/var/lib/docker/volumes/my-vol/_data",
        "Destination": "/app",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

### 删除数据卷

```bash
$ docker volume rm my-vol
```

`数据卷` 是被设计用来持久化数据的，它的生命周期独立于容器，Docker 不会在容器被删除后自动删除 `数据卷`，并且也不存在垃圾回收这样的机制来处理没有任何容器引用的 `数据卷`。如果需要在删除容器的同时移除数据卷。可以在删除容器的时候使用 `docker rm -v` 这个命令。

无主的数据卷可能会占据很多空间，要清理请使用以下命令

```bash
$ docker volume prune
```

## 挂载主机目录

### 挂载一个主机目录作为数据卷

使用 `--mount` 标记可以指定挂载一个本地主机的目录到容器中去。

```bash
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/opt/webapp \
    --mount type=bind,source=/src/webapp,target=/opt/webapp \
    training/webapp \
    python app.py
```

上面的命令加载主机的 `/src/webapp` 目录到容器的 `/opt/webapp`目录。这个功能在进行测试的时候十分方便，比如用户可以放置一些程序到本地目录中，来查看容器是否正常工作。本地目录的路径必须是绝对路径，以前使用 `-v` 参数时如果本地目录不存在 Docker 会自动为你创建一个文件夹，现在使用 `--mount` 参数时如果本地目录不存在，Docker 会报错。

Docker 挂载主机目录的默认权限是 `读写`，用户也可以通过增加 `readonly` 指定为 `只读`。

```bash
$ docker run -d -P \
    --name web \
    # -v /src/webapp:/opt/webapp:ro \
    --mount type=bind,source=/src/webapp,target=/opt/webapp,readonly \
    training/webapp \
    python app.py
```

加了 `readonly` 之后，就挂载为 `只读` 了。如果你在容器内 `/opt/webapp` 目录新建文件，会显示如下错误

```bash
/opt/webapp # touch new.txt
touch: new.txt: Read-only file system
```

### 查看数据卷的具体信息

在主机里使用以下命令可以查看 `web` 容器的信息

```bash
$ docker inspect web
```

`挂载主机目录` 的配置信息在 "Mounts" Key 下面

```json
"Mounts": [
    {
        "Type": "bind",
        "Source": "/src/webapp",
        "Destination": "/opt/webapp",
        "Mode": "",
        "RW": true,
        "Propagation": "rprivate"
    }
],
```

### 挂载一个本地主机文件作为数据卷

`--mount` 标记也可以从主机挂载单个文件到容器中

```bash
$ docker run --rm -it \
   # -v $HOME/.bash_history:/root/.bash_history \
   --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
   ubuntu:18.04 \
   bash

root@2affd44b4667:/# history
1  ls
2  diskutil list
```

这样就可以记录在容器输入过的命令了。

## 选择 -v or –mount 标志

最初，`-v`或`--volume`标志用于独立容器，而`--mount`标志用于群集服务。但是，从Docker 17.06开始，您也可以使用`--mount`独立的容器。一般来说，`--mount`更明确和详细。最大的区别在于-v语法将所有选项组合在一个字段中，而`--mount`语法将它们分开。建议新学者使用。

**`v`或`- volume`:由三个字段组成，由冒号分隔(:)。字段必须按照正确的顺序书写，并且每个字段的含义都不是立刻确定。**

- 在命名卷的情况下，第一个字段是卷的名称，在给定的主机上是惟一的。对于匿名卷，省略了第一个字段。
- 第二个字段是在容器中安装文件或目录的路径。
- 第三个字段是可选的，并且是一个逗号分隔的选项列表，如ro。下面讨论这些选项。

**`--mount`:由多个键-值对组成，由逗号分隔，每一对由`< key>= <value> tuple(元组）`组成。`--mount` 语法比`- v`或`—volume` 更详细，其中键对的顺序并不重要，而且标记的值更容易理解。**

- 挂载的类型(type)，可以是绑定(bind)、卷(volume)或tmpfs。本主题讨论卷，因此类型将始终是卷。
- 挂载源（source)。对于命名卷，是卷的名称。对于匿名卷，该字段被省略。可以指定为source 或src。
- 挂在目标(destination)的值是将文件或目录安装在容器中的路径。可以指定为destination、dst或target。
- 如果存在readonly选项，则将绑定挂载安装到容器中作为只读。
- 可以使用键值对多次指定的volume-opt选项.

与绑定挂载相反，所有的卷的选项对于` --mount` 和` -v `标志 都可以使用。当卷(volume)作为服务时，只支持`--mount`。

# 容器网络

Docker 允许通过外部访问容器或容器互联的方式来提供网络服务。

## 外部访问容器

容器中可以运行一些网络应用，要让外部也可以访问这些应用，可以通过 `-P` 或 `-p` 参数来指定端口映射。

当使用 `-P` 标记时，Docker 会随机映射一个 `49000~49900` 的端口到内部容器开放的网络端口。

使用 `docker container ls` 可以看到，本地主机的 49155 被映射到了容器的 5000 端口。此时访问本机的 49155 端口即可访问容器内 web 应用提供的界面。

``` bash
$ docker run -d -P training/webapp python app.py
ea7fb3aa1ca874847d7469f62f7cdfcd557438453ad08c283416b5d229468db2
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
ea7fb3aa1ca8        training/webapp     "python app.py"     About an hour ago   Up About an hour    0.0.0.0:32768->5000/tcp   serene_buck
```

同样的，可以通过 `docker logs` 命令来查看应用的信息。

```bash
$ docker logs -f serene_buck
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
172.17.0.1 - - [02/Dec/2019 06:27:22] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [02/Dec/2019 06:27:23] "GET /favicon.ico HTTP/1.1" 404 -
```

`-p` 则可以指定要映射的端口，并且，在一个指定端口上只可以绑定一个容器。支持的格式有 `ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort`。

### 映射所有接口地址

使用 `hostPort:containerPort` 格式本地的 8000 端口映射到容器的 5000 端口，可以执行

``` bash
$ docker run -d -p 8000:5000 training/webapp python app.py
```

此时默认会绑定本地所有接口上的所有地址。

### 映射到指定地址的指定端口

可以使用 `ip:hostPort:containerPort` 格式指定映射使用一个特定地址，比如 localhost 地址 127.0.0.1

```bash
$ docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
```

### 映射到指定地址的任意端口

使用 `ip::containerPort` 绑定 localhost 的任意端口到容器的 5000 端口，本地主机会自动分配一个端口。

```bash
$ docker run -d -p 127.0.0.1::5000 training/webapp python app.py
```

还可以使用 `udp` 标记来指定 `udp` 端口

```bash
$ docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
```

### 查看映射端口配置

使用 `docker port` 来查看当前映射的端口配置，也可以查看到绑定的地址

``` bash
$ docker port 5f2b 5000
0.0.0.0:8000
```

> 注意：
>
> - 容器有自己的内部网络和 ip 地址（使用 `docker inspect` 可以获取所有的变量，Docker 还可以有一个可变的网络配置。）
> - `-p` 标记可以多次使用来绑定多个端口
>
> 例如

```bash
$ docker run -d \
    -p 5000:5000 \
    -p 3000:80 \
    training/webapp \
    python app.py
```

## 容器互联

### 新建网络

下面先创建一个新的 Docker 网络。

```bash
$ docker network create -d bridge my-net
cea6b6e901d97a319c537895587df6a1256ccc18fc1e6faccfa911ec843e2e64
```

`-d` 参数指定 Docker 网络类型，有 `bridge` `overlay`。其中 `overlay` 网络类型用于 Swarm mode，属于高级用法了。入门章节则不介绍了。

### 连接容器

运行一个容器并连接到新建的 `my-net` 网络

```bash
$ docker run -i -t --rm --name busybox1 --network my-net busybox sh
```

打开新的终端，再运行一个容器并加入到 `my-net` 网络

```bash
$ docker run -i -t --rm --name busybox2 --network my-net busybox sh
```

> Clean up (--rm) 指在容器运行完之后自动清除，*注意：--rm 和 -d不能共用！*

再打开一个新的终端查看容器信息。

``` bash
$ docker container ls       
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
4f37d2e62181        busybox             "sh"                48 seconds ago      Up 47 seconds                           busybox2
98b27738599c        busybox             "sh"                2 minutes ago       Up About a minute                       busybox1
```

下面通过 `ping` 来证明 `busybox1` 容器和 `busybox2` 容器建立了互联关系。

在 `busybox1` 容器输入以下命令

``` bash
/ # ping busybox2
PING busybox2 (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.115 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.138 ms
64 bytes from 172.18.0.3: seq=2 ttl=64 time=0.139 ms
64 bytes from 172.18.0.3: seq=3 ttl=64 time=0.137 ms
64 bytes from 172.18.0.3: seq=4 ttl=64 time=0.138 ms
64 bytes from 172.18.0.3: seq=5 ttl=64 time=0.138 ms
64 bytes from 172.18.0.3: seq=6 ttl=64 time=0.133 ms
```

用 ping 来测试连接 `busybox2` 容器，它会解析成 `172.18.0.3`。

同理在 `busybox2` 容器执行 `ping busybox1`，也会成功连接到。

``` bash
/ # ping busybox1
PING busybox1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.091 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.137 ms
```

这样，`busybox1` 容器和 `busybox2` 容器建立了互联关系。

> 如果有多个容器需要互相连接，推荐使用Docker Compose。

> `BusyBox` 是一个集成了一百多个最常用 Linux 命令和工具（如 `cat`、`echo`、`grep`、`mount`、`telnet` 等）的精简工具箱，它只需要几 MB 的大小，很方便进行各种快速验证，被誉为“Linux 系统的瑞士军刀”。
>
> `BusyBox` 可运行于多款 `POSIX` 环境的操作系统中，如 `Linux`（包括 `Android`）、`Hurd`、`FreeBSD` 等。

## 配置DNS

Docker 利用虚拟文件来挂载容器的 3 个相关配置文件。

在容器中使用 `mount` 命令可以看到挂载信息：

```bash
$ mount
/dev/disk/by-uuid/1fec...ebdf on /etc/hostname type ext4 ...
/dev/disk/by-uuid/1fec...ebdf on /etc/hosts type ext4 ...
tmpfs on /etc/resolv.conf type tmpfs ...
```

这种机制可以让宿主主机 DNS 信息发生更新后，所有 Docker 容器的 DNS 配置通过 `/etc/resolv.conf` 文件立刻得到更新。

配置全部容器的 DNS ，也可以在 `/etc/docker/daemon.json` 文件中增加以下内容来设置。

```json
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```

这样每次启动的容器 DNS 自动配置为 `114.114.114.114` 和 `8.8.8.8`。使用以下命令来证明其已经生效。

```bash
$ docker run -it --rm ubuntu:18.04  cat etc/resolv.conf
nameserver 114.114.114.114
nameserver 8.8.8.8
```

如果用户想要手动指定容器的配置，可以在使用 `docker run` 命令启动容器时加入如下参数：

> `-h HOSTNAME` 或者 `--hostname=HOSTNAME` 设定容器的主机名，它会被写到容器内的 `/etc/hostname` 和 `/etc/hosts`。但它在容器外部看不到，既不会在 `docker container ls` 中显示，也不会在其他的容器的 `/etc/hosts` 看到。
>
> `--dns=IP_ADDRESS` 添加 DNS 服务器到容器的 `/etc/resolv.conf` 中，让容器用这个服务器来解析所有不在 `/etc/hosts` 中的主机名。
>
> `--dns-search=DOMAIN` 设定容器的搜索域，当设定搜索域为 `.example.com` 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 `host.example.com`。
>
> 注意：如果在容器启动时没有指定最后两个参数，Docker 会默认用主机上的`/etc/resolv.conf` 来配置容器。

# 参考链接

Docker 官网：[https://www.docker.com](https://www.docker.com/)

Github Docker 源码：https://github.com/docker/docker-ce

Docker命令大全：https://www.runoob.com/docker/docker-command-manual.html

本文章摘抄于：https://yeasy.gitbooks.io/docker_practice/content/