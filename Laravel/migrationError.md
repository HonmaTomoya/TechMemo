# マイグレーションエラー

## connection refusedが発生する
ホストでマイグレーションを行うと `connection refused` エラーが発生する。
※仮想マシンでは発生しない。

### エラーメッセージ
```
tomoya:Laravel tomoyahonma$ php artisan migrate

   Illuminate\Database\QueryException  : SQLSTATE[HY000] [2002] Connection refused (SQL: select * from information_schema.tables where table_schema = homestead and table_name = migrations and table_type = 'BASE TABLE')

  at /Users/tomoyahonma/projects/Laravel/vendor/laravel/framework/src/Illuminate/Database/Connection.php:664
    660|         // If an exception occurs when attempting to run a query, we'll format the error
    661|         // message to include the bindings with SQL, which will make this exception a
    662|         // lot more helpful to the developer instead of just the database's errors.
    663|         catch (Exception $e) {
  > 664|             throw new QueryException(
    665|                 $query, $this->prepareBindings($bindings), $e
    666|             );
    667|         }
    668|

  Exception trace:

  1   PDOException::("SQLSTATE[HY000] [2002] Connection refused")
      /Users/tomoyahonma/projects/Laravel/vendor/laravel/framework/src/Illuminate/Database/Connectors/Connector.php:70

  2   PDO::__construct("mysql:host=127.0.0.1;port=3306;dbname=homestead", "homestead", "secret", [])
      /Users/tomoyahonma/projects/Laravel/vendor/laravel/framework/src/Illuminate/Database/Connectors/Connector.php:70

  Please use the argument -v to see more details.
tomoya:Laravel tomoyahonma$
```

### 原因
`config/database.php -> .env` で設定するデータベースの指定先が間違っている。

### 対処法
`.env` の `DB_HOST` をホスト、仮想マシンに共通する内容へ修正する。

```
# .env
DB_CONNECTION=mysql
# DB_HOST=127.0.0.1 # before
DB_HOST=homestead.test # after
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```
