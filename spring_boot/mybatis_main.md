###MyBatis

####使用Mybatis  参照[Spring Boot（六）集成 MyBatis 操作 MySQL 8](https://segmentfault.com/a/1190000016374807)

###1.注解版 MyBatis 集成
> 1.1在pom.xml 文件 添加  Mybatis依赖包 以及相应的数据库驱动  [注意 低版本会出现 @MapperScan注解无法使用](https://blog.csdn.net/hzr0523/article/details/80191909) 

```xml
      <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>1.3.2</version>
           </dependency>

        <!--引入mysql 驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```

 JPA、MyBatis还有Hibernate
> 1.2 在application.properties 文件  配置 mapper 包名
> 
> 注【Mapper是MyBatis的核心，是SQL存储的地方，也是配置数据库映射的地方。】

```text
#配置mapper包名
mybatis.type-aliases-package=com.liaoxx.spring_hello.mapper
```


> 1.3 在程序入口 【 main( ) 】 添加注解@MapperScan("com.liaoxx.spring_hello.mapper")  |   直接在启动文件SpringbootApplication.java的类上配置@MapperScan，这样就可以省去，单独给每个Mapper上标识@Mapper的麻烦。

````java
package com.liaoxx.spring_hello;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
@MapperScan("com.liaoxx.spring_hello.mapper")  //@MapperScan，可以省去单独给每个Mapper上标识@Mapper的麻烦。
@SpringBootApplication
public class SpringHelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringHelloApplication.class, args);
    }
}
````

> 1.4 创建实体类 【Model】

````java
package com.liaoxx.spring_hello.model;
import java.io.Serializable;
public class UserModel implements Serializable {

    private long  id;
    private String username;

    private String password;
    private String salt;

    private String nickname;
    private byte isDelete;

    private String avatars;
    private byte status;

    @Override
    public String toString(){
        return  "Admin:@id:"+this.getId()+";\r\n"
                +"@username: "+this.getId()+";\r\n"

                +"@password: "+this.getPassword()+";\r\n"
                +"@salt: "+this.getSalt()+";\r\n"

                +"@nickname: "+this.getNickname()+";\r\n"
                +"@isDelete: "+this.getIsDelete()+";\r\n"

                +"@avatars: "+this.getAvatars()+";\r\n"
                +"@status: "+this.getStatus()+";\r\n";
    }
    //省略其对应 get set方法
}
````

> 1.5 创建mapper接口


````java
package com.liaoxx.spring_hello.mapper;

import com.liaoxx.spring_hello.model.UserModel;
import org.apache.ibatis.annotations.*;
import java.util.List;
public interface UserMapper {
    @Select("select * from user")
    @Results({
            @Result(property = "isDelete", column = "id_delete")
    })
    List<UserModel> getAll();

    @Select("select * from user where id=#{id}")
    UserModel getById(long id) ;

    @Insert("INSERT INTO user( username , password , salt , nickname , avatars , is_delete, status ) VALUES( #{username} , #{password}, #{salt} , #{nickname} , #{avatars} , #{isDelete} , #{status} )")
    void install(UserModel user);

    @Update({"UPDATE user SET username=#{username} ,password =#{password} ,salt =#{salt} ,nickname =#{nickname},avatars =#{avatars} is_delete=#{isDelete},status =#{status} WHERE id = #{id}"})
    void Update(UserModel userModel);

    @Delete("delete from user where id=#{id}")
    void delete(Long id);

}
````


> 1.6 使用：

`````java
package com.liaoxx.spring_hello.controller.index;

import com.liaoxx.spring_hello.mapper.UserMapper;
import com.liaoxx.spring_hello.model.UserModel;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import java.util.List;

@Controller
@RequestMapping("/user")
public class UserController {
    //注入Mapper
    @Autowired
    private UserMapper userMapper;

    @RequestMapping("/list")
    public String list(){
        List<UserModel> userModelList= userMapper.getAll();
        for (UserModel userItem: userModelList) {
            System.out.println(userItem.toString());
        }
        return "success";
    }
}

`````
