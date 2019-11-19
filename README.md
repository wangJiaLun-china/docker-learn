[TOC]



## 基本概念

- 镜像（Image）
  - 镜像就是一个只读的模板。
  - 镜像可以用来创建 Docker 容器。
- 容器（Container）
  - 容器可以运行应用
  - 容器是从镜像创建的运行实例，允许被启动，开始，停止，删除
  - 各个容器之间相互隔离保证安全，相当于一个独立的linux环境
- 仓库（Repository）
  - 仓库分为公开仓库（Public）和私有仓库（Private）两种形式
  - 仓库是集中存放镜像文件的场所

## CentOS 系列安装 Docker

​	Docker支持 CentOS6.5 及之后版本

​	使用``` uname -r ``` 查看系统内核版本

  	使用 yum -y update 升级所有包，包含软件和系统内核

​	使用``` yum -y upgrade ``` 只升级包，不升级软件和系统内核

- CentOS6  

  - 要求系统为64位 ，系统内核版本位 2.6.32-431 及以上版本
  - CentOS6，可以使用 EPEL 库安装 Docker，命令如下

  ```shell
  $ sudo yum install http://mirrors.yun-idc.com/epel/6/i386/epel-release-68.noarch.rpm
  $ sudo yum install docker-io
  ```

- CentOS7

  - 系统64位，系统内核版本3.10以上
  - CentOS7 系统 CentOS-Extras 库中已带 Docker，可以直接安装：

  ```shell
  $ sudo yum install docker
  ```

  - 安装之后启动 Docker 服务，并使其随系统自动加载

  ```shell
  $ sudo service docker start
  $ sudo chkconfig docker on
  ```

- 查看Docker 版本

  ```shell
  docker version
  ```

- 查看Docker  信息

  ```shell
  docker info
  ```

  ​

## Docker 镜像

- Docker 运行容器前 需要本地存在对应镜像
- 如镜像本地不存在，Docker 会冲镜像仓库下载（默认是Docker Hub 公共注册服务器中的仓库）

### 列出镜像列表

- 使用 ```docker images ``` 列出本地主机 镜像 

#### docker images列表参数

- REPOSITORY： 镜像的仓库源
- TAG: 镜像的标签 （同一个仓库有可以有多个TAG，代表不此仓库源不同版本）
- IMAGE ID: 镜像 ID
- CREATED： 镜像创建时间
- SIZE: 镜像大小

### 获取新的镜像

- 当使用不存在镜像时Docker会自动下载此镜像
- 也可以预先下载此镜像，使用```docker pull``` 命令

### 查找镜像

- 使用```docker search``` 命令搜索镜像

#### docker search列表参数

- NAME: 镜像仓库源的名称
- DESCPIPTION: 镜像描述
- OFFICIAL: 是否Docker官方发布

### 拖取镜像

- 使用```docker pull``` 命令

### 创建镜像

- 从已创建的容器中更新镜像，并提交镜像

  - 在运行中的容器中使用 ```apt-get update``` 命令更新

  - 更新完成后使用 ```exit``` 命令退出容器

  - 使用 ```docker commit ``` 提交副本容器

  - #### docker commit参数

    - -m: 提交的描述信息
    - -a: 指定镜像作者
    - 容器Id
    - 创建后的目标镜像名

### 删除镜像

- 使用```docker rmi```命令移除本地镜像
- 删除镜像之前必须用```docker rm```删除依赖于这个镜像的所有容器

## Docker 容器

容器是独立运行的一个或一组应用，以及它们的运行态环境。

### 启动容器

- 直接使用```docker run```新建并启动
- 利用```docker start``` 命令，将已终止容器启动运行

### 终止容器

- 使用``` docker stop ``` 终止一个运行中的容器
- 当Docker 容器中指定的应用终结时，容器也自动终止（只启动一个终端的容器，通过```exit```或者```Ctrl+d```退出终端时，容器立刻终止）
- 使用```docker ps -a ```命令可以查看到处于终止状态的容器，可以使用```docker start```启动

### 重启容器

- 使用```docker restart``` 将一个运行态的容器终止，再重新启动

### 进入容器

使用``` -d ```参数后，容器启动后会进入后台运行，需要进入容器内部操作通过```docker attach```命令或者```nsenter```工具等

- attach 命令
  - ```docker attach```是Docker 自带命令，使用方式```docker attach 容器名``` 
  - 注意 当多个窗口同时 attach 同一个容器时，所有窗口同步显示
- exec 命令
  - 使用 ```docker exec ``在运行的容器中执行命令
    - **-d :**分离模式: 在后台运行
    - **-i :**即使没有附加也保持STDIN 打开
    - **-t :**分配一个伪终端

### 导出容器

- 使用```docker export``` 命令

  ```shell
  docker export CONTAINERID > ***.tar
  ```

### 导入容器快照

- 使用```docker import``` 从容器快照文件中再导入为镜像

  ```shell
  $ cat ***.tar | sudo docker import - ***/***:v1.0
  ```

- 或者 通过 指定URL 或者目录导入

  ```shell
  $sudo docker import http://example.com/exampleimage.tgz example/imagerepo
  ```

### 删除容器

- 使用``` docker rm ``` 删除处于终止状态的容器
- 删除运行中容器 添加 ```-f```参数，Docker 会发送 SIGKILL 信号给容器

## Docker 仓库



## Docker 安装 MinIO 文件服务器

- 使用```docker search minio``` 搜索镜像

- 使用```docker pull docker.io/minio/minio``` 拉取镜像

- 启动 docker 

  - MinIO 需要一个持久卷来存储配置和应用数据。可以通过简单地传递一个目录（在下面的示例中为`/ data`）启动MinIO。这个目录会在容器启动时在容器的文件系统中创建，不过所有的数据都会在容器退出时丢失。

    ```shell
    docker run -p 9000:9000 minio/minio server /data
    ```

    要创建具有永久存储的MinIO容器，需要将本地持久目录从主机操作系统映射到虚拟配置`~/.minio` 并导出`/data`目录。 为此，请运行以下命令

    #### GNU/Linux 和 macOS

    ```
    docker run -p 9000:9000 --name minio1 \
      -v /mnt/data:/data \
      -v /mnt/config:/root/.minio \
      minio/minio server /data
    ```

    #### Windows

    ```shell
    docker run -p 9000:9000 --name minio1 \
      -v D:\data:/data \
      -v D:\minio\config:/root/.minio \
      minio/minio server /data
    ```

    ## 在Docker中运行MinIO分布式模式

    分布式MinIO可以通过 [Docker Compose](https://docs.min.io/cn/deploy-minio-on-docker-compose) 或者 [Swarm mode](https://docs.min.io/cn/deploy-minio-on-docker-swarm)进行部署。这两者之间的主要区别是Docker Compose创建了单个主机，多容器部署，而Swarm模式创建了一个多主机，多容器部署。

    这意味着Docker Compose可以让你快速的在你的机器上快速使用分布式MinIO-非常适合开发，测试环境；而Swarm模式提供了更健壮，生产级别的部署。

    ## MinIO Docker提示

    ### MinIO自定义Access和Secret密钥

    要覆盖MinIO的自动生成的密钥，您可以将Access和Secret密钥设为环境变量。 MinIO允许常规字符串作为Access和Secret密钥。

    #### GNU/Linux 和 macOS

    ```shell
    docker run -p 9000:9000 --name minio \
      -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" \
      -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
      -v /mnt/data:/data \
      -v /mnt/config:/root/.minio \
      minio/minio server /data
    ```

    #### Windows

    ```
    docker run -p 9000:9000 --name minio \
      -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" \
      -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
      -v D:\data:/data \
      -v D:\minio\config:/root/.minio \
      minio/minio server /data
    ```

    ### 使用Docker secrets进行MinIO Access和Secret密钥自定义

    要覆盖MinIO的自动生成的密钥,你可以把secret和access秘钥创建成[Docker secrets](https://docs.docker.com/engine/swarm/secrets/). MinIO允许常规字符串作为Access和Secret密钥。

    ```powershell
    echo "AKIAIOSFODNN7EXAMPLE" | docker secret create access_key -
    echo "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" | docker secret create secret_key -
    ```

    使用`docker service`创建MinIO服务，并读取Docker secrets。

    ```shell
    docker service create --name="minio-service" --secret="access_key" --secret="secret_key" minio/minio server /data
    ```

## Docker 安装 Nginx 代理服务器

- 拉取镜像 ``` docker push nginx```

- 启动容器 ```docker run -p 80:80 --name nginx -d nginx```

  - -p 端口映射 将本地80(:前) 端口 映射到容器内部的80端口
  - -d 设置容器后台在运行 

- 创建目录用于存储 容器里Nginx默认配置文件

  ```shell
  mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf
  ```

- 拷贝容器内 Nginx 默认配置文件到本地当前目录下的 conf 目录

  ```shell
  docker cp CONTAINERID:/etc/nginx/nginx.conf ~/nginx/conf
  ```

- 部署

  ```shell
  docker run -d -p 80:80 --name nginx -v ~/nginx/www:/usr/share/nginx/html -v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/nginx/logs:/var/log/nginx nginx
  ```

- Nginx 热部署

  ```shell
  docker exec CONTAINERID nginx -s reload
  ```

## Docker 安装 Mysql

- ```shell
  docker run --name mysql -e MYSQL_ROOT_PASSWORD=76516051t -d -i -p 3306:3306 --restart=always mysql:5.7
  ```

## Docker 安装 Nexus3

- ```shell
  docker pull sonatype/nexus3
  ```

- ```shell
  docker run -d -p 8081:8081 --name nexus sonatype/nexus3
  ```

  ​

## Docker 安装 Wordpress

- ```shell
  docker run -e WORDPRESS_DB_HOST=172.16.12.178:3306 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=****** -p 8082:80 --name       wordpress -d wordpress
  ```



## Docker 安装 Redis

- ```shell
  docker run -p 6379:6379 -v $PWD/data:/data  -d redis:3.2 redis-server --appendonly yes
  ```

## 错误问题及解决方式记录

- 错误信息:

  - ```shell
    container_linux.go:247: starting container process caused "process_linux.go:258: applying cgroup configuration for process caused \"Cannot set property TasksAccounting, or unknown property.\""
    /usr/bin/docker-current: Error response from daemon: oci runtime error: container_linux.go:247: starting container process caused "process_linux.go:258: applying cgroup configuration for process caused \"Cannot set property TasksAccounting, or unknown property.\"".

    ```

- 解决: centos系统版本兼容性问题，如果将系统做更新升级，即可解决

  - ```shell
    yum update
    ```
