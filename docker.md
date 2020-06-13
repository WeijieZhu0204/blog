# Docker



## 基本概念

**镜像**

docker镜像是一个特殊的文件系统，包含容器运行时所需的程序、库、资源等一系列文件，镜像不包含动态数据。

镜像采用分层存储的结构，因此一个镜像可以由多层文件系统组成。



**容器**

容器是镜像运行时的实体，其实质是进程，容器进程运行在一个隔离的环境中。

每一个容器运行时会创建一个当前容器的存储层，存储层的生命周期与容器一样，与容器同时被销毁。因此不应该向容器存储层写入数据。



**仓库**

一个仓库会包含同一个软件不同版本的镜像，标签用于指定镜像的不同版本。如：mysql:6.0、mysql:latest。



## 镜像操作

**拉取镜像**

```shell
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

**运行镜像**

```shell
docker run -it --rm nginx bash
```

**列出镜像**

```shell
docker image ls
```

**查看镜像、容器、数据卷所占空间**

```
docker system df
```

**删除镜像**

```
docker image rm [OPTIONS] IMAGE [IMAGE...]
```



## 容器操作


**新建并启动**

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

跟在镜像名后面的是 `command`，运行时会替换 `CMD` 的默认值。

当利用 `docker run` 来创建容器时，Docker 在后台运行的标准操作包括：

- 检查本地是否存在指定的镜像，不存在就从公有仓库下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止



**启动已终止容器**

```
docker container start [OPTIONS] CONTAINER [CONTAINER...]
```

**守护态运行**

```
docker run -d nginx
```

容器是否会长久运行，是和 `docker run` 指定的命令有关，和 `-d` 参数无关。

**获取容器的输出信息**

```
docker container logs [container ID or NAMES]
```

**列出容器**

```
docker container ls
```

**终止容器**

```
docker container stop [OPTIONS] CONTAINER [CONTAINER...]
```

终止状态的容器可以使用`docker container ls -a`命令查看。

**启动/重启容器**

```
docker container restart
docker container start
```

**进入容器**

```
docker exec -it CONTAINER bash
```

**删除容器**

```
docker container rm
```

如果要删除一个运行中的容器，可以添加 `-f` 参数。Docker 会发送 `SIGKILL` 信号给容器。

**清理所有处于终止状态的容器**

```
docker container prune
```



## 容器存储层

镜像是多层存储，每一层是在前一层的基础上进行的修改；而容器同样也是多层存储，是在以镜像为基础层，在其基础上加一层作为容器运行时的存储层。

**查看存储层修改**

```
docker diff CONTAINERNAME
```



当我们运行一个容器的时候（如果不使用卷的话），我们做的任何文件修改都会被记录于容器存储层里。而 Docker 提供了一个 `docker commit` 命令，可以将容器的存储层保存下来成为镜像。换句话说，就是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。以后我们运行这个新镜像的时候，就会拥有原有容器最后的文件变化。



**提交容器存储层修改**

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

提交之后会在原有的镜像层之上再加一层，并生成一个新的镜像。但是commit可能会加入大量无关的文件修改，导致镜像臃肿。另外也无法记录镜像的制作过程。因此实际操作中不会使用commit提交镜像。



## 数据卷

数据卷是一个可供一个或多个容器使用的特殊目录，它绕过 UFS，可以提供很多有用的特性：

- `数据卷` 可以在容器之间共享和重用
- 对 `数据卷` 的修改会立马生效
- 对 `数据卷` 的更新，不会影响镜像
- `数据卷` 默认会一直存在，即使容器被删除

> 注意：`数据卷` 的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会隐藏掉，能显示看的是挂载的 `数据卷`。



**创建数据卷**

```
docker volume create my-vol
```

**查看数据卷**

```
docker volume ls
```

**启动一个挂载数据卷的容器**

```
docker run -d -P
    --name web
    --mount source=my-vol,target=/webapp
    webapp
```

加载一个 `my-vol数据卷` 到容器的 `/webapp` 目录

**查看容器的数据卷信息**

```
docker inspect CONTAINER
```

**删除数据卷**

```
docker volume rm my-vol
```

**清理无主数据卷**

```
docker volume prune
```

**挂载主机目录作为数据卷**

```
$ docker run -d -P
    --name web
    --mount type=bind,source=/src/webapp,target=/opt/webapp \
		webapp
```

加载主机的 `/src/webapp` 目录到容器的 `/opt/webapp`目录。



## 容器网络

**自动映射端口**

```
docker run -d -P nginx

# 0.0.0.0:32768->80/tcp
```

当使用 `-P` 标记时，Docker 会随机映射一个 `49000~49900` 的端口到内部容器开放的网络端口。

**查看映射端口**

```
docker port CONTAINERNAME
```

**指定映射端口**

```
docker run -d -p 5000:80 nginx
```

使用 `hostPort:containerPort` 格式本地的 5000 端口映射到容器的 80 端口。



## dockerfile

**FROM**

指定以某个镜像为基础。

**RUN**

执行命令，每一行命令新增一个镜像层。

**COPY**

```
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

从构建上下文目录中 `<源路径>` 的文件/目录复制到新的一层的镜像内的 `<目标路径>` 位置。

`<源路径>` 可以是多个，甚至可以是通配符，其通配符规则要满足 Go 的 [`filepath.Match`](https://golang.org/pkg/path/filepath/#Match) 规则。

**ADD**

```
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

`<源路径>` 可以是一个 `URL`，这种情况下，Docker 引擎会试图去下载这个链接的文件放到 `<目标路径>` 去。

如果 `<源路径>` 为一个 `tar` 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，`ADD` 指令将会自动解压缩这个压缩文件到 `<目标路径>` 去。

**CMD**

```shell
CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
```

Docker 不是虚拟机，容器就是进程。既然是进程，那么在启动容器的时候，需要指定所运行的程序及参数。`CMD` 指令就是用于指定默认的容器主进程的启动命令的。

提到 `CMD` 就不得不提容器中应用在前台执行和后台执行的问题。这是初学者常出现的一个混淆。

Docker 不是虚拟机，容器中的应用都应该以前台执行，而不是像虚拟机、物理机里面那样，用 `systemd` 去启动后台服务，容器内没有后台服务的概念。

对于容器而言，其启动程序就是容器应用进程，容器就是为了主进程而存在的，主进程退出，容器就失去了存在的意义，从而退出，其它辅助进程不是它需要关心的东西。

**ENV**

```
ENV <key> <value>
ENV <key>=<value> ...
```

定义环境变量

**ARG**

```
ARG <name>[=<default value>]
```

构建参数和 `ENV` 的效果一样，都是设置环境变量。所不同的是，`ARG` 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的。

`Dockerfile` 中的 `ARG` 指令是定义参数名称，以及定义其默认值。该默认值可以在构建命令 `docker build` 中用 `--build-arg <参数名>=<值>` 来覆盖。

**VOLUME**

```
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```

为了防止运行时用户忘记将动态文件所保存目录挂载为卷，在 `Dockerfile` 中，我们可以事先指定某些目录挂载为匿名卷，这样在运行时如果用户不指定挂载，其应用也可以正常运行，不会向容器存储层写入大量数据。

**EXPOSE**

```
EXPOSE <端口1> [<端口2>...]
```

`EXPOSE` 指令是声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。

**WORKDIR**

```
WORKDIR <工作目录路径>
```

使用 `WORKDIR` 指令可以来指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录，如该目录不存在，`WORKDIR` 会帮你建立目录。

**USER**

```
USER <用户名>[:<用户组>]
```

帮助你切换到指定用户，这个用户必须是事先建立好的，否则无法切换。



## 构建镜像

```
docker build -t name:tag [构建上下文路径]
```

构建上下文路径：当构建的时候，用户会指定构建镜像上下文的路径，`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。`COPY`等命令也是基于该上下文路径进行寻址。



**多阶段构建**

当dockerfile出现多个`FROM`指令的之后，前面的指令都会在构建时被清理掉，减少镜像大小。

**指定构建某一阶段**

```
FROM golang:1.9-alpine as builder
```

通过`as`为某一阶段命名，执行构建的时候通过`target`参数，指定需要构建的阶段。

```
docker build --target builder ....
```

**从其他阶段拷贝文件**

```
COPY --from=builder /etc/nginx/nginx.conf /nginx.conf
```

