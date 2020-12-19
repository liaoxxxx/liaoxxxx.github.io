#使用spring data jpa  2020-02-02

>##1.在pom.xml 引入spring data jpa

````xml
  <!-- spring data jpa-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-start-data-jpa</artifactId>
        </dependency>
````

>##2.配置Jpa数据源  略

````properties
#jpa 配置
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.hbm2ddl.auto=update
spring.jpa.database-platform=org.hibernate.dialect.MySQL5Dialect

````

>##3.在 "entity" 包 创建【实体类】 

```java
package com.liaoxx.spring_hello.entity;
import javax.persistence.*;
@Entity     //标注为实体类
@Table(name="admin")      //标注表名为"admin";
public class Admin {
    @Id //标注为主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //标注为自增主键
    private int  id;


    @Column         //标注为字段
    private String username;
    @Column
    private String password;
    @Column
    private String salt;
    @Column
    private String nickname;
    @Column
    private byte is_delete;
    @Column
    private byte is_administrator;
    @Column
    private String avatars;
    @Column
    private byte status;

    @Column
    private int role_id;



    @OneToOne(cascade = CascadeType.ALL,targetEntity = AdminDetail.class)
    @JoinColumn(name = "admin_detail_id", referencedColumnName = "id", insertable = false, updatable = false)
    private AdminDetail adminDetail;

    /*省略 getter() setter() */
}

```


```java
package com.liaoxx.spring_hello.entity;

import javax.persistence.*;
import java.io.Serializable;

@Entity     //标注为实体类
@Table(name="admin_detail")      //标注表名为"admin";
public class AdminDetail implements Serializable {
    @Id //标注为主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)  //标注为自增主键
    private int  id;

    @Column
    private String phone;

    @Column
    private String email;

    @Column
    private byte sexy;

    @Column
    private String remark;

    @Column
    private int created_at;

    @Column
    private int updated_at;


    @Id
    @OneToOne(mappedBy="adminDetail", fetch=FetchType.EAGER) //
    private Admin admin;

}

```



>##4.创建Repository接口 继承JpaRepository

```java
package com.liaoxx.spring_hello.repository;

import com.liaoxx.spring_hello.entity.Admin;
import org.springframework.data.jpa.repository.JpaRepository;
//继承JpaRepository 来完成对数据库的操作
public interface AdminRepository extends JpaRepository<Admin,Integer> {
}
```

>##5.使用 Repository接口 操作

````java
package com.liaoxx.spring_hello.controller;

import com.liaoxx.spring_hello.entity.Admin;
import com.liaoxx.spring_hello.repository.AdminRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import java.util.Map;

//@RestController //注解无法返回视图，默认返回JSON数据。
@Controller
@RequestMapping("/admin")
public class AdminController {
   
    @Autowired
    AdminRepository adminRepository;

    @GetMapping("/find/{id}")
    public String findOne(@PathVariable("id") Integer  id ,Map map){
        Admin admin=adminRepository.getOne(id);
        System.out.println(admin);
        map.put("admin",admin);
        return map;
    }
}
````



###6.@OneToOne   一对一主键关联  Admin(entity) 关联 AdminDetail(entity) 

- ####6. 1.主表 Admin 为例,
`````text
    @OneToOne(cascade = CascadeType.ALL,targetEntity = AdminDetail.class)
    @JoinColumn(name = "admin_detail_id", referencedColumnName = "id", insertable = false, updatable = false)
    //targetEntity =  AdminDetail.class     即目标实体类 为AdminDetail.class
    //name = "admin_detail_id"              即关联字段为Admin表的  'admin_detail_id'    
    //referencedColumnName = "id"           即 引用映射到 AdminDetail 的 主键 id  
    private AdminDetail adminDetail;
`````
- ####6.2.关联附表 AdminDetail  
```java
    @Id
    @OneToOne(mappedBy="adminDetail", fetch=FetchType.EAGER)   
    //mappedBy="adminDetail"   即映射到 Admin.adminDetail 字段
    private Admin admin;
```

- ####6.3.0出现的错误

- #####6.3.1 @Id注解(javax.persistence.Id)需要 实现接口 【Serializable】 接口
````text
org.springframework.beans.factory.BeanCreationException:
Error creating bean with name 'entityManagerFactory' defined in class path resource 
[org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: 
Invocation of init method failed; nested exception is javax.persistence.PersistenceException: 
[PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.MappingException:

Composite-id class must implement Serializable:
 
com.liaoxx.spring_hello.entity.AdminDetail
````

>修正后：


````java
package com.liaoxx.spring_hello.entity;

import javax.persistence.*;
import java.io.Serializable;
@Entity     //标注为实体类
@Table(name="admin_detail")      //标注表名为"admin_detail";
public class AdminDetail implements Serializable {   // @Id  即 【javax.persistence.Id】 需要实现 Serializable 接口


}
````
    



#####6.3.2 忘了233
>在数据库中 Table: 【admin_detail】  重复定义了字段  【admin_id】  或 【adminId】
```text
Invocation of init method failed; nested exception is org.hibernate.DuplicateMappingException: Table [admin_detail] contains physical column name [admin_id] referred to by multiple physical column names: [admin_id], [adminId]
```
