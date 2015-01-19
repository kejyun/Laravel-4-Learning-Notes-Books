# 安裝 Nginx

> 環境 : Ubuntu 14.04

### 更新 Ubuntu 套件

```shell
$ sudo apt-get update && apt-get upgrade
```

### 安裝套件
* nginx
* php5-fpm``（解析 php）``
* php5-mcrypt``（Laravel 4 需求套件）``
* php5-cli & git``（composer 需求套件）``

```shell
$ sudo apt-get install nginx php5-fpm php5-cli php5-mcrypt git
```

### 設定 nginx 的 VirtualHost

```shell
$ sudo vim /etc/nginx/sites-available/default
```

```
server {
        listen   80 default_server;

        # Laravel 網站目錄
        root /home/kejyun/laravel/public/;
        server_name laravel.app;

        index index.php index.html index.htm;

        location / {
             try_files $uri $uri/ /index.php$is_args$args;
        }

        # 傳送 php程式到 FastCGI server，透過 127.0.0.1:9000 解析
        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                include fastcgi_params;
        }
}
```


### 修改 php-fpm 設定檔

```shell
$ sudo vim /etc/php5/fpm/pool.d/www.conf
```

將 `listen = /var/run/php5-fpm.sock` 修改為 `listen = 127.0.0.1:9000`

*若沒有此段設定，請自行加上即可*

### 重新啟動 php-fpm 及 nginx

```shell
$ sudo service php5-fpm restart
$ sudo service nginx restart
```

### 建立虛擬主機 (Virtualhost)

透過剛剛建立的設定檔，我們可以建立其他的虛擬主機，Nginx 與 Apache 一樣都有 `site-available` 與 `sites-enabled` 資料夾可以設定虛擬主機，但是 Nginx 沒有像 Apache 一樣有 `a2ensite` 這樣的指令可以去啟動虛擬主機，只能自己建立虛擬主機設定檔的連結

#### 建立 (或複製) 原先設定檔

```shell
$ cd /etc/nginx/sites-available
/etc/nginx/sites-available$ sudo cp default kejyunapp
```

將原本的網站跟目錄 `root` 及虛擬主機名稱 `server_name` 修改為自己想要的主機名稱

```
server {
        listen   80 default_server;

        # Laravel 網站目錄
        root /home/kejyun/kejyunapp/public/;
        server_name kejyun.app;

        index index.php index.html index.htm;
}
```

在 `/etc/hosts` 自行加入自己設定的偽虛擬主機網址

```shell
$ sudo vim /etc/hosts
```

```
/etc/hosts

127.0.0.1       laravel.app
127.0.0.1       kejyun.app
```

#### 連結設定虛擬主機

```shell
/etc/nginx/sites-available$ cd ../sites-enabled
/etc/nginx/sites-enabled$ sudo ln -s /etc/nginx/sites-available/kejyunapp
/etc/apache2/sites-enabled$ sudo service nginx restart
```

設定完成後重新啟動 Nginx 即可以看到你的設定

 - http://kejyun.app/
 - http://laravel.app/


## 參考資料
* [KeJyun學習日誌: 在Ubuntu 12.04 使用 Nginx 做為 Laravel 4 伺服器](http://blog.kejyun.com/2014/06/how-to-install-laravel-with-nginx-on-an-ubuntu12.04.html)
* [How To Install Laravel with Nginx on an Ubuntu 12.04 LTS VPS | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-laravel-with-nginx-on-an-ubuntu-12-04-lts-vps)
* [Laravel Recipes :: Creating a Nginx VirtualHost](http://laravel-recipes.com/recipes/26/creating-a-nginx-virtualhost)
