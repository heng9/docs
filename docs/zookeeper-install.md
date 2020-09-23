# Zookeeper单机模式安装
1. 配置JAVA环境

2. 下载并解压zookeeper
```
tar -zxvf zookeeper-3.4.12.tar.gz
```

3. 重命名配置文件zoo_sample.cfg
```
cp conf/zoo_sample.cfg conf/zoo.cfg
```

4. 启动zookeeper
```
bin/zkServer.sh start
```

5. 检测是否成功启动，用zookeeper客户端连接下服务端
```
bin/zkCli.sh
```

## Zookeeper UI
[https://github.com/DeemOpen/zkui](https://github.com/DeemOpen/zkui)