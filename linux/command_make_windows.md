## 在windows 安装 make 命令
### 1、下载安装MinGW：https://mirrors.gigenet.com/OSDN//mingw/68260/mingw-get-setup.exe

### 2、安装MinGW，选择Basic setup下选择mingw32-base包。

### 3、打开MinGW安装目录下的bin文件夹，将mingw32-make.exe复制并重命名为make.exe。

### 4、设置环境变量，把bin文件夹的路径 C:\MinGW\bin 添加到用户变量。

### 5、查看make是否安装成功： 在cmd中输入 `make -v`。

### 6、看到以下内容表示安装成功：

```shell
PS C:\Users\11828> make -v
GNU Make 3.82.90
Built for i686-pc-mingw32
Copyright (C) 1988-2012 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```