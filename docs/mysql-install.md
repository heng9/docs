# Windows 安装 mysql-5.6

* 添加环境变量

```
MYSQL_HOME:     D:\DevelopKit\mysql-5.6.44
PATH:           %MYSQL_HOME%\bin
```

* 新建my.ini

```shell
[mysqld]
port=3306    # 设置3306端口
basedir=D:\DevelopKit\mysql-5.6.44       # 设置mysql的安装目录
datadir=D:\DevelopKit\mysql-5.6.44\data  # 设置mysql数据库的数据的存放目录
max_connections=200            # 允许最大连接数
max_connect_errors=10          # 允许连接失败的次数。
character-set-server=utf8      # 服务端使用的字符集默认为UTF8
default-storage-engine=INNODB  # 创建新表时将使用的默认存储引擎
[mysql]
default-character-set=utf8     # 设置mysql客户端默认字符集
[client]
port=3306                      # 设置mysql客户端连接服务端时默认使用的端口
default-character-set=utf8
```

```
[mysqld]
port=3306
basedir=D:\DevelopKit\mysql-5.6.44
datadir=D:\DevelopKit\mysql-5.6.44\Data
max_connections=200
max_connect_errors=10
character-set-server=utf8
default-storage-engine=INNODB
[mysql]
default-character-set=utf8
[client]
port=3306
default-character-set=utf8
```

* 将mysql注册为windows系统服务

```
mysqld install MySQL --defaults-file="D:\DevelopKit\mysql-5.6.44-5.6.44\my.ini"
```

mysqld -remove MySQL

* 启动MySQL服务

```
net start mysql
```

* 初次登录mysql

```
mysql –uroot
```

* 修改密码

```sql
use mysql;
UPDATE user SET password=PASSWORD("root") WHERE user='root';
FLUSH PRIVILEGES;
QUIT;
```

* 登录mysql

```
mysql –uroot -p
```

* 查看MySQL用户信息
> 管理员root的host是localhost，代表仅限localhost登录访问。如果要允许开放其他ip登录，则需要添加新的host。
如果要允许所有ip访问，可以直接修改成 %

```sql
select user, host, authentication_string from mysql.user;
```

* 创建MySQL用户

```sql
CREATE USER 'xxx'@'%' IDENTIFIED BY 'password';

CREATE USER 'test_user'@'%' IDENTIFIED BY '123456';
```

* 授权远程数据库

```sql
GRANT ALL PRIVILEGES ON *.* TO 'xxx'@'%';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON *.* TO 'xxx'@'%';
FLUSH PRIVILEGES;

GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, ALTER ON *.* TO 'test_user'@'%';
```

* 查看MySQL用户权限

```sql
show grants for 'xxh'@'%';

show grants for 'test_user'@'%';
```


update user set authentication_string=password("root") where user="root";
	alter user user() identified by "root";