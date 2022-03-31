## open feign 使用示例

### 错误 `Did you forget to include spring-cloud-starter-netflix-ribbon?`
```log
2022-03-15 01:30:21.691  WARN 12948 --- [           main] ConfigServletWebServerApplicationContext : 
  Exception encountered during context initialization - cancelling refresh attempt: 
    org.springframework.beans.factory.BeanCreationException: 
      Error creating bean with name 'hello': 
        Injection of resource dependencies failed; nested exception is org.springframework.beans.factory.BeanCreationException:
         Error creating bean with name 'com.liaoxx.spring_cloud_my.feign_serve.service.HelloService': 
          FactoryBean threw exception on object creation; nested exception is java.lang.IllegalStateException: 
            No Feign Client for loadBalancing defined. Did you forget to include spring-cloud-starter-netflix-ribbon?
```