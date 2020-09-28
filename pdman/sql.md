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

