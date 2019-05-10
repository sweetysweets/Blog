---
title: mysql一对多查询+分页的踩坑日记
date: 2019-04-24 17:40:20
tags:
- 日常踩坑
- database
---

今天app有一个奇怪的bug，数据变多之后显示的数据反而变少了，经过debug发现一个日常非常容易忽略的关于分页的错误，mark一下，后面不要再踩坑啦

分页时我们一般会用（limit index，pageSize）来做，当面对一对多查询时，我们一般会用笛卡尔乘积进行联合查询，使用mybatis映射到相应的resultMap上。例如：

<!--more-->

```mysql
  SELECT
        z.ID approvalid,
        z.SUPPLIERID,
        z.CREATETIME,
        z.LASTUPDATETIME,
        z.APPROVALUSERID,
        z.STATUS,
        a.id,
        a.modelId,
        a.assetCtyId,
        a.serialNum
    FROM
        ss_asset_approval z ,
        ss_asset_approval_items y
            LEFT JOIN ss_asset_pre a ON y.ASSRTID = a.ID
    WHERE
        z.ID = y.APPROVALID
      AND z.SUPPLIERID ='test1'
    LIMIT 0,10
```

但是这样会出现的问题是我们取得的前十条数据是乘之后的数据条数，其实是包含了子查询的条数，这样前台的分页数据就会出错。

我解决这个问题的办法是先分页，在联合进行自查询，如下：

重点： ==(select * from ss_asset_approval  ORDER BYLASTUPDATETIME DESC LIMIT 0,8) z,==

```mysql
  SELECT
        z.ID approvalid,
        z.SUPPLIERID,
        z.CREATETIME,
        z.LASTUPDATETIME,
        z.APPROVALUSERID,
        z.STATUS,
        a.id,
        a.modelId,
        a.assetCtyId,
        a.serialNum,
    FROM
        (select * from ss_asset_approval  ORDER BY
        LASTUPDATETIME DESC LIMIT 0,8) z,
        ss_asset_approval_items y
            LEFT JOIN ss_asset_pre a ON y.ASSRTID = a.ID
    WHERE
        z.ID = y.APPROVALID
      AND z.SUPPLIERID ='test1'
```

网上还有的解决办法是使用mybatis的collection中添加select关联查询

```mysql
  <resultMap id="teacherResultMap" type="com.oasis.test.entity.Teacher">
        <id column="id" property="id"/>
        <result column="teacher_name" property="name"/>
        <result column="age" property="age"/>
        <collection property="groupList" ofType="com.oasis.test.entity.Group" column="id"
                    select="com.oasis.test.mapper.GroupMapper.getGroupListByTeacherId">
        </collection>
    </resultMap>
    
    <select id="getTeacherList" parameterType="java.lang.Long"
            resultMap="teacherResultMap">
        SELECT
        ID,TEACHER_NAME,AGE
        FROM
        t_teacher limit 0,5
    </select>
```

```mysql
<mapper namespace="com.oasis.test.mapper.GroupMapper">
    <resultMap id="groupResultMap" type="com.oasis.test.entity.Group">
        <id column="id" property="id"/>
        <result column="group_name" property="name"/>
        <result column="number" property="number"/>
    </resultMap>
    <select id="getGroupListByTeacherId" parameterType="java.lang.Long"
            resultMap="groupResultMap">
        SELECT
       ID,GROUP_NAME,NUMBER
        FROM
        t_group WHERE teacher_id=#{id}
    </select>
</mapper>
```

例如以上方式，但这样写有个性能问题 n+1，如果分页查询有10条数据，那么在原本的一条sql的基础上会根据每条数据查询关联表， 那么会产生11条sql查询……

