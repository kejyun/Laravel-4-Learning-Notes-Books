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



## 參考資料
* [KeJyun學習日誌: 在Ubuntu 12.04 使用 Nginx 做為 Laravel 4 伺服器](http://blog.kejyun.com/2014/06/how-to-install-laravel-with-nginx-on-an-ubuntu12.04.html)
* [How To Install Laravel with Nginx on an Ubuntu 12.04 LTS VPS | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-laravel-with-nginx-on-an-ubuntu-12-04-lts-vps)
