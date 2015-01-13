# PHP Unit 進階測試

### 只測試指定群組的測試

#### 將測試方法加入到指定群組中

> 在方法類別前面的註解加入 `@group` 的註記，可以告訴 PHP Unit 這個方法的所屬群組


```php
<?php
/**
 * @group bugTestGroup
 */
public function testBasicExample()
{
  $crawler = $this->client->request('GET', '/');
  $this->assertTrue($this->client->getResponse()->isOk());
}
```

#### 列出所有可以測試的 PHP Unit 測試群組

```shell
$ vendor/bin/phpunit --list-groups

Available test group(s):
- __nogroup__
- bugTestGroup
```

#### 測試指定群組 `bugTestGroup` 的測試

```shell
$ vendor/bin/phpunit --group bugTestGroup

Time: 92 ms, Memory: 8.50Mb

OK (1 test, 1 assertion)
```


## 參考資料
* [PHPUnit Manual – Chapter 3. The Command-Line Test Runner](https://phpunit.de/manual/current/en/textui.html)
* [PHPUnit Manual – Appendix B. Annotations - @group](https://phpunit.de/manual/current/en/appendixes.annotations.html#appendixes.annotations.group)
