###### 1、模糊查询

```xml
and  p.platform_name like CONCAT('%',#{map.platformName},'%')
```

###### 2、in语法

参数是String[]的情况下

```xml
UPDATE zhgy_equipment SET region_id = #{regionId} WHERE id in
<foreach collection="ids" item="id" index="index" open="(" close=")" separator=",">
	#{id}
</foreach>
```

```java
int updateByRegion(@Param("regionId")String regionId,@Param("ids")String[] ids);
```

参数是List情况下

```xml
<if test="list!=null">
	 dep_id in
    <foreach collection="list" item="depart" open="(" separator="," close=")">
        #{depart.id}
    </foreach>
</if>
```

```java
List<SysUserDepart> listUserByDepartId(
    @Param("list") @org.apache.ibatis.annotations.Param("list") List<SysDepart> list);
```

参数是map的情况下

```xml
<if test="query!= null">
    <foreach collection="query.entrySet()" item="value"  index="key" >
    	and ${key} = #{value}
    </foreach>
</if>
```

```java
List<TreeSelectModel> queryTreeList(@Param("query") Map<String, String> query);
```



###### 3、查询最新日期的数据

```xml
SELECT
	*
FROM
	zhgy_scene_people
WHERE   
	create_date IN 
		( SELECT MAX( create_date ) FROM zhgy_scene_people GROUP BY scene_id)
	<if test="map.name != null and map.name != ''">
		 and scene_name  like CONCAT('%', #{map.name},'%')
	</if>                                                                             	  <if test="map.cb != null and map.cb != ''">                                  
 		AND DATE_FORMAT(create_date,'%Y-%m-%d') >= #{map.cb}
	</if>                                                                                 <if test="map.ce != null and map.ce != ''">
		AND DATE_FORMAT(create_date,'%Y-%m-%d')  <![CDATA[<=]]> #{map.ce}                </if>
GROUP BY    
scene_id
```

###### 4、查询最近90数据

```mysql
SELECT
	* 
FROM
	t_sp_daily_temp_report 
WHERE
	DATE_SUB( CURDATE(), INTERVAL 90 DAY ) <= report_date
```

