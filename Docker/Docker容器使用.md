## Docker 客户端

docker 客户端非常简单 ,我们可以直接输入 docker 命令来查看到 Docker 客户端的所有命令选项。

```bash
[root@localhost ~]# docker

Usage:	docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/root/.docker")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/root/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/root/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/root/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  builder     Manage builds
  config      Manage Docker configs
  container   Manage containers
  engine      Manage the docker engine
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  deploy      Deploy a new stack or update an existing stack
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.
```

可以通过命令 **docker command --help** 更深入的了解指定的 Docker 命令使用方法。

例如我们要查看 **docker stats** 指令的具体使用方法：

```bash
[root@localhost ~]# docker stats --help

Usage:	docker stats [OPTIONS] [CONTAINER...]

Display a live stream of container(s) resource usage statistics

Options:
  -a, --all             Show all containers (default shows just running)
      --format string   Pretty-print images using a Go template
      --no-stream       Disable streaming stats and only pull the first result
      --no-trunc        Do not truncate output
```

## 运行一个web应用

前面我们运行的容器并没有一些什么特别的用处。

接下来让我们尝试使用 docker 构建一个 web 应用程序。

我们将在docker容器中运行一个 Python Flask 应用来运行一个web应用。

```bash
[root@localhost ~]# docker pull training/webapp

[root@localhost ~]# docker run -d -P training/webapp python app.py
f36ed296fb380a27ec65d95b9ee844968daad3eb9c725dc23dbf332957557aeb
```

参数说明:

- **-d:**让容器在后台运行。
- **-P:**将容器内部使用的网络端口映射到我们使用的主机上。

## 查看 WEB 应用容器

使用 docker ps 来查看我们正在运行的容器：

```bash
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS                     NAMES
f36ed296fb38        training/webapp     "python app.py"     About a minute ago   Up About a minute   0.0.0.0:32768->5000/tcp   youthful_easley
```

这里多了端口信息。

```
0.0.0.0:32768->5000/tcp 
```

Docker 开放了 5000 端口（默认 Python Flask 端口）映射到主机端口 32769 上。

这时我们可以通过浏览器访问WEB应用

我们也可以通过 -p 参数来设置不一样的端口：

```bash
[root@localhost ~]# docker run -d -p 5000:5000 training/webapp python app.py
```

**docker ps**查看正在运行的容器

```bash
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
f06290586667        training/webapp     "python app.py"     2 minutes ago       Up 2 minutes        0.0.0.0:5000->5000/tcp    youthful_chandrasekhar
f36ed296fb38        training/webapp     "python app.py"     20 minutes ago      Up 20 minutes       0.0.0.0:32768->5000/tcp   youthful_easley

```

容器内部的 5000 端口映射到我们本地主机的 5000 端口上。

## 网络端口的快捷方式

通过 **docker ps** 命令可以查看到容器的端口映射，**docker** 还提供了另一个快捷方式 **docker port**，使用 **docker port** 可以查看指定 （ID 或者名字）容器的某个确定端口映射到宿主机的端口号。

```bash
[root@localhost ~]# docker port f36ed296fb38
5000/tcp -> 0.0.0.0:32768
[root@localhost ~]# docker port youthful_easley
5000/tcp -> 0.0.0.0:32768
```

## 查看 WEB 应用程序日志

docker logs [ID或者名字] 可以查看容器内部的标准输出。

```bash
[root@localhost ~]# docker logs -f youthful_easley
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
192.168.1.69 - - [27/Apr/2019 12:56:27] "GET / HTTP/1.1" 200 -
192.168.1.69 - - [27/Apr/2019 12:56:27] "GET /favicon.ico HTTP/1.1" 404 -
```

## 查看WEB应用程序容器的进程

可以使用 docker top 来查看容器内部运行的进程

```bash
[root@localhost ~]# docker top youthful_easley
UID   PID   PPID    C  STIME  TTY  TIME      CMD
root  16706 16688   0  08:27  ?    00:00:00  python app.py
```

## 检查 WEB 应用程序

使用 **docker inspect** 来查看 Docker 的底层信息。它会返回一个 JSON 文件记录着 Docker 容器的配置和状态信息。

```bash
[root@localhost ~]# docker inspect youthful_easley
[
    {
        "Id": "f36ed296fb380a27ec65d95b9ee844968daad3eb9c725dc23dbf332957557aeb",
        "Created": "2019-04-27T12:27:27.105995804Z",
        "Path": "python",
        "Args": [
            "app.py"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 16706,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2019-04-27T12:27:27.787808304Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:6fae60ef344644649a39240b94d73b8ba9c67f898ede85cf8e947a887b3e6557",
        "ResolvConfPath": "/var/lib/docker/containers/f36ed296fb380a27ec65d95b9ee844968daad3eb9c725dc23dbf332957557aeb/resolv.conf",
        ……
```

## 停止 WEB 应用容器

```bash
[root@localhost ~]# docker stop f06290586667
f06290586667
```

## 重启WEB应用容器

已经停止的容器，我们可以使用命令 docker start 来启动。

```
[root@localhost ~]# docker start youthful_chandrasekhar
youthful_chandrasekhar
```

docker ps -l 查询最后一次创建的容器：

```bash
[root@localhost ~]# docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
f06290586667        training/webapp     "python app.py"     29 minutes ago      Up 32 seconds       0.0.0.0:5000->5000/tcp   youthful_chandrasekhar

```

正在运行的容器，我们可以使用 docker restart 命令来重启

## 移除WEB应用容器

我们可以使用 docker rm 命令来删除不需要的容器

```bash
runoob@runoob:~$ docker rm wizardly_chandrasekhar  
wizardly_chandrasekhar
```

删除容器时，容器必须是停止状态，否则会报如下错误

```bash
runoob@runoob:~$ docker rm wizardly_chandrasekhar
Error response from daemon: You cannot remove a running container bf08b7f2cd897b5964943134aa6d373e355c286db9b9885b1f60b6e8f82b2b85. Stop the container before attempting removal or force remove
```

