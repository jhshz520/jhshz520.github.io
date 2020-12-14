---
title: mpi_docker项目分析
date: 2020-11-24 08:39:50
categories: 实验
tags: 实验
---

# 项目结构
## For single-host with Docker Compose
```
cluster
├── Dockerfile          # Image specification
├── project             # Sample program source code
│   └── mpi_hello_world.c
├── ssh                 # keys for accessing
│   ├── id_rsa          # (could generate your own)
│   └── id_rsa.pub
├── .env                # General configuration
├── docker-compose.yml  # Container orchestration 
└── cluster.sh          # Commands wrapper ultility
```

接下来逐个文件解析项目
## cluster.sh
```
set -e # 如果不带任何参数 直接运行set，会显示所有环境变量和shell函数
# set -x 用来在运行结果之前，先输出执行的那一行命令,set -e表示脚本执行出错就立即退出，避免错误的积累



# shellcheck disable=SC1091
. ./.env

#######################
# TASK INDICATORS
COMMAND_UP=0
COMMAND_DOWN=0
COMMAND_RELOAD=0
COMMAND_SCALE=0
COMMAND_LOGIN=0
COMMAND_EXEC=0
COMMAND_LIST=0
COMMAND_CLEAN=0

# Default values if providing empty
SIZE=4

#############################################
usage ()
{
    echo " Alpine MPICH Cluster (for Docker Compose on single Docker host)"
    echo ""
    echo " USAGE: ./cluster.sh [COMMAND] [OPTIONS]"
    echo ""
    echo " Examples of [COMMAND] can be:"
    echo "      up: start cluster"
    echo "          ./cluster.sh up size=10"
    echo ""
    echo "      scale: resize the cluster"
    echo "          ./cluster.sh scale size=30"
    echo ""
    echo "      reload: rebuild image and distribute to nodes"
    echo "          ./cluster.sh reload size=15"
    echo ""
    echo "      login: login to Docker container of MPI master node for interactive usage"
    echo "          ./cluster.sh login"
    echo ""
    echo "      exec: execute shell command at the MPI master node"
    echo "          ./cluster.sh exec [SHELL COMMAND]"
    echo ""
    echo "      down: shutdown cluster"
    echo "          ./cluster.sh down"
    # echo ""
    # echo "      clean: remove images in the system"
    # echo "          ./cluster.sh clean"
    echo ""
    echo "      list: show running containers of cluster"
    echo "          ./cluster.sh list"
    echo ""
    echo "      help: show this message"
    echo "          ./cluster.sh help"
    echo ""
    echo "  "
}

HEADER="
         __v_
        (.___\\/{
~^~^~^~^~^~^~^~^~^~^~^~^~"

down_all ()
{
    printf "\\n\\n===> CLEAN UP CLUSTER"

    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose down"
    printf "\\n"

    docker-compose down
}

up_registry ()
{
    printf "\\n\\n===> SPIN UP REGISTRY"

    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose up -d registry"
    printf "\\n"

    docker-compose up -d registry
}

generate_ssh_keys ()
{
    if [ -f ssh/id_rsa ] && [ -f ssh/id_rsa.pub ]; then
        return 0
    fi

    printf "\\n\\n===> GENERATE SSH KEYS \\n\\n"

    echo "$ mkdir -p ssh/ "
    printf "\\n"
    mkdir -p ssh/

    echo "$ ssh-keygen -f ssh/id_rsa -t rsa -N ''"
    printf "\\n"
    ssh-keygen -f ssh/id_rsa -t rsa -N ''
}

build_and_push_image ()
{
    printf "\\n\\n===> BUILD IMAGE"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker build -t \"$REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME\" ."
    printf "\\n"
    docker build -t "$REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME" .

    printf "\\n"

    printf "\\n\\n===> PUSH IMAGE TO REGISTRY"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker push \"$REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME\""
    printf "\\n"
    docker push "$REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME"
}

up_master ()
{
    printf "\\n\\n===> SPIN UP MASTER NODE"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose up -d master"
    printf "\\n"
    docker-compose up -d master
}


up_workers ()
{
    printf "\\n\\n===> SPIN UP WORKER NODES"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose up -d worker"
    printf "\\n"
    docker-compose up -d worker 

    printf "\\n"
    printf "\\n%s\\n" "$HEADER"

    NUM_WORKER=$((SIZE - 1))
    echo "$ docker-compose scale worker=$NUM_WORKER"
    printf "\\n"
    docker-compose scale worker=${NUM_WORKER}
}

down_master ()
{
    printf "\\n\\n===> TORN DOWN MASTER NODE"
    printf "\\n%s\\n" "$HEADER"

    echo "$ docker-compose stop master && docker-compose rm -f master"
    printf "\\n"
    docker-compose stop master && docker-compose rm -f master
}

down_workers ()
{
    printf "\\n\\n===> TORN DOWN WORKER NODES"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose stop worker && docker-compose rm -f worker"
    printf "\\n"
    docker-compose stop worker && docker-compose rm -f worker
}

list ()
{
    printf "\\n\\n===> LIST CONTAINERS"
    printf "\\n%s\\n" "$HEADER"
    echo "$ docker-compose ps"
    printf "\\n"
    docker-compose ps
}


exec_on_mpi_master_container ()
{
    # shellcheck disable=SC2046
    docker exec -it -u mpi $(docker-compose ps | grep 'master'| awk 'NR==1{print $1}') "$@"
}

prompt_ready ()
{
    printf "\\n\\n===> CLUSTER READY \\n\\n"
}

show_instruction ()
{
    echo '                            ##         .          '
    echo '                      ## ## ##        ==          '
    echo '                   ## ## ## ## ##    ===          '
    echo '               /"""""""""""""""""\___/ ===        '
    echo '          ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~ '
    echo '               \______ o           __/            '
    echo '                 \    \         __/               '
    echo '                  \____\_______/                  '
    echo '                                                  '
    echo '                 Alpine MPICH Cluster             '
    echo ''
    echo ' More info: https://github.com/NLKNguyen/alpine-mpich'
    echo ''
    echo '=============================================================='
    echo ''

    echo "To run MPI programs in an interative shell:"
    echo "  1. Login to master node:"
    echo "     Using Docker through command wrapper:"
    echo "     $ ./cluster.sh login"
    echo ""
    echo "     Or using SSH with keys through exposed port:"
    echo "     $ ssh -o \"StrictHostKeyChecking no\" -i ssh/id_rsa -p $SSH_PORT mpi@localhost"
    echo '       where [localhost] could be changed to the host IP of master node'
    echo ""
    echo "  2. Execute MPI programs inside master node, for example:"
    echo "     $ mpirun hostname"
    echo "      *----------------------------------------------------*"
    echo "      | Default hostfile of connected nodes in the cluster |"
    echo "      | is automatically updated at /etc/opt/hosts         |"
    echo "      | To obtain hostfile manually: $ get_hosts > hosts   |"
    echo "      * ---------------------------------------------------*"
    echo ""
    echo ""
    echo "To run directly a shell command at master node:"
    echo "     $ ./cluster.sh exec [COMMAND]"
    echo ""
    echo "     Example: "
    echo "     $ ./cluster.sh exec mpirun hostname"
    echo ""
}



#############################################

while [ "$1" != "" ];
do
    PARAM=$(echo "$1" | awk -F= '{print $1}')
    VALUE=$(echo "$1" | awk -F= '{print $2}')

    case $PARAM in
        help)
            usage
            exit
            ;;
        -i)
            show_instruction
            exit
            ;;

        login)
            COMMAND_LOGIN=1
            ;;

        exec)
            COMMAND_EXEC=1
            shift # the rest is the shell command to run in the node
            SHELL_COMMAND="$*"
            break # end while loop
            ;;

        up)
            COMMAND_UP=1
            ;;

        down)
            COMMAND_DOWN=1
            ;;

        reload)
            COMMAND_RELOAD=1
            ;;

        scale)
            COMMAND_SCALE=1
            ;;

        list)
            COMMAND_LIST=1
            ;;

        clean)
            COMMAND_CLEAN=1
            ;;

        size)
            [ "$VALUE" ] && SIZE=$VALUE
            ;;

        *)
            echo "ERROR: unknown parameter \"$PARAM\""
            usage
            exit 1
            ;;
    esac
    shift
done


if [ $COMMAND_UP -eq 1 ]; then
    down_all
    up_registry
    generate_ssh_keys
    build_and_push_image
    up_master
    up_workers

    prompt_ready
    show_instruction

elif [ $COMMAND_DOWN -eq 1 ]; then
    down_all

elif [ $COMMAND_CLEAN -eq 1 ]; then
    echo "TODO"


elif [ $COMMAND_SCALE -eq 1 ]; then
    down_master
    down_workers
    up_master
    up_workers

    prompt_ready
    show_instruction

elif [ $COMMAND_RELOAD -eq 1 ]; then
    down_master
    down_workers
    build_and_push_image
    up_master
    up_workers

    prompt_ready
    show_instruction

elif [ $COMMAND_LOGIN -eq 1 ]; then
    exec_on_mpi_master_container /bin/sh

elif [ $COMMAND_EXEC -eq 1 ]; then
    exec_on_mpi_master_container ash -c "${SHELL_COMMAND}"

elif [ $COMMAND_LIST -eq 1 ]; then
    list
else
    usage
fi
```
## dockerfile
```
FROM nlknguyen/alpine-mpich:onbuild

# # ------------------------------------------------------------
# # Build MPI project
# # ------------------------------------------------------------

# Put all build steps and additional package installation here

# Note: the current directory is ${WORKDIR:=/project}, which is
# also the default directory where ${USER:=mpi} will SSH login to

# Copy the content of `project` directory in the host machine to 
# the current working directory in this Docker image
COPY project/ .

# Normal build command
RUN mpicc -o mpi_hello_world mpi_hello_world.c
# RUN sudo chmod 777 -R /project && cd /project/NPB3.3.1/NPB3.3-MPI && make IS CLASS=A NPROCS=4
# ####################
# For Docker beginner:

# After Docker syntax `RUN`, you can execute any command available in 
# the current shell of the image.

# To switch to root:    USER root
# To switch back to default user: USER ${USER}
```
## docker-compose.yml
```
version: "2"

services:
  registry:
    image: registry
    ports:
      - "${REGISTRY_PORT}:5000"

  master:
    image: $REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME
    user: root
    entrypoint: ["mpi_bootstrap", "role=master", "mpi_master_service_name=master", "mpi_worker_service_name=worker"]
    ports:
      - "${SSH_PORT}:22"
    networks:
      - net

  worker:
    image: $REGISTRY_ADDR:$REGISTRY_PORT/$IMAGE_NAME
    user: root
    entrypoint: ["mpi_bootstrap", "role=worker", "mpi_master_service_name=master", "mpi_worker_service_name=worker"]
    networks:
      - net

networks:
  net:
```


## NPB测试的使用



进入home/hadoop/alpine-mpich/cluster文件夹下

```

make IS CLASS=B NPROCS=8


make: execvp: sys/print_header: Permission denied
Makefile:68: recipe for target 'header' failed
make: *** [header] Error 127


需要更改权限，可以使用 sudo chmod 777 -R /project

```

## docker怎么进入一个正在运行的容器内部

```
sudo docker exec -it 775c7c9ee1e1 /bin/bash

sudo docker exec -it 775c7c9ee1e1 /bin/sh
```

我的宿主机没有安装MPICH但是仍然能够在容器中进行编译和运行，但是在宿主机上是不能编译的，所以文献中的兼容性问题，我认为是想现在主机上进行编译，编译完之后在各个容器之间进行分发，这样在主机编译后的代码需要在容器的环境下能够运行

## 参照默认dockerfile定制化
```


```

定制化后在虚拟机上 运行mpirun -np 1 is.A.4

仍然出现以下错误:

<font color='yellow'>

 ERROR: compiled for 4 processes
 Number of active processes: 1
 Exiting program!

</font>

npb 测试集 mpi run -np， np这个参数得和编译所用的参数相一致才不会退出


## 怎么查看docker容器的运行状态

```
docker top grafana
得出grafana二进制文件目录；

得出grafana配置文件地址；

得出grafana日志文件地址


docker inspect grafana
```

"LogPath": "/var/lib/docker/containers/c7f8a384fc1b647989b5b78336feb67fbc7002d713156a49d33f87aadebc44a1/c7f8a384fc1b647989b5b78336feb67fbc7002d713156a49d33f87aadebc44a1-json.log"


## docker 怎么把容器保存为本地镜像
 
 commit 命令

 ## 怎么给docker 容器重命名
容器在运行的时候可以利用以下语句进行重命名
```
 sudo docker rename discourse_app disc_app
 ```

 ## docker run的参数解释

```
-a  attach 表示追加
- i 表示交互方式
- t

--privileged=false， 指定容器是否为特权容器，特权容器拥有所有的capabilities

```
**指定容器名称**
利用容器镜像启动容器的时候直接指定容器的名称
```
docker run --name [container name] -t -i [image ID | Repository:TAG] /bin/bash

sudo docker run --name sql5.7 -it mysql:5.7 /bin/bash

```

## 删除容器的镜像

直接 sudo docker rmi mysql的时候会报错，因为还有容器依赖于该镜像，所以正常情况下是先删除容器再删除容器依赖的镜像

docker删除所有none镜像

```

docker rmi `docker images | grep  '<none>' | awk '{print $3}'`

```

## docker数据映射

**什么是挂载**

当在 Linux 系统中使用这些硬件设备时，只有将Linux本身的文件目录与硬件设备的文件目录合二为一，硬件设备才能为我们所用。合二为一的过程称为“挂载”。

挂载，指的就是将设备文件中的顶级目录连接到 Linux 根目录下的某一目录（最好是空目录），访问此目录就等同于访问设备文件

当插入了新硬盘，分了新磁盘区sdb1。它现在还不属于/。

我们虽然可以在一些图形桌面系统里找到他的位置，浏览管理里面的文件，但在命令行却不知怎么访问它的目录，比如无法使用cd或者ls。也无法在编程时指定一个目录对它操作。

当我们使用了 mount /dev/sdb1 ~/Share/ ，把新硬盘的区sdb1挂载到工作目录的~/Share/文件夹下，之后访问这个~/Share/文件夹就相当于访问这个硬盘2的sdb1分区了。对/Share/的任何操作，都相当于对sdb1里文件的操作。

所以Linux下，mount挂载的作用，就是将一个设备（通常是存储设备）挂接到一个已存在的目录上。访问这个目录就是访问该存储设备。

插入CD，系统其实自动执行了 mount /dev/cdrom /media/cdrom。所以可以直接在/media/cdrom中对CD中的内容进行管理



纠正一个误区，并不是根目录下任何一个目录都可以作为挂载点，由于挂载操作会使得原有目录中文件被隐藏，因此根目录以及系统原有目录都不要作为挂载点，会造成系统异常甚至崩溃，挂载点最好是新建的空目录


语法：docker run –dit –v /src:/dst centos:latest bash
-v 用来指定挂载目录, 冒号: 前面的/src 为物理机本地目录,:后面的/dst 为容器里的目录:

例1：把物理机上的/var/ww/html映射到docker实例的/var/www/html
[root@xuegod110 ~]# mkdir -p /var/www/html/
[root@xuegod110 ~]# docker run -dit --name docker11 -v /var/www/html:/var/www/html centos:latest bash
[root@xuegod110 ~]# echo xuegod110 > /var/www/html/index.html #在物理机上写入文件
[root@xuegod110 ~]# docker exec -it docker11 bash
[root@5be95cdaaa29 /]# cat /var/www/html/index.html #容器实例查看


## 能不能对同一个docker容器建立两个以上的互动窗口

如何打开多个终端进入Docker容器


