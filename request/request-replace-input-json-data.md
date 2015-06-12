# 取代 Input::json() 資料

我們傳送給 `Input::json()` 的資料的方式，透過 cURL 的方式會像是這樣

```shell
$ curl 'http://localhost/api/doSomething' --data  '{"name":"kejyun","blog":"http://blog.kejyun.com","github":"https://github.com/kejyun"}'
```

這個時候我們就可以用 `Input::json()` 去接收我們用 cURL 傳送過去的資料，就像這樣：


```php
$profile = Input::json();

$name = $profile->get('name', 'default_name');          // kejyun
$blog = $profile->get('blog', 'default_blog');          // http://blog.kejyun.com
$github = $profile->get('github', 'default_github');    // https://github.com/kejyun
```

一般我們在透過程式去產生測試資料除錯時，可以使用 `Input::replace($input_data)`去取代原本使用者傳入的資料，但是我們卻無法透過 `Input::replace()` 取代我們使用 `Input::json()` 的資料

若我們想要取代 `Input::json()` 的資料，我們可以用 `Input::json()->replace()` 的方式去取代 `Input::json()` 的資料，就像這樣：

```php
$replace_profile_data = [
    'name'=> 'Jimmy',
    'blog'=> 'http://laravel4-book.kejyun.com',
    'github'=> 'https://github.com/kejyun/Laravel-4-Learning-Notes-Books',
];
Input::json()->replace($replace_profile_data);


$profile = Input::json();

$name = $profile->get('name', 'default_name');          // Jimmy
$blog = $profile->get('blog', 'default_blog');          // http://laravel4-book.kejyun.com
$github = $profile->get('github', 'default_github');    // https://github.com/kejyun/Laravel-4-Learning-Notes-Books
```

這樣我們就可使用程式去取代 `Input::json()` 變數資料去做程式的測試了！

