# 系统模块

* 获取用户信息

```sql

```



* 获取父级菜单

```xml
<select id="getParentMenus" parameterType="java.util.List" resultMap="DetailResultMap">
    SELECT
        parent.*,
        child.id AS childId,
        child.parent_id AS childParentId,
        child.name AS childName,
        child.code AS childCode,
        child.path AS childPath,
        child.icon AS childIcon,
        child.sort AS childSort
    FROM
        sys_menu parent
        LEFT JOIN sys_menu child ON parent.id = child.parent_id
    <where>
        <if test="list != null and list.size > 0">
            AND parent.id IN
            <foreach collection="list" index="index" item="item" open="(" separator="," close=")">
                #{item}
            </foreach>
        </if>
        AND parent.parent_id IS NULL
    </where>
    ORDER BY
        parent.sort, child.sort
</select>
```

* 获取子菜单

```xml
<select id="getChildMenus" parameterType="java.util.List" resultMap="DetailResultMap">
    SELECT
        parent.*,
        child.id AS childId,
        child.parent_id AS childParentId,
        child.name AS childName,
        child.code AS childCode,
        child.path AS childPath,
        child.icon AS childIcon,
        child.sort AS childSort
    FROM
        sys_menu child
        INNER JOIN sys_menu parent ON parent.id = child.parent_id
    WHERE child.id IN
        <foreach collection="list" index="index" item="item" open="(" separator="," close=")">
            #{item}
        </foreach>
    ORDER BY
        child.sort ASC
</select>
```



* 区域信息表

```sql
DROP TABLE IF EXISTS `area2015`;
CREATE TABLE `area2015`  (
  `type` char(1)  NOT NULL,
  `code` char(12)  NOT NULL,
  `name` varchar(255)  NOT NULL,
  `parent_code` char(12) DEFAULT NULL,
  `short_code` varchar(12) NOT NULL,
  `catalog` varchar(255) DEFAULT NULL,
  `sub` smallint(6) DEFAULT NULL,
  PRIMARY KEY (`code`) USING BTREE,
  INDEX `code`(`code`) USING BTREE,
  INDEX `parent_code`(`parent_code`) USING BTREE
) ENGINE = MyISAM CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;
```

```java
@Data
public class Area {
    private String type;
    private String code;
    private String name;
    private String parentCode;
    private String shortCode;
}

@Data
public class Province extends Area {
    List<City> cities;
}

@Data
public class City extends Area {
    List<Region> regions;
}

@Data
public class Region extends Area {}
```

```java
@Mapper
public interface AreaMapper {
    List<Province> findAll();
}
```

```sql
<mapper namespace="org.example.mapper.AreaMapper">
    <resultMap id="DetailMap" type="org.example.entity.Province">
        <result property="type" column="province_type"/>
        <result property="name" column="province"/>
        <result property="code" column="province_code"/>
        <result property="shortCode" column="province_short_code"/>
        <collection property="cities" ofType="org.example.entity.City">
            <result property="type" column="city_type"/>
            <result property="name" column="city"/>
            <result property="code" column="city_code"/>
            <result property="shortCode" column="city_short_code"/>
            <collection property="regions" ofType="org.example.entity.Region">
                <result property="type" column="region_type"/>
                <result property="name" column="region"/>
                <result property="code" column="region_code"/>
                <result property="shortCode" column="region_short_code"/>
            </collection>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="DetailMap">
        SELECT
            t1.type as province_type,
            t1.name as province,
            t1.code as province_code,
            t1.short_code as province_short_code,
            t2.type as city_type,
            t2.name as city,
            t2.code as city_code,
            t2.short_code as city_short_code,
            t3.type as region_type,
            t3.name as region,
            t3.code as region_code,
            t3.short_code as region_short_code
        FROM area2015 t1
             LEFT JOIN area2015 t2 ON t2.parent_code = t1.code
             LEFT JOIN area2015 t3 ON t3.parent_code = t2.code
        WHERE t1.type = '1'
        ORDER BY t1.code;
    </select>
</mapper>   
```





# 多线程数据库重复插入、更新

常见操作，存在即更新，不存在则插入，该逻辑多线程情况下会重复插入

从 select  到 insert 期间，数据库并没有记录，会导致多个线程同时进入 insert 方法。

```
select * from user where username = 'xxx';
if(用户存在){
    update();
} else {
    insert();
}
```

1. ​	唯一索引

```java
select * from user where username = 'xxx';
if (用户存在) {  
    update();  
} else {   
    try {  
         insert();  //违反唯一性约束会报异常：InvocationTargetException
    } catch (InvocationTargetException e) {  
         update();  //如果重复插入已经有数据，则进行更新
     }
} 
```

2.  select for update

```java
begin;
select * from user where username = 'xxx' for update;  // 利用数据库行锁
if (用户存在) {  
    update();  
} else {   
    insert();
} 
commit;
```

**注意：查询字段  一定是主键或者索引，不然是锁表**

3. redis 分布式锁

```
boolean lock = setnx(username);  //等待锁
if(lock) {
	select * from user where username = 'xxx'
	if (用户存在) {  
    	update();  
    } else {   
        insert();
    } 
}
```





# 数据库锁

1.  select 无锁
2.  insert / update / delete  / select for update
   * 查询带主键 或 索引：行锁
   * 查询不带主键 或 索引：表锁







# 一对多查询方式

以字典组 和 字典项 为例，一个字段组下面有多个字典项

1. 分次查询

* 字典组

```java
@Data
public class SysDictGroup {
    private Long id;
    private String name;
    private String code;
    private List<SysDictItem> items;
}
```

```java
@Mapper
public interface SysDictGroupMapper {
    SysDictGroup findById(Long id);
}
```

```xml
<mapper namespace="org.example.mapper.SysDictGroupMapper">
    <resultMap id="BaseMap" type="org.example.entity.SysDictGroup">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="code" column="code"/>
        <collection property="items" ofType="org.example.entity.SysDictItem"
                    select="org.example.mapper.SysDictItemMapper.findByGroupId" column="id" />
    </resultMap>
    
    <select id="findById" resultMap="BaseMap">
        SELECT id, name, code
        FROM sys_dict_group WHERE id = #{id}
    </select>
</mapper>
```

* 字典项

```java
@Data
public class SysDictItem {
    private Long id;
    private Long groupId;
    private String name;
    private String code;
}
```

```java
@Mapper
public interface SysDictItemMapper {
    SysDictGroup findByGroupId(Long groupId);
}
```

```xml
<mapper namespace="org.example.mapper.SysDictItemMapper">
    <resultMap id="BaseMap" type="org.example.entity.SysDictItem">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="code" column="code"/>
        <result property="groupId" column="group_id"/>
    </resultMap>
    
    <select id="findByGroupId" resultMap="BaseMap">
        SELECT id, name, code, group_id
        FROM sys_dict_item
        WHERE group_id = #{groupId}
    </select>
</mapper>
```

2. 连表查询

```xml
<resultMap id="DetailMap" type="org.example.entity.SysDictGroup">
    <id property="id" column="id"/>
    <result property="name" column="name"/>
    <result property="code" column="code"/>
    <collection property="items" javaType="java.util.List" ofType="org.example.entity.SysDictItem">
        <id property="id" column="item_id"/>
        <id property="groupId" column="item_group_id"/>
        <result property="name" column="item_name"/>
        <result property="code" column="item_code"/>
    </collection>
</resultMap>

<select id="findById" resultMap="DetailMap">
    SELECT
        g.id,
        g.name,
        g.code,
        item.id as item_id,
        item.group_id as item_group_id,
        item.name as item_name,
        item.code as item_code
    FROM sys_dict_group g
    LEFT JOIN sys_dict_item item ON g.id = item.group_id
    WHERE g.id = #{id}
</select>
```





































