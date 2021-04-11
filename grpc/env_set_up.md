## 环境搭建

### 1. 下载 `protoc` 编译工具

> * 地址 https://github.com/protocolbuffers/protobuf/releases

> * 版本 protoc-3.15.8-win64.zip


### 2. golang项目 中的配置
>* 2.1  安装golang 的编译插件    `protoc-gen-go`  go get github.com/golang/protobuf/protoc-gen-go

>* go.mod 依赖项目
```text
module grpcmy

go 1.15

require (
	google.golang.org/protobuf v1.26.0
	google.golang.org/grpc v1.30.0
)
```