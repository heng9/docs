# HBase基本操作

## 命名空间

* 列出所有命名空间
```
list_namespace
```

* 查看命名空间
```
describe_namespace 'namespace'
```

* 新建命名空间
```
create_namespace 'namespace'
```

* 删除命名空间
```
drop_namespace 'namespace'
```

* 修改命名空间
```
alter_namespace 'namespace', {METHOD=>'set', 'PROPERTY_NAME'=>'new_namespace'}
```

**若没有指定 namespace, 则 namespace 默认为 default**


## 表

* 查看数据库中的所有表
```
list
```

* 查看表信息
```
describe 'namespace:table_name'
```

* 建表
```
create 'namespace:table_name', {NAME=>'cf1', VERSIONS=>3, TTL=>2592000}, {NAME=>'cf2', VERSIONS=>1}
create 'namespace:table_name', 'column_family'
```

* 新增列簇
```
alter 'table_name', {NAME=>'column_family', VERSIONS=>1}
```

* 删除列簇
```
alter 'table_name', {NAME=>'column_family', METHOD=>'delete'}
```

* 添加数据
```
put 'table_name', 'row_key', 'family:qualifier', 'value'
```

* 查看一行
```
get 'table_name', 'row_key', 'family:qualifier'
get 'table_name', 'row_key', {COLUMNS=>['family:qualifier', 'family:qualifier'], LIMIT=>3}
```

* 查看表
```
scan 'table_name'
```

* 删除某一行的某一列数据
```
delete 'table_name', 'row_key', 'family:qualifier'
```

* 删除某一行的数据
```
deleteall 'table_name', 'row_key'
```

* 删除某个表
```
disable 'table_name'
drop 'table_name'
```

* 统计记录数
```
count 'table_name'
```

* 清除表数据
```
truncate 'table_name'
```


# Phoenix 
    
* Phoenix JDBC 连接URL
```
jdbc:phoenix:zookeeper_quorum:2181:/hbase_znode or
jdbc:phoenix:zookeeper_quorum:/hbase_znode

zookeeper_quorum: 默认端口为2181，当有多个地址时，用","隔开
hbase_znode: 替换为 hbase or hbase-unsecured
```

* 创建表
```
CREATE TABLE `my_table` (
	ID varchar(20), 
	NAME varchar(20),
	AGE integer,
	CONSTRAINT pk PRIMARY KEY(ID, NAME)
) COMPRESSION='Snappy', SALT_BUCKETS=32;
```

**对于Phoenix来说，加了引号的话，不管是表还是字段名，会变成大小写敏感，不加的话，会统一转换成大写字母**


* 删除表
```
DROP TABLE IF EXISTS `my_table`;
```

* 创建序列
```
CREATE SEQUENCE `my_sequence` START WITH 100 INCREMENT BY 2 CACHE 10;
```

* 删除序列
```
DROP SEQUENCE `my_sequence`;
```

* 获取序列值
```
SELECT NEXT VALUE FOR `my_sequence`;
```

* 新增/插入数据
```
UPSERT INTO `my_table`(id, name) VALUES(next value for `my_sequence`, `value`);
```

* 删除数据
```
DELETE FROM `my_table` WHERE ID = `id`;
```

* 查询数据
```
SELECT * FROM `my_table` LIMIT 1000 OFFSET 100;
```

* 创建全局索引(Global Indexes)
```
CREATE INDEX `my_index` ON `my_table`(v1,v2)
```

* 创建覆盖索引(Covered Indexes)
```
CREATE INDEX `my_index` ON `my_table`(v1,v2) INCLUDE(v3)
```

* 删除索引
```
DROP INDEX `my_index` ON `my_table`
```

* 分页
```
limit size offset (page-1) * size
```