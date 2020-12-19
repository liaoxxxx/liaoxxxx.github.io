###  文件操作以及流 (stream)
```php
<?php
    echo "开始时的内存占用：" . round(memory_get_usage() / 1024, 3) . 'KB' . PHP_EOL;
    $srcPath = './source.zip';
    $src = fopen($srcPath, 'r');
    $target = fopen('./target.zip', 'w');
    
    echo '源文件的大小：' . (round(filesize($srcPath) / 1024, 3)) . "KB" . PHP_EOL;
    
    stream_copy_to_stream($src, $target);
    echo "文件流 复制结束时内存占用：" . round(memory_get_usage() / 1024, 3) . "KB" . PHP_EOL;
    
    $cTargetPath = './cTarget.zip';
    copy($srcPath, $cTargetPath);
    
    echo "文件 复制结束时内存占用：" . round(memory_get_usage() / 1024, 3) . "KB" . PHP_EOL;
    
    $wTargetPath = './wTarget.zip';
    $wTarget = fopen($wTargetPath, 'w+');
    $srcHandle = file_get_contents($srcPath);
    fwrite($wTarget,$srcHandle);
```




````text


D:\coding\zhofuTech\back_end_of_mall\test>php test.php
开始时的内存占用：368.68KB
源文件的大小：596377.997KB
文件流 复制结束时内存占用：369.281KB
文件 复制结束时内存占用：369.5KB
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted (tried to allocate 610699280 bytes) in D:\coding\zhofuTech\back_end_of_mall\test\test.php on line 19

Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 610699280 bytes) in D:\coding\zhofuTech\back_end_of_mall\test\test.php on line 19

D:\coding\zhofuTech\back_end_of_mall\test>php test.php
PHP Parse error:  syntax error, unexpected 'fwrite' (T_STRING) in D:\coding\zhofuTech\back_end_of_mall\test\test.php on line 21

Parse error: syntax error, unexpected 'fwrite' (T_STRING) in D:\coding\zhofuTech\back_end_of_mall\test\test.php on line 21


````