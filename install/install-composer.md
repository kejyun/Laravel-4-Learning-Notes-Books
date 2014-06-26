# 安裝Composer

> 環境 : Ubuntu 14.04


### 若沒有安裝 curl 可以先行安裝

```shell
$ sudo apt-get install curl
```

### 要使用composer必須要安裝php套件

***(備註:[php版本必須為5.4以上](http://laravel.com/docs/quick))***

```shell
$ sudo apt-get install php5
```

### 使用curl下載composer，並把 composer.phar 檔名重新命名為 composer

```shell
$ curl -sS https://getcomposer.org/installer | php -- --filename=composer
```

### 將composer移到系統預設程式

```shell
$ sudo mv composer /usr/local/bin/composer
```

### 測試composer

```shell
$ composer
```

![測試composer](http://2.bp.blogspot.com/-M_sNSLH41qs/U6mTrQqz-yI/AAAAAAAAB1s/G97UdMGdhXE/s1600/2014-06-24_230440.png)

---

## 參考資料
* [KeJyun學習日誌: 在 Ubuntu14.04 安裝 composer](http://blog.kejyun.com/2014/06/Install-laravel4.2-on-ubuntu14.04.html)
* [Composer Download](https://getcomposer.org/download/)
