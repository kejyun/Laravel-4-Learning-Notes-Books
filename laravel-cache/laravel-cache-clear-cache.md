# 清除快取


## 清除所有快取

在網站上線之後，如果有修改一些資料的內容並希望能夠及時更新，但是若資料存放在快取中，我們就必須要自己去把快取中的資料清除，Laravel 有提供給我們指令及方法可以達到這樣的目的

### 使用指令清除快取

```shell
$ php artisan cache:clear
```

### 使用程式碼清除快取

 - 方法1：

```php
<?php

foreach (Cache::getMemory() as $cacheKey => $cacheValue)
{  
  Cache::forget($cacheKey);
}
```

- 方法2：

```php
<?php

Cache::flush();
```


## 參考資料
 * [php - Flush all cache in Laravel 4 - Stack Overflow](http://stackoverflow.com/questions/18900969/flush-all-cache-in-laravel-4)
 * [快取 - Laravel - 為網頁藝術家創造的 PHP 框架](http://laravel.tw/docs/4.2/cache)
