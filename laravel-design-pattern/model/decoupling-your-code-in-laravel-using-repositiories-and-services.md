# 在 Laravel 4 使用資源庫 (Repositories) 及服務 (Services) 去降低程式的耦合性

> 文章來源：[Decoupling your code in Laravel using Repositiories and Services](http://dfg.gd/blog/decoupling-your-code-in-laravel-using-repositiories-and-services)

當開始使用 Laravel 的時候，你可以找到有好幾個地方可以建立你自己的目錄結構，而模型（Models）則是最大的一個結構，Laravel 則是建議模型最好是使用 Eloquent 物件去設計，並告知 Laravel 這個模型指的是哪一個資料表 - 而程式的商業邏輯比較不適合放在模型這個地方。

這邊文章重點在於將不同用途的模型放在不同的地方，藉此去降低程式碼的耦合性，更重要的是特別在你的應用正在成長中且要發展較快的時候，你可以有可重複使用性的程式碼。

我們在這裡會實作資源庫 (Repository) 設計模式結合服務 (Service) 導向的架構，這個是我喜歡在乾淨的 Laravel 安裝時，分離程式邏輯的方法，可能值得注意的是，我過去開發很多 Symfony 且這樣的實做是有很大的參考基礎的。

## 不同類型的模型

在實作這些元素時，我們最終會留下 3 個不同類型的模型

### 實體 (Entities)：

標準的 Laravel 模型，裡面只有包含設定的變數、被 Eloquent 使用的方法、關聯關係、訪問器及存取器。

### 資源庫 (Repositories)：

使用實體 (Entities) 去取得你的資料，裡面的函式去收集應用程式需要的特定資料集，雖然在裡面的邏輯可能會異動，但是回傳的資料格式應該都是相同的。

### 服務 (Services)：

全域資料邏輯的家，包含被你應用程式的使用的函式，他可以稱為是你的資源庫 (Repositories) ，也是你的資料驗證器 (validates)、建立 session 且包含你的邏輯，這些可以幫助你的控制器 (controller) 變得更為輕盈！


## 所以什麼是資源庫 (Repository) 的設計模式？
資源庫 (Repository) 是一個與你的資料來源做交談的通用類別（在這個情況是指你的實體 (Entity)），且回傳非常特定的輸出格式，如果資料來源被異動（例如：在API中），你可以交換你的舊資源庫變成新的資源庫，這兩個資源庫會回傳相同輸出格式的資料，意味著你應用程式的其他部分都不需要做異動！

使用這個模式只需要一點點額外的功夫就可以，但有一天你會感謝你的幸運之星，你做到了！

## 而服務 (Service) 導向的架構？

這個一般會被考慮做為替代 MVC 的架構，但其實不需要這麼做，Laravel 已經使用很多服務 (Service) 在他自己的 MVC 環境中，你或許知道他們叫做 Facades，藉由這個模式，你可以在網站中任一地方，就像 Facades 一樣很容易的重新使用函式及方法。

## 介面 (Interfaces)、服務提供者 (Service Providers) 及 Facade 類別

在我們開始之前，這裏有三種檔案類型值得去了解一下，這會讓這些設置變得像魔術一樣，這三個分別是介面 (Interfaces)、服務提供者 (Service Providers) 及 Facade 類別

介面是讓資源庫可以通用的關鍵，它包含了類別所需要的資訊，且在沒有得到介面想要的結果時會丟出資訊告知我們，這讓我們不會忘記任何類別所需要的方法，並保護我們的應用。

服務提供者 (Service Providers) 散落在 Laravel 各處，且通常會在供應商 (vendor) 那邊找到，他們告訴 Laravel 有關你的類別且要怎麼去建立他，對於依賴性注入是非常重要的。

最後在這三者之中最簡單的是 Facade 類別，每當你嘗試要使用 Facade，Laravel 會到這個類別中，並僅取得類別的名稱並回傳，就這麼簡單！

## 那我們開始吧．．．

第一步是將你的模型放到「app/models/entities」目錄中，這些模型必須要有「Entities」的命名空間，在建立資料夾同時，我們也需要「app/models/repositories」及「app/models/services」這兩個資料夾。

現在，我們可以說我們有一個資料表包含所有 151 個神奇寶貝，你當然會有一個命名空間為「\Entities\Pokemon」這個類別的實體，在你的資源庫資料夾中，建立一個新的「pokemon」資料夾。

```php
<?php namespace Entities;
// model/Entities/Pokemon.php

class Pokemon extends \Eloquent {

    /**
     * The database table used by the model.
     *
     * @var string
     */
    protected $table = 'pokemon';
}
```

在我們建立類別之前，我們首先必須建立一個介面 (interface)，這樣有一個好處是可以讓我們好好想想並計劃我們的資源庫 (Repository) 內需要包含哪些東西。

```php
<?php namespace Repositories\Pokemon;
// model/Repositories/Pokemon/PokemonInterface.php

/**
 * A simple interface to set the methods in our Pokemon repository, nothing much happening here
 * 簡單的介面去設定我們的 Pokemon 資源庫
 */
interface PokemonInterface
{
    public function getPokemonById($pokemonId);

    public function getPokemonByName($pokemonName);
}
```

每當我們在類別中實作這個介面 (interface)，我們可以說這個類別必須要包含這兩種方法，每一個接收變數都會被描述。

接下來，讓我們建立我們的類別。

```php
<?php namespace Repositories\Pokemon;
// model/Repositories/Pokemon/PokemonRepository.php

use Illuminate\Database\Eloquent\Model;
use \stdClass;

/**
* Our pokemon repository, containing commonly used queries
* 我們的 pokemon 資源庫，包含一些常用的查詢
*/
class PokemonRepository implements PokemonInterface
{
    // Our Eloquent pokemon model
    // 我們的 pokemon Eloquent 模型
    protected $pokemonModel;

    /**
    * Setting our class $pokemonModel to the injected model
    * 設定我們的模型，將它注入到 $pokemonModel中
    *
    * @param Model $pokemon
    * @return PokemonRepository
    */
    public function __construct(Model $pokemon)
    {
        $this->pokemonModel = $pokemon;
    }

    /**
    * Returns the pokemon object associated with the passed id
    * 回傳傳入 ID 相關的 pokemon 物件
    *
    * @param mixed $pokemonId
    * @return Model
    */
    public function getPokemonById($pokemonId)
    {
        return $this->convertFormat($this->pokemonModel->find($pokemonId));
    }

    /**
    * Returns the pokemon object associated with the pokemonName
    * 回傳傳入 pokemonName 相關的 pokemon 物件
    *
    * @param string $pokemonName
    */
    public function getPokemonByName($pokemonName)
    {
        // Search by name
        $pokemon = $this->pokemonModel->where('name', strtolower($pokemonName));

        if ($pokemon)
        {
            // Return first found row
            return $this->convertFormat($pokemon->first());
        }

        return null;
    }

    /**
    * Converting the Eloquent object to a standard format
    * 轉換 Eloquent 物件為標準格式
    *
    * @param mixed $pokemon
    * @return stdClass
    */
    protected function convertFormat($pokemon)
    {
        if ($pokemon == null)
        {
            return null;
        }

        $object = new stdClass();
        $object->id = $pokemon->id;
        $object->name = $pokemon->name;

        return $object;
    }
}
```

三件重要的事情會發生在這裡，首先，我們實作我們剛剛做的介面 (interface)，再來我們注入我們的 Pokemon 實體 (Entity)到資源庫中 (Repository)，最後在回傳相關的 eloquent 物件時，我們將它轉換為一般的物件，如此一來當資料來源有異動時，我們還是可以回傳相同格式的物件實體。

在我們資源庫準備完成的最後階段，則是告訴 Laravel 他的相關資訊！

使用註冊 (register) 及綁定 (bind) 函式告訴 Laravel 去使用這個類別。

```php
<?php namespace Repositories\Pokemon;
// model\Repositories\Pokemon\PokemonRepositoryServiceProvider.php

use Entities\Pokemon;
use Repositories\Pokemon\PokemonRepository;
use Illuminate\Support\ServiceProvider;

/**
* Register our Repository with Laravel
* 註冊我們的資源庫到 Laravel
*/
class PokemonRepositoryServiceProvider extends ServiceProvider
{
    /**
    * Registers the pokemonInterface with Laravels IoC Container
    * 註冊 pokemonInterface 介面到 Laravel IoC Container 中
    */
    public function register()
    {
        // Bind the returned class to the namespace 'Repositories\PokemonInterface
        $this->app->bind('Repositories\Pokemon\PokemonInterface', function($app)
        {
            return new PokemonRepository(new Pokemon());
        });
    }
}
```

我們現在已經綁定我們的 PokemonRepository 資源庫到 PokemonInterface 介面中，一旦我們把這個服務提供者 (Service Provider) 加入到你的「app/config/app.php」當中（稍後會提到更多相關介紹），我們可以注入我們的資源庫到任何我們喜歡的地方，而 Laravel 將會注入這個類別。

假如我們決定去建立一個新的 PokemonRepository 資源庫，我們可以在這裡簡單地告訴 Laravel ，且在我們應用中所有的注入參照將會使用這個參照。

## 讓我們建立服務 (Service)！

就像資源庫 (Repository) 一樣，有三個階段去實作它，在我們不需要去替換類別時，我們不用去擔心有關介面的部分，但我們需要其他的服務提供者 (Service Provider)，Facade 類別及服務 (Service) 本身。

因為我們只有建置了一個我們的資源庫 (Repository)，讓我們開始來建立服務提供者 (Service Provider)，就像你的資源庫 (Repository) 一樣，我們需要在「model/services」裡建立一個新的「pokemon」資料夾給這個服務 (Service)。

```php
<?php namespace Services\Pokemon;
// model/Services/Pokemon/PokemonServiceServiceProvider.php

use Illuminate\Support\ServiceProvider;

/**
* Register our pokemon service with Laravel
* 註冊我們的 pokemon 服務到 Laravel
*/
class PokemonServiceServiceProvider extends ServiceProvider
{
    /**
    * Registers the service in the IoC Container
    * 註冊服務到 IoC Container
    */
    public function register()
    {
        // Binds 'pokemonService' to the result of the closure
        // 在閉合函式中綁定 'pokemonService'
        $this->app->bind('pokemonService', function($app)
        {
            return new PokemonService(
                // Inject in our class of pokemonInterface, this will be our repository
                // 注入我們的 pokemonInterface 介面，這個會是我們的資源庫
                $app->make('Repositories\Pokemon\PokemonInterface')
            );
        });
    }
}
```

所以就像先前一樣我們會告訴 Laravel 有關這個服務放置的位置並建立類別，然後．．．等等，那是什麼？

朋友啊，那個就是 Laravel 聰明的地方！Laravel 可以看到我們需要一個實作 PokemonInterface 介面的類別，所以 Laravel 就注入這個介面類別。

現在我們可以建立我們的服務 (Service)，它應該看起來會像這樣：

```php
<?php namespace Services\Pokemon;
// model/Services/Pokemon/PokemonService.php

use Repositories\Pokemon\PokemonInterface;

/**
* Our PokemonService, containing all useful methods for business logic around Pokemon
* 我們的 PokemonService 服務，包含所有在 Pokemon 的商業邏輯中有用的方法
*/
class PokemonService
{
    // Containing our pokemonRepository to make all our database calls to
    // 包含我們的 pokemonRepository 資源庫，去讓我們的資料庫去呼叫他
    protected $pokemonRepo;

    /**
    * Loads our $pokemonRepo with the actual Repo associated with our pokemonInterface
    * 使用真正的 Repo 資源庫去載入到我們的 $pokemonRepo，並關聯到我們的 pokemonInterface 介面
    *
    * @param pokemonInterface $pokemonRepo
    * @return PokemonService
    */
    public function __construct(pokemonInterface $pokemonRepo)
    {
        $this->pokemonRepo = $pokemonRepo;
    }

    /**
    * Method to get pokemon based either on name or ID
    * 透過 ID 或名稱取得 pokemon 資料
    *
    * @param mixed $pokemon
    * @return string
    */
    public function getPokemon($pokemon)
    {
        // If pokemon variable is numeric, assume ID
        // 假如變數是整數
        if (is_numeric($pokemon))
        {
            // Get pokemon based on ID
            // 使用 ID 去取得 Pokemon
            $pokemon = $this->pokemonRepo->getPokemonById($pokemon);
        }
        else
        {
            // Since not numeric, lets try get the pokemon based on Name
            // 非整數，嘗試使用名稱去取得 pokemon
            $pokemon = $this->pokemonRepo->getPokemonByName($pokemon);
        }

        // If Eloquent Object returned (rather than null) return the name of the pokemon
        // 若 Eloquent 物件有回傳資料 (非 null)，則回傳 pokemon 的名稱
        if ($pokemon != null)
        {
            return $pokemon->name;
        }

        // If nothing found, return this simple string
        // 如果沒有找到任何資料，則回傳這串簡單的字串
        return 'Pokemon Not Found';
    }
}
```

一個非常簡單的類別去顯示在你的服務 (Service) 裡去使用資源庫 (Repository)，當然這個方法可以變得更加複雜，較少強調在資源庫上，最後一個檔案去建立 Facade 類別，這是非常簡單且不需要額外解釋的類別：

```php
<?php namespace Services\Pokemon;
// model/Services/Pokemon/PokemonFacade.php

use \Illuminate\Support\Facades\Facade;

/**
* Facade class to be called whenever the class PokemonService is called
* Facade 類別當 PokemonService 被呼叫時呼叫
*/
class PokemonFacade extends Facade {

    /**
     * Get the registered name of the component. This tells $this->app what record to return
     * (e.g. $this->app[‘pokemonService’])
     * 取得註冊的元件名稱，這裡會告訴 $this->app 去回傳什麼樣的資料 (例如: $this->app[‘pokemonService’])
     *
     * @return string
     */
    protected static function getFacadeAccessor() { return 'pokemonService'; }

}
```

 - 服務提供者 (Service Provider)  - 檢查 OK

 - 服務 (Service) 類別  - 檢查 OK

 - Facade 類別  - 檢查 OK

在我們完成這個之前，我們必須要能自動載入我們剛剛新增的所有類別，在你的 composer.json 檔案中我們必須要在你的 class map 註冊新的資料夾目錄。

```json
"autoload": {
    "classmap": [
        "app/models/entities",
        "app/models/repositories",
        "app/models/repositories/pokemon",
        "app/models/services",
        "app/models/services/pokemon",
    ]
},
```

然後設定完後在你的網站根目錄執行 composer dump-autoload

現在剩下的是將設定加入到在你的 app/config 檔案中，在 app/config/app.php 檔案中你會看到提供者 (Provider) 的陣列，在這個陣列加入你服務提供者的命名空間 (namespace)

```php
<?php
// app/config/app.php
// ....
'providers' => array(
    'Repositories\Pokemon\PokemonRepositoryServiceProvider',
    'Services\Pokemon\PokemonServiceServiceProvider',
),
```

之後我們需要在相同的檔案中註冊 Facade：

```php
<?php
// app/config/app.php
// ....
'aliases' => array(
    'Pokemon' => 'Services\Pokemon\PokemonFacade',
),
```

然後我們完成了

## 看看下面動作的範例

我們已經建立了資源庫的相關範例，而我們也必須要有資料庫 migration 去測試這個教學範例

```shell
$ php artisan migrate:make create_pokemon_table --create=pokemon
```

```php
<?php
// app/database/migrations/2015_01_25_085024_create_pokemon_table.php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreatePokemonTable extends Migration {

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('pokemon', function(Blueprint $table)
        {
            // Auto increment
            $table->increments('id');
            $table->string('name' , 200);
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('pokemon');
    }
}
```

```shell
$ php artisan migrate
```

使用這些 migration 你可以簡單的建立一個像下面範例路由，嘗試得去用名稱或 ID 取得 pokemon

```php
<?php

// A nice simple route to show a pokemons name if we can find it using our Service
// 一個簡單的路由去顯示 pokemon 名稱，我們可以發現它使用我們的服務去取得 pokemon 資料
Route::any('/pokemon/{pokemon}', function($pokemon) {
    return Pokemon::getPokemon($pokemon);
});
```

## 注意 Facades 的部分

這裡有些意見關於 Facades 不應該用在 Laravel 中，藉由使用 Facade 你會讓你的 Laravel 應用很特殊並難以與 Laravel 分離，有一個比較好的解決方法是在你的 Controller 注入你的服務（Services），你可以到[這裡看更多](http://taylorotwell.com/response-dont-use-facades/)相關的資訊。

## 結論

很好，我在這裡學了很多有關使用這些模式的理由，希望你也跟我一樣，使用資源庫（Repositories）及服務（Services）我們可以正確的解構我們的程式碼，讓我們的程式更容易維護、重複使用並變的更性感，所有這些模式都是我了讓我們的應用變得更好，我非常歡迎所有的評論及改善建議。

就如一開始所提到的，這個就是我喜歡的工作方式，也有很多可替代的方案，假如這個風格適合你的話，麻煩你讓我知道一下！

# 參考資料
* [KeJyun學習日誌: 在 Laravel 4 使用資源庫 (Repositories) 及服務 (Services) 去降低程式的耦合性](http://blog.kejyun.com/2015/01/decoupling-your-code-in-laravel-using-repositiories-and-services.html)
* [Decoupling your code in Laravel using Repositiories and Services](http://dfg.gd/blog/decoupling-your-code-in-laravel-using-repositiories-and-services)
* [Response: Don’t Use Facades](http://taylorotwell.com/response-dont-use-facades/)
