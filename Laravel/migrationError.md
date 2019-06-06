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
`config/database.php -> .env` で設定するデータベースの指定先が間違っていた。

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

## 外部キー制約を定義したテーブルのマイグレーションが失敗する
`php artisan migrate` を実行した際に、外部キー制約の問題で下記のエラーが発生する。

### エラーメッセージ
```
tomoya:Laravel tomoyahonma$ php artisan migrate
Migrating: 2019_06_05_073451_create_books_table

   Illuminate\Database\QueryException  : SQLSTATE[HY000]: General error: 1215 Cannot add foreign key constraint (SQL: alter table `books` add constraint `books_author_id_foreign` foreign key (`author_id`) references `authors` (`id`))

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

  1   PDOException::("SQLSTATE[HY000]: General error: 1215 Cannot add foreign key constraint")
      /Users/tomoyahonma/projects/Laravel/vendor/laravel/framework/src/Illuminate/Database/Connection.php:458

  2   PDOStatement::execute()
      /Users/tomoyahonma/projects/Laravel/vendor/laravel/framework/src/Illuminate/Database/Connection.php:458

  Please use the argument -v to see more details.
tomoya:Laravel tomoyahonma$
```

### 原因
マイグレーションで定義するテーブル(カラム)の定義順番に問題があった。
books(外部キー制約を設定するテーブル)を先に、authors(参照テーブル)を後にマイグレーションするようになっていた。

### 対処法
下記2つの対処法がある。
1. マイグレーションの実行順番を変更
  実行順番はファイル名に依存するため、ファイル名のタイムスタンプを変更する。
1. 別途、外部キー制約を行うマイグレーションを追加
  外部キー制約を付与する処理が参照するテーブルの作成後になるように、別途マイグレーションを追加する。
