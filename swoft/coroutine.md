##  sgo()&Co::crate()  协程 

###1.创建协程 sgo() 开启新协程。

```php

function sgo(callable $callable, bool $wait = true): void
```

srun ()启动协程并等待执行结束。
```php
function srun(callable $callable): bool
```

Swoft::Co() 开启新协程。

```php
Swoft::Co(callable $callable, bool $wait = true): void
```