# 安裝 Laravel


> 環境 : Ubuntu 14.04

> 版本 : Laravel 4.2


### 使用 composer 指令安裝 laravel 4.2

```shell
$ composer create-project laravel/laravel custom_laravel_folder --prefer-dist
```

*指令格式*

> composer create-project [PACKAGE] [DESTINATION PATH] [--FLAGS]

***若出現以下訊息表示你沒有安裝 Mcrypt PHP extension***

> Mcrypt PHP extension required.
Script php artisan clear-compiled handling the post-install-cmd event returith an error

![Mcrypt PHP extension required.](http://3.bp.blogspot.com/-0ACOAXipmGc/U6me9xLiKzI/AAAAAAAAB18/KvX-_zxRBpw/s1600/2014-06-24_234025.png)

### 檢查有無安裝 Mcrypt

```shell
$ php --ri mcrypt
```

*有安裝 Mcrypt 會出現*

> Extension 'mcrypt' not present.

*沒有安裝 Mcrypt 會出現*

> mcrypt support => enabled

### 安裝 Mcrypt PHP extension


```shell
$ sudo apt-get install php5-mcrypt
```

### 啟動Mcrypt，並在重新檢測有無安裝成功


```shell
$ sudo php5enmod mcrypt
$ php --ri mcrypt
```

> 若原先安裝laravel時安裝失敗後，還要重新安裝至相同目錄，必須要將該安裝失敗的目錄清空，安裝laravel時必須安裝在一個空的資料夾

#### 安裝完後將網站跟目錄指定為 laravel/public 下即可

![Install Laravel 4 Success](http://1.bp.blogspot.com/-J741dYcYTzc/U6mfEgI3UQI/AAAAAAAAB2E/O2UydO3X8E0/s1600/2014-06-24_235035.png)


## 變更Laravel 4資料夾群組及寫入權限問題

```shell
$ sudo chgrp -R www-data /home/kejyun/laravel
$ sudo chmod -R 775 /home/kejyun/laravel/app/storage
```

# Let's play Laravel 4！！


## 參考資料
* [KeJyun學習日誌: 在 Ubuntu 14.04 安裝 Laravel 4.2](http://blog.kejyun.com/2014/06/Install-laravel4.2-on-ubuntu14.04.html)
* [Laravel - The PHP Framework For Web Artisans.](http://laravel.com/docs/installation)
* [php - My composer won't update completely with Laravel 4 it gets stuck with artisan - Stack Overflow](http://stackoverflow.com/questions/19775012/my-composer-wont-update-completely-with-laravel-4-it-gets-stuck-with-artisan?answertab=votes#tab-top)
* [php - composer laravel create project - Stack Overflow](http://stackoverflow.com/questions/18862160/composer-laravel-create-project)
