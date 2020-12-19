### Maximum upload size exceeded （上传文件大小限制）

####1.报错信息

```text
 Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed;
 nested exception is org.springframework.web.multipart.MaxUploadSizeExceededException: 
 Maximum upload size exceeded;
 nested exception is java.lang.IllegalStateException: org.apache.tomcat.util.http.fileupload.FileUploadBase$SizeLimitExceededException:
 the request was rejected because its size (329652) exceeds the configured maximum (131072)] with root cause
 
``` 

####1.错误信息意思 : 上传文件的大小是5019,而最大可以上传的size是1024.超出最大限制,所以无法上传成功

造成这种问题的原因：上传文件太大导致的。
错误信息中可以看出SpringBoot设置的上传文件大小不能超过1MB,而实际却上传了5MB大小的文件。5MB超过了限制的1MB,导致上传文件报异常

解决办法：修改springboot设置上传文件大小。
以application.prperties为例(不同的springboot版本,配置可能有些差异)