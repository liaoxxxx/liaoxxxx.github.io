##定时任务


###文章参考搬运 :
>1.https://liuyueyi.github.io/hexblog/2018/08/01/180801-Spring%E4%B9%8B%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E7%AF%87/

>2.https://segmentfault.com/a/1190000015880324

####. 基本使用

#####1.在SpringBoot项目中，使用定时任务需要先开启对应的配置，一个简单的demo如下  添加 @EnableScheduling  注解


```java
package com.liaoxx.spring_hello;
import org.springframework.scheduling.annotation.EnableScheduling;


@EnableScheduling   //用于定时任务
public class SpringHelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringHelloApplication.class, args);
    }

}

```
#####2. 在要执行定时任务的方法上 使用注解 @Scheduled ,并配置相应的参数

````java
package com.liaoxx.spring_hello.task;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
@Component
public class PrintfTask {

    private static final Logger logger= LoggerFactory.getLogger(PrintfTask.class);

    @Scheduled(cron = "0/1 * * * * ?")  //配置crontab 等 参数
    public void sc1() {
        System.out.println("线程id:"+Thread.currentThread().getId()+" sc1 " + System.currentTimeMillis());
    }
    @Scheduled(cron = "0/1 * * * * ?")
    public void sc2() {
        logger.info( "线程id:"+Thread.currentThread().getId()+" sc2 ()" + System.currentTimeMillis());

    }
}
````

>需要注意在Task类上 注解 @Componet 

```text
2020-01-05 21:36:59.000  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231419000
2020-01-05 21:37:00.002  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231420002
线程id:39 sc1 1578231420003
2020-01-05 21:37:01.001  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231421001
线程id:39 sc1 1578231421001
2020-01-05 21:37:02.002  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231422002
线程id:39 sc1 1578231422002
2020-01-05 21:37:03.001  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231423001
线程id:39 sc1 1578231423001
2020-01-05 21:37:04.002  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231424002
线程id:39 sc1 1578231424002
2020-01-05 21:37:05.001  INFO 1728 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:39 sc2 ()1578231425001

```
#####3.单线程串行的任务

>可以发现线程id 是相同的,即是同步堵塞,所以当某个线程堵塞时,会阻碍其他任务 的运行

>添加代码:

```java

	@Scheduled(cron = "0/1 * * * * ?")
    public void sc3() throws InterruptedException {
     
        logger.info( "线程id:"+Thread.currentThread().getId()+" sc3 ()" + System.currentTimeMillis());
      
        while (true) {
            Thread.sleep(5000);
            logger.info("=======sleep()=======");
        }
        
    }
```

>log信息显示 sc3() 阻碍了其他的任务运行

```text
....略
2020-01-05 22:25:26.264  INFO 7708 --- [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2020-01-05 22:25:27.183  INFO 7708 --- [           main] o.s.s.c.ThreadPoolTaskScheduler          : Initializing ExecutorService 'taskScheduler'
2020-01-05 22:25:27.328  INFO 7708 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8081 (http) with context path ''
2020-01-05 22:25:27.330  INFO 7708 --- [           main] c.l.spring_hello.SpringHelloApplication  : Started SpringHelloApplication in 4.782 seconds (JVM running for 6.01)
2020-01-05 22:25:28.001  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : 线程id:37 sc3 ()1578234328001
2020-01-05 22:25:31.001  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:34.002  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:37.002  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:40.002  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:43.003  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:46.004  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:49.004  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:52.005  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
2020-01-05 22:25:55.006  INFO 7708 --- [   scheduling-1] com.liaoxx.spring_hello.task.PrintfTask  : =======sleep()=======
....略
```
#####4. 并行调度

>为了避免串行调度任务阻塞,影响其他线程 ,在Application 入口的class  使用注解 @EnableAsync ,并且需要异步执行的任务的 method 使用 注解 @Async 标注为异步任务.

###### 启动类上添加注解 @EnableAsync

````java
@EnableScheduling  //用于定时任务
@EnableAsync        //用于开启并行任务
public class SpringHelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringHelloApplication.class, args);
    }
}
````

######异步执行的任务的方法使用 注解 @Async
```java
    @Scheduled(cron = "0/1 * * * * ?")
    @Async  //设为异步多线程执行
    public void sc4()  {
        logger.info( "线程id:"+Thread.currentThread().getId()+" sc4 ()" + System.currentTimeMillis());
    }
```





