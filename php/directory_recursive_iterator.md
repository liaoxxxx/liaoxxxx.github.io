### php 递归实现文件目录迭代器


##### 1.官方实现

````php
<?php
	$dir_iterator = new RecursiveDirectoryIterator('./');
	$iterator = new RecursiveIteratorIterator($dir_iterator);

	foreach ($iterator as $file) {
    	echo $file."\r\n";
	}

````