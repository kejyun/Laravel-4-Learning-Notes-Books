# 在程式中呼叫控制器（Call controller within code）

通常控制器是在使用者發出請求（Request）時，我們會用來呼叫去控制整個程式需要處理的邏輯，然後再回應請求給使用者。

但有時我們自己的程式邏輯可能需要整個控制器處理的結果，再透過這個處理結果去進行下一步的程式邏輯，這時候我們可以有 2 種方法可以達到這樣的目的：

## 使用 Laravel App 內建呼叫函式呼叫控制器取得結果

假如我們有一個 HomeController 長的樣子像這樣：

```php
<?php namespace Controllers;

use Controller;

class HomeController extends Controller {

    public function postBussinessLogic($star = 5566)
    {
        // 一些程式邏輯 ...
        // Some bussiness logic ...

        return '鄉民偶像：' . $star;
    }
}
?>
```

我們可以用內建的 App 物件去建立 HomeController 物件，然後立即呼叫我們要的函式，就可以的得到我們要的結果：

```php
<?
$content = App::make('Controllers\HomeController')->postBussinessLogic('柯P');

// 鄉民偶像：柯P
var_dump($content);
?>
```

如果有要重複呼叫同一個控制器（Controller）的方法，我們也可以將整個控制器存放起來，然後再重複傳參數呼叫，也可以得到我們要的結果：

```php
<?
$controller = App::make('Controllers\HomeController');

$content_kp = $controller->postBussinessLogic('柯P');
// 鄉民偶像：柯P
var_dump($content_kp);


$content_keke = $controller->postBussinessLogic('科科');
// 鄉民偶像：科科
var_dump($content_keke);


$content_other = $controller->someOtherMethod();
// 其他方法結果（Other method result）
var_dump($content_other);
?>
```


## 使用 Request 假裝使用者呼叫自己的控制器取得結果

我們也可以像 cURL 一樣，假裝自己是使用者，去呼叫控制器

```php
<?php
function pretendRequest($params){
    $request = Request::create('BussinessLogic', 'POST', $params);
    return Route::dispatch($request)->getContent();    
}

$content = pretendRequest('柯P');
// 鄉民偶像：柯P
var_dump($content);
?>
```


## 參考資料
* [Call a controller in Laravel 4](http://stackoverflow.com/questions/15205239/call-a-controller-in-laravel-4)
