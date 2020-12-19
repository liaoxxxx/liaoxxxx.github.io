#DTO 对象的使用 


>1 . 数据传输对象（DTO)(Data Transfer Object)，是一种设计模式之间传输数据的软件应用系统。数据传输目标往往是数据访问对象从数据库中检索数据。数据传输对象与数据交互对象或数据访问对象之间的差异是一个以不具有任何行为除了存储和检索的数据（访问和存取器）。https://baike.baidu.com/item/DTO/16016821 


>2 .创建 DTO 对象

```java
     
    package com.liaoxx.spring_hello.dto.index;
    
    public class DtoinputDto {   //用户信息传输
        private String username;
    
        private String password;
    
        private String salt;
    
        public String getUsername() {
            return username;
        }
    
        public void setUsername(String username) {
            this.username = username;
        }
    
        public String getPassword() {
            return password;
        }
    
        public void setPassword(String password) {
            this.password = password;
        }
    
        public String getSalt() {
            return salt;
        }
    
        public void setSalt(String salt) {
            this.salt = salt;
        }
    }


```


> 3 .  注入到请求对象 ,访问 http://localhost:18306/dto/test?username=liaoxx&password=99354996&salt=99999&orther=name   ,其中请求的参数 和   dtoinputDto 有效的字段匹配,其他字段无效

    
```java
package com.liaoxx.spring_hello.controller.index;

import com.liaoxx.spring_hello.dto.index.DtoinputDto;
import com.liaoxx.spring_hello.entity.Dto;
import com.liaoxx.spring_hello.repository.DtoRepository;
import com.liaoxx.spring_hello.service.DtoService;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;


@Controller
@ResponseBody
@RequestMapping("/dto")
public class DtotestController {

    @Autowired
    DtoService dtoService;

    @RequestMapping("/test")
    @ResponseBody
    public String test(DtoinputDto dtoinputDto) {   //注入到请求
    
        Dto dtoEntity=dtoService.transDto2Entity(dtoinputDto);
        dtoService.save(dtoEntity);
        return dtoinputDto.getUsername();
    }
}

```

> 4 . 使用 org.springframework.beans.BeanUtils#copyProperties() ; 转换DTO 对象成为 Entity 对象后,Entity封装其他属性,保存到数据库即可     

````java
package com.liaoxx.spring_hello.service;

import com.liaoxx.spring_hello.dto.index.DtoinputDto;
import com.liaoxx.spring_hello.entity.Dto;
import com.liaoxx.spring_hello.repository.DtoRepository;
import org.springframework.beans.BeanUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DtoService {

    @Autowired
    DtoRepository dtoRepository;

    public Dto transDto2Entity(DtoinputDto dtoinputDto){
        Dto dto =  new Dto();
        BeanUtils.copyProperties(dtoinputDto,dto);
        return dto;
    }

    public void save(Dto dtoEntity){
        dtoRepository.save(dtoEntity);
    }
}
````