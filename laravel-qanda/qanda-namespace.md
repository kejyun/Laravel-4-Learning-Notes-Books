# Namespace（命名空間）

為了避免 Class 的名稱彼此污染到，我們現在撰寫類別函式時都會加上 Namespace（命名空間），但因為 Laravel 4 沒有支援 PSR-4 的自動載入包含命名空間類別函式方法，所以我們加入命名空間的時候，需要手動的去設定載入。

## 在 Controller 加入命名空間

在 `app/controller` 加入 `project` 資料夾，並在裡面新增一個 `app/controller/project/TestController.php` 檔案，並將該檔案加入命名空間 `Controller\Project`，會長的像這樣：


```php
<?php namespace Controller\Project;
// app/controller/project/TestController.php

use Controller;

class TestController extends Controller
{
    /**
     * 測試函式
     */
    public function getTest()
    {
        var_dump('5566');
    }
}
?>
```


## 載入命名空間

我們要到 `app/start/global.php` 檔案中手動載入這個資料夾的檔案

> Laravel 4 預設會載入 `controller` 資料夾下的所有檔案，若有開新的資料夾，比需要手動指定載入

```php
<?php
// app/start/global.php

ClassLoader::addDirectories(array(
    app_path().'/controllers/project',
));
?>
```

## 更新 autoload 檔案

設定完之後，需要使用指令 `php artisan dump-autoload` 及 `composer dump-autoload`指令，重新載入所有 autoload 檔案，讓 autoload 檔案中有新的命名空間檔案

```shell
$ php artisan dump-autoload
 Generating optimized class loader

$ composer dump-autoload
  Generating optimized class loader
```


這樣就可以正常的讀取到該命名空間下的檔案了！！！
