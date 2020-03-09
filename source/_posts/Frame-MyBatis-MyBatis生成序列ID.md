---
title: MyBatis生成序列ID
date: 2019-09-16 23:50:21
tags:
- MyBatis
categories: 
- ORM框架
keywors: 
description: 
top_img: 
cover: /img/mybatis.jpg
---

```java
//调用代码：
Long id = sequenceDao.getNextVal();

//持久化层
public interface SequenceDao{
    /**
    * 得到下一个sequence值
    */
    Long getNextVal();
}
```

```xml
<mapper namespace="com.huawei.pano.dao.SequenceDao">
    <!-- 得到下一个序列id -->
    <select id="getNextVal" resultType="java.lang.Long">
    SELECT DATE_FORMAT(NOW();%Y%m%d)*100000000+NEXTVAL();
    </select>
</mapper>
```