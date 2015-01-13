# 安裝 PHP Unit

### 使用 composer 安裝 phpunit

可以到 [Packagist](https://packagist.org/) 找到最新版本的 [phpunit/phpunit](https://packagist.org/packages/phpunit/phpunit) 套件，或者可以安裝你自己想要執行的版本。

將你想要安裝的版本加入到 `composer.json` 中的 `require-dev` 中

> 因為我只需要在開發環境執行這些測試，所以不需要在正式環境上安裝

```json
{
  "require-dev": {
    "phpunit/phpunit": "4.4.*"
  }
}
```

#### 安裝開發環境套件套件

```shell
$ composer update --dev
```



### 執行第一次的 Laravel 測試

到 Laravel 資料夾目錄中 (目錄有包含 `phpunit.xml` 檔案)，輸入 `vendor/bin/phpunit`

> 所有 PHP Unit 測試相關設定皆在 phpunit.xml 中設定，當執行 phpunit 測試指令時，必須要有該設定檔， Laravel 預設的設定是執行所有在 `./app/tests/` 目錄下的測試

```shell
$ vendor/bin/phpunit
```



### PHP Unit 執行規則
 - 若未指定測試檔，則會執行所有在測試目錄及子目錄的所有測試
  - `.app/tests/`
  - `.app/tests/othertest`
  - `.app/tests/othertest/sometest`
 - 檔案名稱與類別名稱必須以Test做為結尾
  - e.g. MyTest  =>  MyTest.php
 - 測試檔案中的測試函式必須要以test為開頭才會進行測試
  - e.g. testMyResult()
