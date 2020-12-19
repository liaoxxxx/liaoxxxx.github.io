####@Query 注解 的使用

###1.0 范例，参数

> 1.1.1  代码

````java 

package com.liaoxx.spring_hello.repository;
import com.liaoxx.spring_hello.entity.Admin;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import java.util.List;
import java.util.Map;

public interface AdminRepository extends JpaRepository<Admin,Integer> {

    @Query(value="SELECT ad.id AS id ," +
            "ad.username AS username ,"+
            "ad.nickname AS nicknaame,"+
            "ad.avatars  AS avatars,"+
            "ad.status   AS status "+
            "FROM  #{#entityName} ad  " +
            "LEFT JOIN  AdminRole ar ON ad.id = ar.adminId",nativeQuery = true)
    List<Map<String, Object>> getRoles();
}

````

> 1.1.2 参数1.  value = "查询语句"

> 1.1.3 参数2.  nativeQuery =  boolean  ,默认不开启 ，即  

> 1.1.4  #{#entityName}  为 [JpaRepository<Admin,Integer>]  中的 Admin （entity ） 的类型

> 1.1.5  


**********************************

###2.0错误范例


#####2.1.1 代 码
````java
    @Query(value = "SELECT rl.roleName " +
            "FROM  #{#entityName} ad  " +
            "LEFT JOIN  AdminRole ar ON ad.id = ar.adminId " +
            "LEFT JOIN Role rl ON ar.roleId = rl.id " +
            "WHERE ad.id = ?1")
    List<Map<String, Object>> getRoleNames(long adminId);
````
#####2.1.2 错误

```text
Resolved [org.springframework.http.converter.HttpMessageNotWritableException: Could not write JSON: Null key for a Map not allowed in JSON (use a converting NullKeySerializer?); nested exception is com.fasterxml.jackson.databind.JsonMappingException:


Null key for a Map not allowed in JSON (use a converting NullKeySerializer?)


(through reference chain: java.util.HashMap["data"]->org.springframework.validation.support.BindingAwareModelMap["roleList"]->java.util.ArrayList[0]->org.springframework.data.jpa.repository.query.AbstractJpaQuery$TupleConverter$TupleBackedMap["null"])]
DEBUG [http-nio-8081-exec-5] - {conn-10005} pool-recycle
```

#####2.1.3原因

> 1. Json 不能序列化  键名 为 null  的 map 对象 ，例中 "SELECT rl.roleName FROM  #{#entityName} ad  ...." 的SELECT 块获取表中字段时未 设键值。

```
@Query(value = "SELECT rl.roleName " +
            "FROM  #{#entityName} ad  " +
            "LEFT JOIN  AdminRole ar ON ad.id = ar.adminId " +
            "LEFT JOIN Role rl ON ar.roleId = rl.id " +
            "WHERE ad.id = ?1")
```


##### 2.1.4 解决方案： 

> 获取表中字段时 设置键值  ，如 ' SELECT  a  AS   aKeynName  FROM ......'


````java
  @Query(value = "SELECT rl.roleName as roleName " +      //设置键值  ' SELECT  a  AS   aKeynName  FROM ......
            "FROM  #{#entityName} ad  " +
            "LEFT JOIN  AdminRole ar ON ad.id = ar.adminId " +
            "LEFT JOIN Role rl ON ar.roleId = rl.id " +
            "WHERE ad.id = ?1")
    List<Map<String, Object>> getRoleNames(long adminId);
````






