* entity.java

```velocity
##引入宏定义
$!define
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))
##使用宏定义设置回调（保存位置与文件后缀）
#save("/entity", ".java")
##使用宏定义设置包后缀
#setPackageSuffix("entity")
##使用全局变量实现默认包导入
$!autoImport

import com.hengsoft.repository.core.model.Model;
import lombok.Data;

@Data
public class $!{tableInfo.name} extends Model {

#set($otherColumn = $tool.newArrayList("id", "deleted", "disabled", "createTime", "editTime", "creator", "editor"))
#foreach($column in $tableInfo.fullColumn)
#if(! $otherColumn.contains($!{column.name}))
#if(${column.comment})
    //${column.comment}
#end
    private $!{tool.getClsNameByFullName($column.type)} $!{column.name};
#end
#end

}
```

* mapper.java

```velocity
##引入宏定义
$!define
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))
##定义初始变量
#set($mapperName = $tool.append($tableInfo.name, "Mapper"))
#set($entityPackage = $tool.append($tableInfo.savePackageName, ".entity.", $tableInfo.name))
##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end
##设置回调
$!callback.setFileName($tool.append($mapperName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/mapper"))

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}mapper;

import com.hengsoft.repository.core.mapper.BaseMapper;
import $!{entityPackage};
import org.springframework.stereotype.Repository;

@Repository
public interface $!{mapperName} extends BaseMapper<$!{pk.shortType}, $!{tableInfo.name}> {

}
```

* mapper.xml

```xml
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))

##引入mybatis支持
$!mybatisSupport

##设置保存名称与保存位置
$!callback.setFileName($tool.append($!{tableInfo.name}, "Mapper.xml"))
$!callback.setSavePath($tool.append($modulePath, "/src/main/resources/mappers"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="$!{tableInfo.savePackageName}.mapper.$!{tableInfo.name}Mapper">

    <resultMap id="BaseResultMap" type="$!{tableInfo.savePackageName}.entity.$!{tableInfo.name}">
#foreach($column in $tableInfo.fullColumn)
        <result property="$!column.name" column="$!column.obj.name" jdbcType="$!column.ext.jdbcType"/>
#end
    </resultMap>

    <sql id="columns">
#foreach($column in $tableInfo.fullColumn)
        $!column.obj.name#if($velocityHasNext), #end
    
#end
    </sql>

    <!--通过主键查询-->
    <select id="selectByPrimaryKey" resultMap="BaseResultMap">
        SELECT <include refid="columns" />
        FROM $!tableInfo.obj.name
        WHERE $!pk.obj.name = #{$!pk.name}
    </select>

    <!--通过实体查询-->
    <select id="selectList" parameterType="$!{tableInfo.savePackageName}.entity.$!{tableInfo.name}" resultMap="BaseResultMap">
        SELECT <include refid="columns" />
        FROM $!tableInfo.obj.name
        <where>
#foreach($column in $tableInfo.fullColumn)
            <if test="$!column.name != null#if($column.type.equals("java.lang.String")) and $!column.name != ''#end">
                and $!column.obj.name = #{$!column.name}
            </if>
#end
        </where>
        ORDER BY $!pk.name desc
    </select>

    <!--新增所有列-->
    <insert id="insert" keyProperty="$!pk.name" useGeneratedKeys="true">
        INSERT INTO $!{tableInfo.obj.name}(
#foreach($column in $tableInfo.otherColumn)
            $!column.obj.name#if($velocityHasNext), #end

#end
        ) VALUES (
#foreach($column in $tableInfo.otherColumn)
            #{$!{column.name}}#if($velocityHasNext), #end

#end
        )
    </insert>
    
    <!--批量新增数据-->
    <insert id="insertBatch" keyProperty="$!pk.name" useGeneratedKeys="true">
        INSERT INTO $!{tableInfo.obj.name}(
#foreach($column in $tableInfo.otherColumn)
            $!column.obj.name#if($velocityHasNext), #end

#end
        ) VALUES 
        <foreach collection ="list" index= "index" item="item" separator=",">
        (
#foreach($column in $tableInfo.otherColumn)
            #{item.$!{column.name}}#if($velocityHasNext), #end

#end
        )
        </foreach>
    </insert>

    <!--动态新增所有列-->
    <insert id="insertSelective" keyProperty="$!pk.name" useGeneratedKeys="true">
        INSERT INTO $!{tableInfo.obj.name}(
#foreach($column in $tableInfo.otherColumn)
            <if test="$!column.name != null#if($column.type.equals("java.lang.String")) and $!column.name != ''#end">
                $!column.obj.name#if($velocityHasNext), #end
                
            </if>
#end
        ) VALUES (
#foreach($column in $tableInfo.otherColumn)
            <if test="$!column.name != null#if($column.type.equals("java.lang.String")) and $!column.name != ''#end">
                #{$!{column.name}}#if($velocityHasNext), #end
                
            </if>
#end
        )
    </insert>

    <!--通过主键修改数据-->
    <update id="updateByPrimaryKey">
        UPDATE $!{tableInfo.obj.name}
        <set>
#foreach($column in $tableInfo.otherColumn)
            $!column.obj.name = #{$!column.name},
#end
        </set>
        WHERE $!pk.obj.name = #{$!pk.name}
    </update>

    <!--通过主键动态修改数据-->
    <update id="updateByPrimaryKeySelective">
        UPDATE $!{tableInfo.obj.name}
        <set>
#foreach($column in $tableInfo.otherColumn)
            <if test="$!column.name != null#if($column.type.equals("java.lang.String")) and $!column.name != ''#end">
                $!column.obj.name = #{$!column.name},
            </if>
#end
        </set>
        WHERE $!pk.obj.name = #{$!pk.name}
    </update>

    <!--通过主键删除-->
    <delete id="deleteByPrimaryKey">
        DELETE FROM $!{tableInfo.obj.name} 
        WHERE $!pk.obj.name = #{$!pk.name}
    </delete>

    <!--通过主键批量删除-->
    <delete id="deleteByPrimaryKeys">
        DELETE FROM $!{tableInfo.obj.name} 
        WHERE $!pk.obj.name in
        <foreach collection="ids" index="index" item="item" open="(" separator="," close=")">
            #{item}
        </foreach>
    </delete>
    
</mapper>
```

* service.java

```velocity
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))

##定义初始变量
#set($serviceName = $tool.append($tableInfo.name, "Service"))
#set($dtoName = $tool.append($tableInfo.name, "DTO"))
#set($dtoNameLowerCase = $tool.firstLowerCase($dtoName))
#set($dtoPackage = $tool.append($tableInfo.savePackageName, ".dto.", $dtoName))
#set($queryName = $tool.append($tableInfo.name, "Query"))
#set($queryPackage = $tool.append($tableInfo.savePackageName, ".dto.", $queryName))
##设置回调
$!callback.setFileName($tool.append($serviceName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/service"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}service;

import com.hengsoft.common.model.Pagination;
import $!{dtoPackage};
import $!{queryPackage};
import java.util.List;

public interface $!{serviceName} {

    /**
     * 查询数据详情
     */
    $!{dtoName} detail($!pk.shortType $!pk.name);

    /**
     * 分页查询数据
     */
    Pagination<$!{dtoName}> list($!{queryName} query);

    /**
     * 新增数据
     */
    $!{dtoName} insert($!{dtoName} $!{dtoNameLowerCase});

    /**
     * 修改数据
     */
    $!{dtoName} update($!{dtoName} $!{dtoNameLowerCase});

    /**
     * 通过主键批量删除数据
     */
    int deleteByIds(List<$!pk.shortType> ids);

}
```

* serviceImpl.java

```velocity
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))

##定义初始变量
#set($serviceImplName = $tool.append($tableInfo.name, "ServiceImpl"))
#set($serviveName = $tool.append($tableInfo.name, "Service"))
#set($servicePackage = $tool.append($tableInfo.savePackageName, ".service.", $serviveName))
#set($entityName = $tableInfo.name)
#set($entityPackage = $tool.append($tableInfo.savePackageName, ".entity.", $entityName))
#set($mapperName = $tool.append($tableInfo.name, "Mapper"))
#set($mapperNameLowerCase = $tool.firstLowerCase($mapperName))
#set($mapperPackage = $tool.append($tableInfo.savePackageName, ".mapper.", $mapperName))
#set($dtoName = $tool.append($tableInfo.name, "DTO"))
#set($dtoNameLowerCase = $tool.firstLowerCase($dtoName))
#set($dtoPackage = $tool.append($tableInfo.savePackageName, ".dto.", $dtoName))
#set($queryName = $tool.append($tableInfo.name, "Query"))
#set($queryPackage = $tool.append($tableInfo.savePackageName, ".dto.", $queryName))
##设置回调
$!callback.setFileName($tool.append($serviceImplName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/service/impl"))

##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}service.impl;

import com.github.pagehelper.Page;
import com.github.pagehelper.PageHelper;
import com.hengsoft.common.model.Pagination;
import com.hengsoft.common.util.BeanUtil;
import com.hengsoft.repository.util.PageUtil;
import $!{entityPackage};
import $!{dtoPackage};
import $!{queryPackage};
import $!{mapperPackage};
import $!{servicePackage};
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * $!{tableInfo.comment}($!{tableInfo.name})表服务实现类
 *
 * @author $!author
 * @since $!time.currTime()
 */
@Service("$!tool.firstLowerCase($!{tableInfo.name})Service")
public class $!{serviceImplName} implements $!{tableInfo.name}Service {
    
    @Autowired
    private $!{mapperName} $!{mapperNameLowerCase};

    @Override
    public $!{dtoName} detail($!pk.shortType $!pk.name) {
        $!{entityName} entity = $!{mapperNameLowerCase}.selectByPrimaryKey($!pk.name);
        return BeanUtil.map(entity, $!{dtoName}.class);
    }

    @Override
    public Pagination<$!{dtoName}> list($!{queryName} query) {
        $!{entityName} entity = BeanUtil.map(query, $!{entityName}.class);
        Page<$!{entityName}> page = PageHelper.startPage(query.getCurrent(), query.getSize())
                .doSelectPage(() -> $!{mapperNameLowerCase}.selectAll(entity));
        return PageUtil.toPage(page, $!{dtoName}.class);
    }

    @Override
    public $!{dtoName} insert($!{dtoName} $!{dtoNameLowerCase}) {
        $!{entityName} entity = BeanUtil.map($!{dtoNameLowerCase}, $!{entityName}.class);
        entity.setDefaultValue();
        $!{mapperNameLowerCase}.insertSelective(entity);
        return BeanUtil.map(entity, $!{dtoName}.class);
    }

    @Override
    public $!{dtoName} update($!{dtoName} $!{dtoNameLowerCase}) {
        $!{entityName} entity = BeanUtil.map($!{dtoNameLowerCase}, $!{entityName}.class);
        entity.setUpdateValue();
        $!{mapperNameLowerCase}.updateByPrimaryKeySelective(entity);
        return BeanUtil.map(entity, $!{dtoName}.class);
    }

    @Override
    public int deleteByIds(List<$!pk.shortType> ids) {
        return $!{mapperNameLowerCase}.deleteByPrimaryKeys(ids);
    }
}
```

* controller.java

```velocity
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))

##定义初始变量
#set($controllerName = $tool.append($tableInfo.name, "Controller"))
#set($serviceName = $tool.append($tableInfo.name, "Service"))
#set($serviceNameLowerCase = $!tool.firstLowerCase($serviceName))
#set($servicePackage = $tool.append($tableInfo.savePackageName, ".service.", $serviceName))
#set($dtoName = $tool.append($tableInfo.name, "DTO"))
#set($dtoNameLowerCase = $!tool.firstLowerCase($dtoName))
#set($dtoPackage = $tool.append($tableInfo.savePackageName, ".dto.", $dtoName))
#set($queryName = $tool.append($tableInfo.name, "Query"))
#set($queryPackage = $tool.append($tableInfo.savePackageName, ".dto.", $queryName))
##设置回调
$!callback.setFileName($tool.append($controllerName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/controller"))
##拿到主键
#if(!$tableInfo.pkColumn.isEmpty())
    #set($pk = $tableInfo.pkColumn.get(0))
#end

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}controller;

import com.hengsoft.common.model.Pagination;
import com.hengsoft.common.model.Response;
import $!{dtoPackage};
import $!{queryPackage};
import $!{servicePackage};
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/api/$!tool.firstLowerCase($tableInfo.name)")
public class $!{controllerName} {
    
    @Autowired
    private $!{serviceName} $!{serviceNameLowerCase};

    @GetMapping("/detail")
    public Response<$!{dtoName}> detail($!pk.shortType id) {
        return Response.ok($!{serviceNameLowerCase}.detail(id));
    }
    
    @GetMapping("/list")
    public Response<Pagination<$!{dtoName}>> list($!{queryName} query) {
        return Response.ok($!{serviceNameLowerCase}.list(query));
    }
    
    @PostMapping("/insert")
    public Response<$!{dtoName}> insert(@RequestBody @Valid $!{dtoName} $!{dtoNameLowerCase}) {
        return Response.ok($!{serviceNameLowerCase}.insert($!{dtoNameLowerCase}));
    }
    
    @PostMapping("/update")
    public Response<$!{dtoName}> update(@RequestBody @Valid $!{dtoName} $!{dtoNameLowerCase}) {
        return Response.ok($!{serviceNameLowerCase}.update($!{dtoNameLowerCase}));
    }
    
    @GetMapping("/delete")
    public Response<Integer> delete(List<$!{pk.shortType}> ids) {
        return Response.ok($!{serviceNameLowerCase}.deleteByIds(ids));
    }

}
```

* dto.java

```velocity
##引入宏定义
$!define
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))
##定义初始变量
#set($dtoName = $tool.append($tableInfo.name, "DTO"))
##设置回调
$!callback.setFileName($tool.append($dtoName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/dto"))

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}dto;

$!autoImport
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

@Data
@ApiModel("$!{tableInfo.comment}DTO类")
public class $!{dtoName} {
#set($otherColumn = $tool.newArrayList("deleted", "disabled", "createTime", "editTime", "creator", "editor"))
#foreach($column in $tableInfo.fullColumn)
    #if(! $otherColumn.contains($!{column.name}))
        
    @ApiModelProperty(notes = "$!{column.comment}")
    private $!{tool.getClsNameByFullName($column.type)} $!{column.name};
#end
#end

}
```

* query.java

```velocity
##引入宏定义
$!define
## 去掉表的前缀
$!tableInfo.setName($tool.getClassName($tableInfo.obj.name.substring($tableInfo.obj.name.indexOf("_"))))
##定义初始变量
#set($queryName = $tool.append($tableInfo.name, "Query"))
##设置回调
$!callback.setFileName($tool.append($queryName, ".java"))
$!callback.setSavePath($tool.append($tableInfo.savePath, "/dto"))

#if($tableInfo.savePackageName)package $!{tableInfo.savePackageName}.#{end}dto;

$!autoImport
import com.hengsoft.repository.core.model.BaseQuery;
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.Data;

@Data
@ApiModel("$!{tableInfo.comment}查询类")
public class $!{queryName} extends BaseQuery {
#set($otherColumn = $tool.newArrayList("id", "deleted", "disabled", "createTime", "editTime", "creator", "editor"))
#foreach($column in $tableInfo.fullColumn)
#if(! $otherColumn.contains($!{column.name}))
    @ApiModelProperty(notes = "$!{column.comment}")
    private $!{tool.getClsNameByFullName($column.type)} $!{column.name};
#end
#end

}
```



t_demo 表样例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hengsoft.module.demo.mapper.DemoMapper">

	<resultMap id="BaseResultMap" type="com.hengsoft.module.demo.entity.Demo">
		<id column="id" property="id"/>
		<result column="username"  property="username"/>
		<result column="password" property="password"/>
		<result column="gender" property="gender"/>
		<result column="birthday" property="birthday"/>
		<result column="create_time" property="createTime"/>
		<result column="edit_time" property="editTime"/>
		<result column="creator" property="creator"/>
		<result column="editor" property="editor"/>
		<result column="deleted" property="deleted"/>
		<result column="disabled" property="disabled"/>
	</resultMap>

	<sql id="columns">
		id,
		username,
		password,
		gender,
		birthday,
		create_time,
		edit_time,
		creator,
		editor,
		deleted,
		disabled
	</sql>

	<select id="selectByPrimaryKey" parameterType="int" resultMap="BaseResultMap">
		SELECT <include refid="columns" />
		FROM t_demo
		WHERE id = #{id} AND
			deleted = FALSE
	</select>

	<select id="selectList" parameterType="com.hengsoft.module.demo.entity.Demo" resultMap="BaseResultMap">
		SELECT <include refid="columns" />
		FROM t_demo
		<where>
			<if test="id != null">
				AND id = #{id}
			</if>
			<if test="username != null and username !=''">
				AND username = #{username}
			</if>
			<if test="password != null and password !=''">
				AND password = #{password}
			</if>
			<if test="gender != null and gender !=''">
				AND gender = #{gender}
			</if>
			<if test="birthday != null">
				AND birthday = #{birthday}
			</if>
		</where>
	</select>

	<insert id="insert" parameterType="com.hengsoft.module.demo.entity.Demo"
			useGeneratedKeys="true" keyColumn="id" keyProperty="id">
		INSERT INTO t_demo(
			username,
			password,
			gender,
			birthday,
			create_time,
			edit_time,
			creator,
			editor,
			deleted,
			disabled
		) values(
			#{username},
			#{password},
			#{gender},
			#{birthday},
			#{createTime},
			#{editTime},
			#{creator},
			#{editor},
			#{deleted},
			#{disabled}
		)
	</insert>

	<insert id="insertBatch" parameterType="java.util.List" useGeneratedKeys="true" keyColumn="id" keyProperty="id">
		INSERT INTO t_demo(
			username,
			password,
			gender,
			birthday,
			create_time,
			edit_time,
			creator,
			editor,
			deleted,
			disabled
		) values
		<foreach collection ="list" index= "index" item="item" separator=",">
		(
			#{item.username},
			#{item.password},
			#{item.gender},
			#{item.birthday},
			#{item.createTime},
			#{item.editTime},
			#{item.creator},
			#{item.editor},
			#{item.deleted},
			#{item.disabled}
		)
		</foreach>
	</insert>

	<insert id="insertSelective" parameterType="com.hengsoft.module.demo.entity.Demo" useGeneratedKeys="true" keyColumn="id" keyProperty="id">
		INSERT INTO t_demo
		<trim prefix="(" suffix=")" suffixOverrides=",">
			<if test="id != null">
				id,
			</if>
			<if test="username != null and username != ''">
				username,
			</if>
			<if test="password != null and password != ''">
				password,
			</if>
			<if test="gender != null and gender != ''">
				gender,
			</if>
			<if test="birthday != null">
				birthday,
			</if>
			<if test="createTime != null">
				create_time,
			</if>
			<if test="editTime != null">
				edit_time,
			</if>
			<if test="creator != null and creator != ''">
				creator,
			</if>
			<if test="editor != null and editor != ''">
				editor,
			</if>
			<if test="deleted != null">
				deleted,
			</if>
			<if test="disabled != null">
				disabled,
			</if>
		</trim>
		<trim prefix="values (" suffix=")" suffixOverrides=",">
			<if test="id != null">
				#{id},
			</if>
			<if test="username != null and username != ''">
				#{username},
			</if>
			<if test="password != null and password != ''">
				#{password},
			</if>
			<if test="gender != null and gender != ''">
				#{gender},
			</if>
			<if test="birthday != null">
				#{birthday},
			</if>
			<if test="createTime != null">
				#{createTime},
			</if>
			<if test="editTime != null">
				#{editTime},
			</if>
			<if test="creator != null and creator != ''">
				#{creator},
			</if>
			<if test="editor != null and editor != ''">
				#{editor},
			</if>
			<if test="deleted != null">
				#{deleted},
			</if>
			<if test="disabled != null">
				#{disabled},
			</if>
		</trim>
	</insert>

	<update id="updateByPrimaryKey" parameterType="com.hengsoft.module.demo.entity.Demo">
		UPDATE t_demo SET
			username = #{username},
			password = #{password},
			gender = #{gender},
			birthday = #{birthday},
			create_time = #{createTime},
			edit_time = #{editTime},
			creator = #{creator},
			editor = #{editor},
			deleted = #{deleted},
			disabled = #{disabled}
		WHERE id = #{id}
	</update>

	<update id="updateByPrimaryKeySelective" parameterType="com.hengsoft.module.demo.entity.Demo">
		UPDATE t_demo
		<set>
			<if test="username != null and username != ''">
				username = #{username},
			</if>
			<if test="password != null and password != ''">
				password = #{password},
			</if>
			<if test="gender != null and gender != ''">
				gender = #{gender},
			</if>
			<if test="birthday != null">
				birthday = #{birthday},
			</if>
			<if test="createTime != null">
				create_time = #{createTime},
			</if>
			<if test="editTime != null">
				edit_time = #{editTime},
			</if>
			<if test="creator != null and creator !=''">
				creator = #{creator},
			</if>
			<if test="editor != null and editor !=''">
				editor = #{editor},
			</if>
			<if test="deleted != null">
				deleted = #{deleted},
			</if>
			<if test="disabled != null">
				disabled = #{disabled}
			</if>
		</set>
		WHERE id = #{id}
	</update>

	<delete id="deleteByPrimaryKey" parameterType="java.util.List">
		DELETE FROM t_demo
		WHERE id = #{id}
	</delete>

	<delete id="deleteByPrimaryKeys" parameterType="java.util.List">
		DELETE FROM t_demo
		WHERE id IN
		<foreach collection="ids" index="index" item="item" open="(" separator="," close=")">
			#{item}
		</foreach>
	</delete>

	<update id="disable" parameterType="int">
		UPDATE t_demo
		<set>
			<if test="editTime != null">
				edit_time = #{editTime},
			</if>
			<if test="editor != null and editor !=''">
				editor = #{editor},
			</if>
			disabled = !disabled
		</set>
		WHERE id = #{id}
	</update>

</mapper>

```





