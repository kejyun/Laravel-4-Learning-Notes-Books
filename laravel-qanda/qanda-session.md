# SESSION

## SESSION 測試時沒有作用

在做 Session 的邏輯測試時，我會想要測試 Session 是否有正確的存進去，所以再存入之後我就直接把他列印出來

```php
<?php

Session::put('kejyun', 'Hello');
$get_session = Session::get('kejyun');
var_dump($get_session);
exit;
```

列印出來後，會發現可以正確地取到剛剛存入的 Session

```html
string(5) "Hello"
```

但是當我把存入 Session 的 `Session::put` 註解掉，想要重取上一步我存入的 Session 值時，會發現取出來的 Session 卻是 `NULL`

```php
<?php

// Session::put('kejyun', 'Hello');
$get_session = Session::get('kejyun');
var_dump($get_session);
exit;
```

```html
NULL
```

在 trace 程式之後發現，在整個 Request 結束之前，Laravel 僅僅是把 Session 存放在變數當中。當 Request 要結束之後，才會真正的將 Session 記錄下來，然而我在做 Session 測試時，最後有用 `exit;` 去中斷程式碼的運作（Debug 的習慣），因為整個 Request 被我中斷了，所以 Session 就一直沒有被記錄下來，只要不中斷程式之後 Session 就可以正確地被記錄下來了。
