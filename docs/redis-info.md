### Redis数据类型
* string(字符串)

```
SET key_name value
GET key_name
SETNX key_name value (在指定的 key 不存在时，为 key 设置指定的值)

MSET key1 value1 key2 value2 ...keyN valueN
MGET key1 key2 ... keyn
MSETNX key1 value1 key2 value2 ...keyN valueN

SETEX key_name timeout value (为指定的 key 设置值及其过期时间seconds)
TTL key_name

INCR key_name (如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCR 操作)
INCRBY key_name incr_amount (将 key 中储存的数字加上指定的增量值)

DECR key_name 
DECRBY key_name decrement_amount
```

```
stringRedisTemplate.opsForValue().setIfAbsent("key", "value");  //SETNX
```

* hash(哈希)
> Redis hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象

```
HSET key_name field value 
HGET key_name field_name
HGETALL key_name
HDEL key_name field1 ...fieldn
HSETNX key_name field value (用于为哈希表中不存在的的字段赋值)

HMSET key_name field1 value1 ...fieldn valuen
HMGET key_name field1 ...fieldn
```

```
stringRedisTemplate.opsForHash().putIfAbsent("hash", "key", "value");  //HSETNX
```

* list(列表)
> 列表是简单的字符串列表，按照插入顺序排序

```
LLEN key_name
LRANGE key_name 0 -1
LPUSH key_name value1 ...valuen (将一个或多个值插入到列表头部)
RPOP key_name (移除列表的最后一个元素，返回值为移除的元素)
```

```
stringRedisTemplate.opsForList().leftPush("key", "value");
```

* set(集合)
> Set 是 String 类型的无序集合。集合成员是唯一的

```
SCARD key_name 
SMEMBERS key_name
SADD key_name value1 ...valuen
SPOP key_name [count] (移除集合中的指定 key 的一个或多个随机元素，移除后会返回移除的元素)
```



* zset(sorted set：有序集合)
> 每个元素都会关联一个double类型的分数。redis通过分数来为集合中的成员进行从小到大的排序。
有序集合的成员是唯一的,但分数(score)却可以重复。

```
ZCARD key_name
ZRANGE key_name 0 -1
ZADD key_name score1 value1 ... scoren valuen (将一个或多个成员元素及其分数值加入到有序集当中)
ZINCRBY key_name increment value (对有序集合中指定成员的分数加上增量 increment)
ZCOUNT key_name min max (计算有序集合中指定分数区间的成员数量)
```

### Redis安全
* 查看是否设置了密码验证

```
CONFIG get requirepass
```

* 设置密码

```
CONFIG set requirepass "password"
```

### Redis事务
> 以 MULTI 开始一个事务，然后将多个命令入队到事务中，最后由 EXEC 命令触发事务

```
MULTI
...
EXEC
```
