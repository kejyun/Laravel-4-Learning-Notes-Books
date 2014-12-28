# 新增 Migration

> 環境 : Ubuntu 14.04

> 版本 : Laravel 4.2


### 建立新的 Migration

```shell
$ php artisan migrate:make create_users_table
```

*`create_users_table`為要產生的 Migration 檔名及 Class 名稱*

#### Example :

**Migration 檔名**

```
2014_06_25_081905_create_users_table.php
```

**Class**

```php
class CreateUsersTable extends Migration  {
    public function up()
    {
    }

    public function down()
    {
    }
}
```

*所以為了避免 CLASS 檔名重複，在每一次的 Migration 建立指令必須為不同*


### 命名要處理的資料表

```
$ php artisan migrate:make add_votes_to_user_table --table=users
$ php artisan migrate:make create_users_table --create=users
```

在 Migration 指令後加入 `--table=users`，指定要**處理**的資料表為`users`

在 Migration 指令後加入 `--create=users`，指定要**新增**的資料表為`users`


## 參考資料
* [Migrations & Seeding : Laravel - The PHP Framework For Web Artisans.](http://laravel.com/docs/migrations)
