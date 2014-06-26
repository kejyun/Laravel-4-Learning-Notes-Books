# 佈署 Laravel 程式

> 環境 : Ubuntu 14.04

> 版本 : Laravel 4.2

> 佈署工具 : git

> Client端環境 : Ubuntu 或 Windows 皆可

### [安裝 Laravel 4](http://blog.kejyun.com/2014/06/Install-laravel4.2-on-ubuntu14.04.html)

```shell
$ composer create-project laravel/laravel custom_laravel_folder --prefer-dist
```

*指令格式*

> composer create-project [PACKAGE] [DESTINATION PATH] [--FLAGS]


### 將安裝完後的 Laravel 4 程式加入 git 版本控制


```shell
$ cd custom_laravel_folder
$ git init
$ git add .
$ git commit -m "Initial commit"
```

### [安裝 git server](http://blog.kejyun.com/2012/11/Install-Git-Server-On-Ubuntu.html)

```shell
$ mkdir /home/kejyun/laravel.git
$ cd  /home/kejyun/laravel.git
~/laravel.git$ git init --bare
```


### 建立git推送處理hook
```shell
$vim  /home/kejyun/laravel.git/hooks/post-update
```

```
#!/bin/sh
work_tree=/home/kejyun/laravel
GIT_WORK_TREE=$work_tree git checkout -f
cd $work_tree
composer install
composer update
php artisan migrate --force
```
*work_tree為網站的根目錄*


### 將程式處理 hook 變更為可執行檔

```shell
$ chmod +x  /home/kejyun/laravel.git/hooks/post-update
```

### 將原本安裝的laravel程式加入新的git server

```shell
$ git remote add origin ssh://kejyun@production-server/home/kejyun/laravel.git
```


### 將程式推送到 Git Server 完成佈署程式

```shell
git push origin master
```

*因為在`comopser`套件管理中`vender`資料夾的程式不是直接透過 git 推送上去，而是在每次推送過去 git server 後，透過`git hook`中的 `composer install` & `composer update`去抓取並更新套件，所以在第一次推送佈署 Laravel 4 的程式會比較慢是正常的喔~*



## 參考資料
* [KeJyun學習日誌: 使用 git 佈署 Laravel 4 程式](http://blog.kejyun.com/2014/06/Using-git-to-deplay-laravel4-application.html)
* [Deploying a Laravel website with git](http://marcanuy.com/en/homepage/36-general/technology/18-deploying-a-laravel-website-with-git)
* [php - Repository deployment and Composer : what workflow? - Stack Overflow](http://stackoverflow.com/questions/14498560/repository-deployment-and-composer-what-workflow)


