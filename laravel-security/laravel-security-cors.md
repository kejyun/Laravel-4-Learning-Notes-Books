# CORS 跨來源資源共享

> Cross-Origin Resource Sharing

由於一般的請求通常是同網域 (Domain) 的請求，如果在API的部分要使用跨網域請求，像是`www.domain.com`請求`api.domain.com`，這樣的話就會牽涉到跨網域請求，如果想要達到這樣的跨網域請求，可以在`app/start/global.php`裡面加入允許請求來源，這樣就可以達到跨網域請求了。


```php
App::after(function($request, $response) {
    $response
        ->headers
        ->set('Access-Control-Allow-Origin', 'http://api.domain.com');
    return $response;
});
```
