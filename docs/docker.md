# docker

* **docker使用注意**
    * docker 启动的时候会绑定本机的一个IP地址 (IP address of docker VM)


* **docker命令**

```
docker version
docker pull
docker images
docker rmi
docker run   :  将镜像放入容器中 并 运行
docker ps -a :  查看所有容器
docker start :  启动容器
docker stop  :  停止容器
docker rm    :  删除容器
docker-machine stop default  :  停止docker服务
docker exec -it <container> bash : 进入容器bash
```


* **启动mysql**

```
mkdir /home/mysql/data 创建数据文件夹 

docker run --name=mysql -d -it \
-e 'DB_NAME=db_test' \
-e 'DB_USER=admin' \
-e 'DB_PASS=123' \
-p 3306:3306 \
-v /home/mysql/data:/var/lib/mysql \
sameersbn/mysql
```


* **启动redis**

```
docker run --name=redis -d -it \
-p 6379:6379 \
sameersbn/redis
```


* **后台进程参数**

```
-d, --daemon=false	开启Daemon模式
-v,                 挂载宿主机的一个目录
-i                  让容器的标准输入保持打开
-t                  让docker分配一个伪终端并绑定到容器的标准输入上
```


# docker compose
> Docker Compose 是 Docker 容器进行编排的工具，定义和运行多容器的应用，可以一条命令启动多个容器。

### 使用Compose 基本上分为三步：

* Dockerfile:  定义应用的运行环境
* docker-compose.yml : 定义组成应用的各服务
* docker-compose up  : 启动整个应用

* docker-compose -f docker-compose.yml up
* docker-compose down : 删除所有容器, 镜像
* docker-compose config  -q : 验证（docker-compose.yml）文件配置，当配置正确时，不输出任何内容，当文件配置错误，输出错误信息。 





# CentOS 安装Docker

1. 安装所需的软件包 

**yum-utils** 提供了 yum-config-manager ，

并且 device mapper 存储驱动程序需要 **device-mapper-persistent-data** 和 **lvm2**

```
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

2. 设置仓库 (使用 阿里云)

```
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

3. 安装 Docker Engine-Community

```
yum install docker-ce docker-ce-cli containerd.io
```

4. 查看可用版本（从高到低）

```
yum list docker-ce --showduplicates | sort -r

docker-ce.x86_64            3:19.03.9-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.8-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.7-3.el7                     docker-ce-stable
```

5. 选择版本安装  

```
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

选择 18.09.1 版本安装, 如果提示您接受 GPG 密钥，请选是

```
yum install -y docker-ce-18.09.1 docker-ce-cli-18.09.1 containerd.io
```

```
导入 GPG 公钥 0x621E9F35:
 Userid: "Docker Release (CE rpm) <docker@docker.com>"
 指纹: 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
 来自: https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
```

6. 启动 Docker

```
systemctl start docker
```

7. 查看docker版本

```
docker --version
```

8. 通过运行 hello-world 映像来验证是否正确安装了 Docker Engine-Community

```
docker pull hello-world
docker run hello-world
```



# 编写 Dockerfile

编写 build.sh,   构建 镜像

```shell
#!/usr/bin/env bash
mvn clean package
# . 表示根据当前目录下的 Dockerfile 构建 dockers 镜像
docker build -t spring-boot:latest .
```

编写 Dockerfile， **起始第一行必须是依赖的镜像**

```
# 该镜像需要依赖的基础镜像
FROM java:8

# 指定维护者的名字
MAINTAINER admin

# 将 当前目录下的jar包复制到 docker容器的/目录下
ADD target/spring-boot-1.0-SNAPSHOT.jar /spring-boot.jar

# 指定docker容器启动时运行jar包
ENTRYPOINT ["java", "-jar", "/spring-boot.jar"]
```

启动 docker

对于一些动态参数， 可以使用参数的方式传入

```
docker run -it spring-boot:latest  -p 6379:6379  --mysql.address=127.0.0.1
```



# docker-compose

* docker 下的服务通讯

```

```





# harbor

>  开源 的 docker 管理仓库





# Kubernetes

>  服务编排

查看版本

```
kubectl version
```

获取节点所有信息

```
kubectl get nodes
```

获取所有部署

```
kubectl get deploy
```

获取所有的 pod

```
kubectl get pods
kubectl get pods -o wide
```

创建 deployment

```
kubectl run deploy-name --image=image-name:version --port=8080
```

扩缩容

```
kubectrl scal deploy deploy-name --replicas=4
```



### kube-proxy

为集群增加 service 功能 , 通过 iptables 负载均衡

```
kubectl get services
```

```
kubectl expose deploy deploy-name --type="NodePort" --target-port=8080 --port=80
```



### kube-dns

> 通过 DNS 访问

```

```





