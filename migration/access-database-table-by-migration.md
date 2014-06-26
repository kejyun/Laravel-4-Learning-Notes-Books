# 編輯 Migration

> 環境 : Ubuntu 14.04

> 版本 : Laravel 4.2

### 建立新資料表

```php
class CreateUsersTable extends Migration {
    // 執行 Migration
    public function up()
    {
        Schema::create('users', function(Blueprint $table)
        {
            // Auto increment
            $table->increments('id');
            // Email
            $table->string('email' , 200);
            // 密碼
            $table->string('password' , 60);
            // 時間戳記 created_at & updated_at
            $table->timestamps();
        });
    }

    // 取消 Migration
    public function down()
    {
        // 移除 user 資料表
        Schema::drop('users');
    }
}
```

**建立資料表**

使用`Schema::create()`方法去建立新的資料表

**移除資料表**

使用`Schema::drop()`方法去移除資料表


### 異動資料表欄位

```php
class AddNameColumnToUsers extends Migration {
    // 執行 Migration
    public function up()
    {
        Schema::table('users', function(Blueprint $table)
        {
            // 姓名
            $table->string('name', 50)->after('password');
        });
    }

    // 取消 Migration
    public function down()
    {
        Schema::table('users', function($table)
        {
            // 姓名
            $table->dropColumn('name');
        });
    }
}
```

**建立欄位**

使用`Schema::table()`去對資料表進行操作，並在裡面直接指定要新增的欄位屬性即可

**移除欄位**

使用`Schema::table()`去對資料表進行操作，並使用`$table->dropColumn();`方法去移除資料表中的欄位


### 異動資料表已存在欄位屬性

```php
class ChangeUserNameColumnLength extends Migration {
    // 執行 Migration
    public function up()
    {
        // 將`user`.`name`欄位長度變更為80
        DB::statement("ALTER TABLE `users` CHANGE `name` `name` VARCHAR(80) CHARACTER SET utf8 COLLATE utf8_unicode_ci NOT NULL");
    }

    // 取消 Migration
    public function down()
    {
        // 將`user`.`name`欄位長度變更為50
        DB::statement("ALTER TABLE `users` CHANGE `name` `name` VARCHAR(50) CHARACTER SET utf8 COLLATE utf8_unicode_ci NOT NULL");
    }
}
```

因為 Laravel 4.2 沒有直接的函式可以去異動欄位的屬性，所以我們可以直接用`DB::statement()`的方法去直接下Query異動欄位


### 異動資料表欄位名稱

因為 Laravel 4.2 沒有預設載入[`doctrine/dbal`](https://packagist.org/packages/doctrine/dbal)套件，所以要使用函式去異動欄位名稱時，要在`composer.json`中自行手動加入此套件，才能使用此函式。

```json
"require": {
	"doctrine/dbal": "2.5.*@dev"
}
```

加入後使用`composer update`去安裝後即可使用

```php
class ChangeUsersNameColumn extends Migration {
    // 執行 Migration
    public function up()
    {
        Schema::table('users', function($table)
        {
            // 變更 name 欄位名稱為 nickname
            $table->renameColumn('name', 'nickname');
        });
    }

    // 取消 Migration
    public function down()
    {
        Schema::table('users', function($table)
        {
            // 變更 nickname 欄位名稱為 name
            $table->renameColumn('nickname', 'name');
        });
    }
}
```

使用`Schema::table()`去對資料表進行操作，並用`$table->renameColumn()`方法去變更資料表中的欄位名稱

## 參考資料
* [KeJyun學習日誌: Laravel 4 Migrate介紹](http://blog.kejyun.com/2013/07/Laravel4-Migrate-Introduction.html)
* [Schema Builder : Laravel - The PHP Framework For Web Artisans.](http://laravel.com/docs/schema)
