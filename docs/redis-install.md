# windows安装redis
* 下载地址: https://github.com/MSOpenTech/redis/releases


* 配置说明
    * redis-server.exe：服务端程序，提供redis服务
    * redis-cli.exe: 客户端程序，通过它连接redis服务并进行操作
    * redis-check-dump.exe：本地数据库检查
    * redis-check-aof.exe：更新日志检查
    * redis-benchmark.exe：性能测试，用以模拟同时由N个客户端发送M个 SETs/GETs 查询 (类似于 Apache 的ab 工具).
    * redis.windows.conf: 配置文件，将redis作为普通软件使用的配置，命令行关闭则redis关闭
    * redis.windows-service.conf：配置文件，将redis作为系统服务的配置，用以区别开两种不同的使用方式


* 安装为windows服务
```
redis-server --service-install redis.windows.conf
```

* 启动服务
```
redis-server --service-start
```

* 进入终端
```
redis-cli.exe
redis-cli.exe -h 127.0.0.1 -p 6379
```

* 停止服务
```
redis-server --service-stop
```

* 卸载服务
```
redis-server --service-uninstall
```
