# Linux 目录介绍

| 目录       | 说明             | 对比             |
| ---------- | ---------------- | ---------------- |
| /usr       | 系统级的目录     | C:/Windows       |
| /usr/local | 用户级的程序目录 | C:/Progrem Files |
| /opt       | 附加软件包       | D:/Software      |

建议：

* 安装包放在 /opt 下





# Linux 用户组 和 用户

* 添加用户组

```
groupadd group_name
```

* 添加用户到 用户组

```
useradd user_name -g grup_name -p password
```

* 删除用户组

```
groupdel group_name
```

* 删除用户

```
userdel user_name
```





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

编辑 profile 文件，在末尾添加环境变量，之后重新加载

```shell
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

* 关闭 tomcat

```shell
/usr/local/tomcat8/bin/shutdown.sh
```

* 查看 java 后台运行的命令

```
netstat -anptu|grep java
```

## tomcat 日志输出

* tomcat 日志 默认存放在  tomcat 的 logs 目录下的 catalina.out

catalina.out 包含 tomcat的日志以及**控制台输出**的内容

```shell
tail -f catalina.out
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

输入用户名和密码 :  

```
auth  123456
```

* 退出 redis命令行

```
exit
```



# CentOS 安装 Redis

(参考链接)[https://www.cnblogs.com/heqiuyong/p/10463334.html]

* 安装 gcc 环境（gcc -v）

```
yum install -y gcc 
```

* 解压 并 编译

```
tar -zxvf redis-5.0.3.tar.gz
cd redis-5.0.3
make
```

* 安装并指定安装目录

```
make install PREFIX=/usr/local/redis
```

* 启动

1. 前台启动

```
/usr/local/redis/bin/redis-server
```

2. 后台启动

* 从 redis 的源码目录中复制 redis.conf 到 redis 的安装目录

```
cp /opt/redis-5.0.3/redis.conf /usr/local/redis/bin/
```

* 修改 redis.conf 文件，把 daemonize no 改为 daemonize yes，启动 redis

```

/usr/local/redis/bin/redis-server /usr/local/redis/bin/redis.conf
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



# CentOS YUM

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

## 安装 mysql5.7

1. **准备安装包**

* 解压安装包

```shell
tar -xvf mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz
```

* 移动并重命名

```shell
mv mysql-5.7.26-linux-glibc2.12-x86_64 /usr/local/mysql
```



2. **创建MySQL用户组 和 用户**

* 创建MySQL用户组 和 用户并修改权限

```shell
groupadd mysql
useradd -r -g mysql mysql
```

* 创建数据目录并赋予权限

```shell
mkdir -p /data/mysql
chown mysql:mysql -R /data/mysql
```



3. **初始化数据库**

* 配置my.cnf

```
vi /etc/my.cnf
```

内容如下

```shell
[mysqld]
bind-address=0.0.0.0
port=3306
user=mysql
basedir=/usr/local/mysql
datadir=/data/mysql
log-error=/data/mysql/mysql.err
pid-file=/data/mysql/mysql.pid
socket=/tmp/mysql.sock

#character config
character_set_server=utf8mb4
symbolic-links=0
explicit_defaults_for_timestamp=true
```

* 初始化

```shell
/usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql/ --datadir=/data/mysql/ --user=mysql --initialize
```



4. **启动MySQL服务**

* 先将 mysql.server 放置到 /etc/init.d/mysql 中

```shell
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

* 启动

```
service mysql start
```

![mysql](.\mysql.png)

到这里说明MySQL已经安装成功了

* 添加软链接

```shell
ln -s /usr/local/mysql/bin/mysql /usr/bin
```



5. **修改密码**

* 查看初始密码

```
cat /data/mysql/mysql.err
```

* 登陆MySQL

```
mysql -uroot -p
```

* 设置密码

```
SET PASSWORD = PASSWORD('root');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
FLUSH PRIVILEGES;
```



6. **远程连接**

```
use mysql;
update user set host = '%' where user = 'root';    #使root能再任何host访问
FLUSH PRIVILEGES;
```



7. **mysql 命令**

* 启动 mysql 服务 

```shell
service mysql start
```

* 停止 mysql 服务

```shell
service mysql stop
```

* 重启 mysql 服务 

```shell
service mysql restart
```

修改 ini 配置文件后，重启 mysql 服务生效



## 报错解决

* mysql 登录报错

```
./mysql -uroot -p

./mysql: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory
```

解决办法

```
yum install -y libncurses*
```



## 命令行指令

* 查看 server_id

```
show variables like 'server_id';
```





## 开启 binlog 日志

* binlog 日志配置

```shell
# binlog 配置
log-bin=/data/mysql/logs/mysql-bin.log
expire-logs-days=14
max-binlog-size=1024M
server-id=1
```

* 查询 binlog 日志是否开启

```
show variables like 'log_bin';
```



# MySql 主从复制

## 主服务器中有数据

1. 使用 mysqldump 工具创建要复制的所有数据库的转储

```shell
/usr/local/mysql/bin/mysqldump -uroot -p --all-databases --master-data=1 > dbdump.db
```

2. 把备份出来的数据传输到从服务器

```
scp dbdump.db root@192.168.0.104:/data/
```

3. 导入数据到从服务器，进入命令行，执行：

```
source /data/dbdump.db
```

**注意**：经试验，无需 复制 主数据库数据，表也不需要建立，只要在 binlog 开启 前的数据库操作，会同步到从数据库。



## 主从复制

**注意**：确保在主服务器上 `skip_networking` 选项处于 `OFF` 关闭状态, 这是默认值。

如果是启用的，则从站无法与主站通信，并且复制失败。

```
show variables like '%skip_networking%';
```

* 在 主数据库 创建一个专门用于复制数据的用户

```
CREATE USER 'backup'@'%';
GRANT REPLICATION SLAVE ON *.* TO 'backup'@'%' identified by 'backup@123456';
FLUSH PRIVILEGES;
```

* 配置从服务器，修改 my.cnf 文件并重启

```
[mysqld]
server-id=2
```

```
service mysql restart
```

* 在从服务器配置连接到主服务器的相关信息

进入命令行，执行：

```
CHANGE MASTER TO
MASTER_HOST='192.168.0.105',  -- 主服务器的主机名(也可以是 IP) 
MASTER_PORT=3306,
MASTER_USER='backup',
MASTER_PASSWORD='backup@123456';
```

* 启动从服务器的复制线程

```
start slave;
stop slave;  # 停止复制线程
```

* 查看状态， I/O 线程和 SQL 线程 是否都启动成功

```
show slave status\G
```

```shell
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.0.105
                  Master_User: backup
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 7860
               Relay_Log_File: localhost-relay-bin.000011
                Relay_Log_Pos: 990
        Relay_Master_Log_File: mysql-bin.000003
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 7860
              Relay_Log_Space: 1367
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
                  Master_UUID: f4626cb9-ff92-11ea-b4b1-000c294799e9
             Master_Info_File: /data/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 

```



# MySql 读写分离

## sharding-jdbc 读写分离

* spring-boot 项目依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
    <relativePath/>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.0.1</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.16</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shardingsphere</groupId>
        <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
        <version>4.0.0-RC1</version>
    </dependency>
</dependencies>
```

* spring-boot 项目配置

```yml
server:
  port: 8084
spring:
  shardingsphere:
    datasource:
      names: master,slave
      master:
        driver-class-name: com.mysql.jdbc.Driver
        password: root
        type: com.alibaba.druid.pool.DruidDataSource
        url: jdbc:mysql://192.168.0.105:3306/db_admin?characterEncoding=utf-8
        username: root
      slave:
        driver-class-name: com.mysql.jdbc.Driver
        password: root
        type: com.alibaba.druid.pool.DruidDataSource
        url: jdbc:mysql://192.168.0.104:3306/db_admin?characterEncoding=utf-8
        username: root
    masterslave:
      name: dataSource
      load-balance-algorithm-type: round_robin
      master-data-source-name: master
      slave-data-source-names: slave
    props:
      sql:
        show: true

mybatis:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.xxx.module.*.entity
```



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



# CentsOS 安装 ElasticSearch6

## Elasticsearch 安装

1. 解压并重命名

```shell
tar -zxvf elasticsearch-6.8.6.tar.gz
mv elasticsearch-6.8.6  /usr/local/elasticsearch
```

2. 修改配置

修改 config/elasticsearch.yml

```shell
network.host: 0.0.0.0  #改为0.0.0.0对外开放，如对特定ip开放则改为指定ip
http.port: 9200        #可更改端口不为9200
```

3. 启动 elasticSearch

**由于elasticsearch不允许root用户操作，所以需要建立子用户**

* 增加一个子用户

```
groupadd esgroup
useradd esuser -g esgroup -p espassword
```

* 赋权

```
chown -R esuser:esgroup /usr/local/elasticsearch
```

* 切换成子用户

```
su esuser
```

* 启动 (-d表示后台启动)

```
/usr/local/elasticsearch/bin/elasticsearch -d    
```

* 添加软链接

```shell
ln -s /usr/local/elasticsearch/bin/elasticsearch /usr/bin
```



## ElasticSearch 命令

* 查看所有索引

```
curl -X GET http://127.0.0.1:9200/_cat/indices?v  
```

* 创建索引

```
curl -X PUT http://127.0.0.1:9200/order
```

* 删除索引

```
curl -X DELETE http://127.0.0.1:9200/order
```

* 创建带有类型、映射的索引

入参：

```
{
	"settings": {
		"number_of_shards": 3,
		"number_of_replicas": 2
	},
	"mapping": {
		"_doc": {
			"properties": {
				"order_id": {
					"type": "long"
				},
				"order_no": {
					"type": "text"
				},
				"price": {
					"type": "double"
				}
			}
		}
	}
}
```

* 修改参数

```
PUT http://127.0.0.1:9200/order/_settings
```

入参

```
{
	"number_of_replicas": 3
}
```





## IK 分词器

* 添加 IK 分词器

将 压缩包解压到  /elasticsearch/plugins/ik 下，重启即可

```shell
unzip elasticsearch-analysis-ik-6.8.6.zip -d /usr/local/elasticsearch/plugins/ik
```

杀掉进程，重启

```shell
ps -ef|grep elasticsearch
/usr/local/elasticsearch/bin/elasticsearch -d
```

* 分词

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

* IK分词器 分词模式

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
* Git Parameters
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