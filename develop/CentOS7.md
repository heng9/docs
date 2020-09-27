

# Linux 文件上传下载

* 安装服务

```shell
yum install -y lrzsz
```

*  命令查看是否安装该程序

```shell
rpm -qa |grep sz
rpm -qa |grep rz
```

使用 **rz** 上传文件到服务器上
使用 **sz** 命令发送服务器文件到本机



# Linux 文件解压

* 解压 tar.gz 包

```
tar -xzvf file.tar.gz
```

* 解压 tar包

```
tar –xvf file.tar
```

* 解压 zip 包

```
unzip file.zip
```

* 解压 rar 包

```shell
unrar e file.rar
```

# Linux nohup命令

> nohup命令用于不挂断地运行命令（关闭当前session不会中断改程序，只能通过kill等命令删除）

* 示列

```
nohup command > /opt/logs/myout.log 2>&1 &
```

* 2>&1 &详解
  * bash中：
    * 0 代表STDIN_FILENO 标准输入（一般是键盘），
    * 1 代表STDOUT_FILENO 标准输出（一般是显示屏，准确的说是用户终端控制台），
    * 2 三代表STDERR_FILENO (标准错误（出错信息输出）。
  * \> 直接把内容生成到指定文件，会覆盖原来文件中的内容
  * \>> 尾部追加，不会覆盖原有内容

> 2>&1就是用来将标准错误2重定向到标准输出1中的。此处1前面的&就是为了让bash将1解释成标准输出而不是文件1。至于最后一个&，则是让bash在后台执行。

* /dev/null

表示不输出任何信息到终端



# JDK 配置

* 环境变量

```
vim /etc/profile

export JAVA_HOME=/usr/local/java1.8/jdk1.8.0_131
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

source /etc/profile
```



# Maven 配置

* 环境变量配置

```shell
vim /etc/profile

MAVEN_HOME=/usr/local/maven/apache-maven-3.6.1
export MAVEN_HOME
export PATH=${PATH}:${MAVEN_HOME}/bin

source /etc/profile
```

* 添加 阿里镜像

```xml
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
</mirror>
```

* 依赖默认存放位置

```
${user.home}/.m2/repository
```



# Git 配置

## Yum 安装 git

* 安装 git

```
yum install -y git
```

* 卸载 git

```
yum remove git
```

* 查看 git 信息

```
rpm -qa|grep git
```

* **git 安装位置**

```shell
/usr/libexec/git-core
```

## 设置用户信息

* 设置用户名

```
git config --global user.name 设置用户名
```

* 设置邮箱地址

```
git config --global user.email 设置邮箱
```

* 查看git配置

```
git config --list
```

* 删除用户信息

```
git config --global --unset user.name 用户名
```



# Tomcat

## 内存配置

linux下修改 bin目录下 catalina.sh 文件

```shell
JAVA_OPTS="-Xms1024m -Xmx1024m"
```

## server.xml 配置

> 在 Host 节点下添加 Context 节点, 指定加载war包
>
> Host 节点中 appBase="webapps"  默认指 webapps/ROOT
>
> Context 节点中 war包的命名也同样为 ROOT.war

```xml
<?xml version="1.0" encoding="UTF-8"?>

<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    
    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
          <Context path="" docBase="/usr/local/app/ROOT.war" debug="0" reloadable="true"/>

      </Host>
    </Engine>
  </Service>
</Server>
```

## 启动 tomcat  

tomcat 的路径为 ：/usr/local/tomcat8

* 直接启动

```shell
/usr/local/tomcat8/bin/startup.sh
```

* 作为服务启动

```shell
nohup /usr/local/tomcat8/bin/startup.sh &
```

* 控制台动态输出方式启动

```shell
/usr/local/tomcat8/bin/catalina.sh run
```

* tomcat

```shell
/usr/local/tomcat8/bin/shutdown.sh
```

* 查看 tomcat 进程

```shell
ps aux | grep tomcat
```

* 查看 java 后台运行的命令

```
netstat -anptu|grep java
```



# Redis

## yum 安装 redis

* 安装 redis 数据库

```
yum install -y redis
```

* 下载fedora的epel仓库

```
yum install -y epel-release
```

* 启动redis 服务

```
systemctl start redis
```

* 查看redis 状态

```
systemctl status redis
```

* 停止服务

```
systemctl stop redis 
```

* 重启服务

```
systemctl restart redis
```

* 查看redis进程

```
ps -ef|grep redis
```

* 设置开机自启动

```
systemctl enable redis
```

## 设置 redis 远程连接和密码

编辑 redis 配置文件

```
vi /etc/redis.conf
```

1. 注释 bind 127.0.0.1 ，否则只有本机才能访问

2. 保护模式修改为no，protected-mode no
3. port 6379  端口号默认为 6379
4. requirepass  123456  修改密码为 123456

保存并退出编辑重启redis

## redis客户端

* 进入 redis命令行

```
redis-cli -h 127.0.0.1 -p 6379
```

输入用户名和密码 :  auth  123456

* 退出 redis命令行

```
exit
```



# CentOS7 开启端口访问

> CentOS7 默认的防火墙不是 **iptables**, 而是 **firewalle**

* 查看防火墙所有开放的端口

```shell
firewall-cmd --zone=public --list-ports
```

* 查看防火墙状态

```shell
 firewall-cmd --state
```

* 启动防火墙

```shell
systemctl start firewalld
```

* 关闭防火墙

```shell
systemctl stop firewalld
```

* 开放端口

```shell
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

* 关闭端口

```shell
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

* 配置立即生效

```shell
firewall-cmd --reload
```

* 查看监听的端口

```shell
netstat -lnpt
```

* 检查端口 被哪个进程占用

```shell
netstat -lnpt|grep 80
```



# CnetOS YUM

> Shell前端软件包管理器, 基于RPM包管理, 能够从指定的服务器自动下载RPM包并且安装

* 查找安装包

```
rpm -qa|grep nginx
```

* 查找安装包的安装路径

```
rpm -ql nginx-1.18.0-1.el7.ngx.x86_64
```

如果使用RPM安装了一些包，一般来说，RPM默认安装路径如下：

| Directory      | Contents of Directory                     |
| -------------- | ----------------------------------------- |
| /etc           | 一些配置文件的目录，例如/etc/init.d/mysql |
| /usr/bin       | 一些可执行文件                            |
| /usr/lib       | 一些程序使用的动态函数库                  |
| /usr/share/doc | 一些基本的软件使用手册与帮助文档          |
| /usr/share/man | 一些man page文件                          |

 

# CentOS7 安装 mysql5.7

> 参考：https://www.jianshu.com/p/276d59cbc529
>
> https://www.jianshu.com/p/4587e9429702





# CentOS7  YUM 安装 nginx

> 参考：https://www.cnblogs.com/larryzq/p/11009045.html

## YUM 安装 nginx

1. 添加 nginx 到 YUM源

```shell
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

2. 安装 nginx

```shell
yum -y install nginx
```

3. 启动 nginx 服务

```shell
systemctl start nginx.service
```

4. 开机启动 nginx

```shell
systemctl enable nginx.service
```

5. 开放80端口

```shell
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --reload
```

## Nginx 命令

- **nginx 启动**

```
nginx -c /etc/nginx/nginx.conf
```

* nginx 停止

```shell
nginx -s stop
```

* nginx 重新加载配置

```shell
nginx -s reload
```

* 查看 nginx 监听端口状态

```
netstat -anptu|grep nginx
```

* 查看 nginx 进程

```
ps -ef|grep nginx
```

```shell
root        2928       1  0 11:09 ?        00:00:00 nginx: master process nginx -c /etc/nginx/nginx.conf
nginx       2929    2928  0 11:09 ?        00:00:00 nginx: worker process
nginx       2930    2928  0 11:09 ?        00:00:00 nginx: worker process
nginx       2931    2928  0 11:09 ?        00:00:00 nginx: worker process
nginx       2932    2928  0 11:09 ?        00:00:00 nginx: worker process

```



## Nginx配置信息

* **网站文件存放默认目录**

```
/usr/share/nginx/html
```

- **网站默认站点配置** ( 根据需要自行修改 )

```
/etc/nginx/conf.d/default.conf
```

- 自定义nginx站点配置文件存放目录

```
/etc/nginx/conf.d/
```

- nginx全局配置

```
/etc/nginx/nginx.conf
```

## 配置反向代理

```json
http {
    server {
        listen       80;
        server_name  admin.acgname.com;
		location / {
            root   /usr/local/app/vue-admin; 		# nginx 目录下的 html文件夹
            index  index.html index.htm;
        }

		location /api {
            proxy_pass http://127.0.0.1:8002;
        }

		location ^~/img/static/ {
            proxy_pass http://127.0.0.1:8002/static/;
        }
    }

	server {
        listen       80;
        server_name  blog.acgname.com;
        location / {
            root   /usr/local/app/vue-blog;
            index  index.html index.htm;
        }

        location /api {
            proxy_pass http://127.0.0.1:8002;
        }

		location ^~/img/static/ {
            proxy_pass http://127.0.0.1:8002/static/;
        }
    }
}
```



# CentOS7  源码 安装 nginx

## 源码安装

* 安装依赖包

```
yum install -y gcc openssl-devel pcre-devel zlib-devel gcc-c++
```

* 获取 nginx 源码

```
wget http://nginx.org/download/nginx-1.18.0.tar.gz
```

* 解压进入目录

```
tar -zxvf nginx-1.18.0.tar.gz
cd nginx-1.18.0
```

* 创建文件夹做引导

```
mkdir -pv /usr/local/nginx/
```

* 定义配置 (提供HTTPS服务)

```
./configure --prefix=/usr/local/nginx --with-http_ssl_module
```

* 编译

```
make
```

* 编译安装

```
make install
```

* 启动

```
/usr/local/nginx/sbin/nginx
```

* 停止

```
/usr/local/nginx/sbin/nginx –s stop
```

* 重启

```
/usr/local/nginx/sbin/nginx –s reload
```

* 测试配置文件是否正常

```
/usr/local/nginx/sbin/nginx –t
```

## 配置参数

| 配置                                             | 说明                                       |
| ------------------------------------------------ | ------------------------------------------ |
| --prefix=/usr/local/nginx                        | 安装部署后的根目录，默认为/usr/local/nginx |
| --with-http_ssl_module                           | 提供HTTPS服务；依赖于OpenSSL               |
| --error-log-path=/usr/local/nginx/logs/error.log | error日志放置位置                          |
| --http-log-path=/usr/local/nginx/logs/access.log | access日志放置的位置                       |





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

6. **启动 Docker**

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



# CentsOS 安装 ElasticSearch

## 修改配置

修改 elasticsearch/config/elasticsearch.yml

```shell
network.host: 0.0.0.0  #改为0.0.0.0对外开放，如对特定ip开放则改为指定ip
http.port: 9200      #可更改端口不为9200
cluster.initial_master_nodes: ["node-1"]
```

## 启动 elasticSearch

**由于elasticsearch不允许root用户操作，所以需要建立子用户**

* 增加一个子用户

```
useradd esuser
```

* 赋权

```
chown -R esuser:esuser /usr/local/elasticsearch-7.4.2
```

* 切换成子用户

```
su esuser
```

* 启动 (-d表示后台启动)

```
./elasticsearch -d    
```



* 报错

1. 

```
[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

解决办法

切换到root用户修改配置sysctl.conf，添加下面配置：

```
vm.max_map_count=655360
```

并执行命令

```
sysctl -p
```

重新启动elasticsearch

2. 

```
[1]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
```

修改elasticsearch.yml配置文件

```
cluster.initial_master_nodes: ["node-1"]
```







## ElasticSearch 命令

* 查看所有索引

```
curl -X GET http://localhost:9200/_cat/indices?v  
```





## 添加 IK 分词器

将 压缩包解压到  /elasticsearch-7.2.0/plugins/ik 下，重启即可

```shell
curl -XGET http://localhost:9200/_analyze?pretty -H 'Content-Type:application/json' -d'  {"analyzer":"ik_smart","text":"团子大家族"}' 
```

输入成功返回分词结果

```json
{
  "tokens" : [
    {
      "token" : "团子",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "大家族",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 1
    }
  ]
}
```



IK分词器有两种分词模式：ik_max_word和ik_smart模式。

* ik_max_word : 将文本做最**细粒度**的拆分

* ik_smart : 将文本做最**粗粒度**的拆分

两种分词器使用的最佳实践是：索引时用 ik_max_word，在搜索时用 ik_smart



# CentOS 安装 Kibana

## Kibana 安装启动

* 解压压缩文件，修改 /config/kibana.yml 配置

```
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
i18n.locale: "zh-CN"
```

* 后端启动

```
nohup ./kibana > ./nohup.out 2>&1 &
```

* 访问地址

```
http://localhost:5601
```

## Kibana 命令

* 创建索引

```
PUT index_name
```

* 查看索引的配置

```
GET index_name/_settings
```

* 查看所有索引配置

```
GET _all/_settings
```



# Jenkins

## jenkins 部署

1. 运行 jenkins war 包

```shell
java -jar jenkins.war

nohup java -jar jenkins.war --httpPort=8077 --prefix=/jenkins > /opt/log/jenkins.log 2>&1 &
```

2. 初始化目录位置 webroot: $user.home/.jenkins

```shell
/root/.jenkins
```

## jenkins 设置代理

* **jenkins 设置清华镜像加速**

Manage Jenkins  ->  Manage Plugins  ->  Advanced    ->   Update Site

将 https://updates.jenkins.io/update-center.json  修改为  https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

**这个文件里面 包含了所有插件的更新地址 清华把这个文件镜像过来了 ，但是没有把里面的插件升级地址改成清华。 这样只会获取更新信息快，实际下载不快。**

* **插件下载加速**

1. 在 Windows hosts 文件中 添加

```shell
127.0.0.1 mirrors.jenkins-ci.org
```

​      如果是CentOS系统， 在 CentOS 的  /etc/hosts 中添加

```
127.0.0.1 mirrors.jenkins-ci.org
```

2. 将 jenkins 的请求 反向 代理到清华, 可以借助 nginx 实现反向代理, 启动 nginx,  增加配置.

```json
server {
        listen 80;
        server_name mirrors.jenkins-ci.org;
        location / {
            proxy_redirect off;
            proxy_pass https://mirrors.tuna.tsinghua.edu.cn/jenkins/;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Accept-Encoding "";
            proxy_set_header Accept-Language "zh-CN";
        }
}
```

3. jenkins 常用插件

* Locale
* Git
* Maven Integration
* SSH build Agents

## jenkins 部署maven项目



jenkins执行完任务后，会把衍生出的进程杀死，导致不能启动tomcat

添加BUILD_ID=dontkillme



# Power designer

* 连接数据库导入表

```
File -> Reverse Engineer  ->  Database  
->  Using a data source
->  Connection profile (选择对应的 .dcp 文件，可以同 Configure 新建)
->  Configure  ->  Add Database Source (可选择JDBC的方法配置)

```

* 新建 概念模型 Conceptual Data Model 

```
File  ->  New Model -> Model types  ->  Conceptual Data Model 
```

* 概念模型 下可继续 新建 Conceptual Diagram

```
new  ->  Conceptual Diagram
```

* 显示 表名

```
Tools  ->  Display Preferences  ->  Table  ->  Comment
```

* 给 列 添加 Comment 项

```
双击表格  ->   Columns  ->  Customize Columns and Fiters  ->  Comment 打钩
```

* 显示 字段 注释

```
Tools  ->  Display Preferences  ->  Table  ->  Advanced  ->  Column
在List columns 中选中name
将 name 作为 Comment 使用便可
```

* 设置主键自增

```
选中字段 ->  右键 Properties ->  Identity 打钩
```













# Object Storage Service （OSS）

* 阿里云
* 腾讯云
* 又拍云