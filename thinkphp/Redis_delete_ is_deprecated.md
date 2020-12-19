##报错 ：Function Redis::delete() is deprecated  

###1.报错详情
```text
[8192] ErrorException in Redis.php line 165
Function Redis::delete() is deprecated

    /**
     * 删除缓存
     * @access public
     * @param string $name 缓存变量名
     * @return boolean
     */
    public function rm($name)
    {
        return $this->handler->delete($this->getCacheKey($name));
    }
```

###2.原因 /解决方案 ：Function Redis::delete() is deprecated  //Redis::delete()方法 在新版的predis扩展中已经被启用弃用,新的 api 是 Reddis::del(),将其替换即可。