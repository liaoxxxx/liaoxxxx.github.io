# grpc 简单使用

## 1. 编写proto 文件

```proto
syntax ="proto3";

package example.first;
option go_package = "./;pb";

message PersonMessage{
  int32 id = 1;
  string name = 2;
  bool is_adult = 3;
}
```

##2. golang 编译生成

2.1 命令 `protoc --proto_path src/ --go_out=src/ ./src/first/person.proto`




## 3.错误  示例

#### 3.1.1 未指定包名（go）
```
Please specify either:
• a "go_package" option in the .proto source file, or
• a "M" argument on the command line.

-----------------------------------------
请指定:
•在.proto源文件中的“go_package”选项，或
•命令行中的“M”参数。




```


#### 3.1.2解决方案  在proto 文件中 指定选项  `go_package`

 ```
 option go_package = "./;grpcmy";
 ```

#### 3.1.3   `"./;pb";`     释义 `"./;你的包名";`  即 `pb` 为需要生成代码文件指定的包名



## 4. dart代码编译生成
> 以下所有命令管理员身份运行

#### 4.1.0  前置条件 
> 1. 添加目录到环境变量`Path`   `C:\Users\你的用户文件夹\AppData\Local\Pub\Cache\bin`

> 2. 添加目录到环境变量`Path`  使`dart`命令可以在命令行运行   `C:\flutter_windows_1.22.4-stable\flutter\bin\cache\dart-sdk\bin`


#### 4.1.1  安装protoc-gen-dart 扩展插件  `protoc_plugin` `dart pub global activate protoc_plugin`

#### 4.2  编译命令 `protoc --proto_path=./lib/proto  -Iprotos ./lib/proto/*.proto --dart_out=grpc:lib/pb`



