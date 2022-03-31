#Spring Native
##[官方文档](https://docs.spring.io/spring-native/docs/current/reference/htmlsingle/#getting-started-buildpacks)  |  [发行版本](https://github.com/spring-projects-experimental/spring-native/releases)
### 1.环境
````text
os : windows10 21H1
jdk : 11
spring-boot : 2.5.6
maven : 3.8.2
GraalVM : community 22.0.0.2
native-image: community 22.0.0.2
````

<br>

### 1.1 安装Graalvm [参考文档](https://juejin.cn/post/6943554536917172231) |  [发行版本](https://github.com/graalvm/graalvm-ce-builds/releases)  
####1.1.1  需要下载的文件
> 1.  虚拟机 [graalvm-ce-java11-windows-amd64-22.0.0.2.zip](https://github.com/graalvm/graalvm-ce-builds/releases/download/vm-22.0.0.2/graalvm-ce-java11-windows-amd64-22.0.0.2.zip) 

> 2.  native-image组件 [native-image-installable-svm-java11-windows-amd64-22.0.0.2.jar`](https://github.com/graalvm/graalvm-ce-builds/releases/download/vm-22.0.0.2/graalvm-ce-java11-windows-amd64-22.0.0.2.zip) 

<br>

###1.2 安装`Graalvm`  
#### 解压文件到本地: `C:\graalvm-ce-java11-22.0.0.2`
#### 将PATH环境变量指向GraalVM的bin目录 ; 设置JAVA_HOME ; cmd运行
```shell
$:      setx /M PATH "C:\graalvm-ce-java11-22.0.0.2\bin;%PATH%" 
$:      setx /M JAVA_HOME "C:\graalvm-ce-java11-22.0.0.2" 
```
> 可能需要重启电脑
#### 验证安装
```shell
$:    java -verison
```

```text
C:\Users\11828\Downloads>java -version
openjdk version "11.0.14" 2022-01-18
OpenJDK Runtime Environment GraalVM CE 22.0.0.2 (build 11.0.14+9-jvmci-22.0-b05)
OpenJDK 64-Bit Server VM GraalVM CE 22.0.0.2 (build 11.0.14+9-jvmci-22.0-b05, mixed mode, sharing)
```
<br>




###1.3 安装`native-image`组件
```shell
$:    gu -L install native-image-installable-svm-java11-windows-amd64-22.0.0.2.jar
```

```text
C:\Users\11828\Downloads>gu -L install native-image-installable-svm-java11-windows-amd64-22.0.0.2.jar
Processing Component archive: native-image-installable-svm-java11-windows-amd64-22.0.0.2.jar
Installing new component: Native Image (org.graalvm.native-image, version 22.0.0.2)
```



#### 验证命令
```shell
$:    gu list
```

```text
C:\Users\11828\Downloads>gu list
ComponentId              Version             Component name                Stability                     Origin
---------------------------------------------------------------------------------------------------------------------------------
graalvm                  22.0.0.2            GraalVM Core                  Supported
js                       22.0.0.2            Graal.js                      Supported
native-image             22.0.0.2            Native Image                  Early adopter
```

<br>

### 2.1 在pom.xml添加`Spring Native`依赖

```xml
<dependencies>
    <!-- ... -->
    <dependency>
        <groupId>org.springframework.experimental</groupId>
        <artifactId>spring-native</artifactId>
        <version>0.11.3</version>
    </dependency>
</dependencies>
```

### 2.2 . 添加`Spring AOT`插件
```xml
<build>
    <plugins>
        <!-- ... -->
        <plugin>
            <groupId>org.springframework.experimental</groupId>
            <artifactId>spring-aot-maven-plugin</artifactId>
            <version>0.11.3</version>
            <executions>
                <execution>
                    <id>generate</id>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```


### 2.3 启用原生镜像支持
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <image>
            <builder>paketobuildpacks/builder:tiny</builder>
            <env>
                <BP_NATIVE_IMAGE>true</BP_NATIVE_IMAGE>
            </env>
        </image>
    </configuration>
</plugin>
```
### 2.4 固定GraalVM 版本
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <!-- ... -->
        <image>
            <buildpacks>
                <buildpack>gcr.io/paketo-buildpacks/java-native-image:7.1.0</buildpack>
            </buildpacks>
        </image>
    </configuration>
</plugin>
```


### 2.5添加原生构建工具插
```xml
<profiles>
        <profile>
            <id>native</id>
            <dependencies>
                <!-- Required with Maven Surefire 2.x -->
                <dependency>
                    <groupId>org.junit.platform</groupId>
                    <artifactId>junit-platform-launcher</artifactId>
                    <scope>test</scope>
                </dependency>
            </dependencies>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.graalvm.buildtools</groupId>
                        <artifactId>native-maven-plugin</artifactId>
                        <version>0.9.10</version>
                        <extensions>true</extensions>
                        <executions>
                            <execution>
                                <id>build-native</id>
                                <goals>
                                    <goal>build</goal>
                                </goals>
                                <phase>package</phase>
                            </execution>
                            <execution>
                                <id>test-native</id>
                                <goals>
                                    <goal>test</goal>
                                </goals>
                                <phase>test</phase>
                            </execution>
                        </executions>
                        <configuration>
                            <!-- ... -->
                        </configuration>
                    </plugin>
                    <!-- Avoid a clash between Spring Boot repackaging and native-maven-plugin -->
                    <plugin>
                        <groupId>org.springframework.boot</groupId>
                        <artifactId>spring-boot-maven-plugin</artifactId>
                        <configuration>
                            <classifier>exec</classifier>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>

```


### 2.6 配置包含spring-native依赖项Maven存储库以及插件仓库 

```xml
<repositories>
    <!-- ... -->
    <repository>
        <id>spring-release</id>
        <name>Spring release</name>
        <url>https://repo.spring.io/release</url>
    </repository>
</repositories>
```

```xml
<pluginRepositories>
    <!-- ... -->
    <pluginRepository>
        <id>spring-release</id>
        <name>Spring release</name>
        <url>https://repo.spring.io/release</url>
    </pluginRepository>
</pluginRepositories>
```
