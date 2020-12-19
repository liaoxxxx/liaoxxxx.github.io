##安装和调试


###启动出现报错 ：  (CONSOLE)ErrorException: proc_open() has been disabled for security reasons

### 原因 ： php.ini 文件中因为安全原因 ，禁用了该函数
 disable_functions = passthru,exec,system,putenv,chroot,chgrp,chown,shell_exec,popen,
  proc_open  ，
 ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru

###解决方案  在disable_functions 项中删除  proc_open  即可


****************************************************************************
