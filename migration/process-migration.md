# 執行 Migration

> 環境 : Ubuntu 14.04

> 版本 : Laravel 4.2


### 執行

```shell
$ php artisan migrate
```

### 強制執行

```shell
$ php artisan migrate --force
```
有許多的 Migration 是具有破壞性的，可能會讓你的產品中的資料遺失，所以在執行 Migration 的時候，會詢問你是否真的要執行此次的 Migration ，讓你確認同意執行，若不想要被詢問，可以在執行指令後方加入`--force`去強制執行，不再詢問

### 回復上一次的 Migration

```
$ php artisan migrate:rollback
```

### 重設所有的 Migration 操作

```shell
$ php artisan migrate:reset
```

### 重設所有的 Migration 操作，並重新進行所有 Migration 操作

```shell
php artisan migrate:refresh
```

**注意** : 任何`重設 Migration` 的操作，可能會讓`資料表的資料遺失`，所以在正式環境時要進行此操作要小心謹慎


## 參考資料
* [Migrations & Seeding : Laravel - The PHP Framework For Web Artisans.](http://laravel.com/docs/migrations)
