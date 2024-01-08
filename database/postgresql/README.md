# PostgreSQL

## 環境變數

* PGDATA: 存放資料的目錄。docker image預設路徑為/var/lib/postgresql/data。
* POSTGRES\_USER/POSTGRES\_PASSWORD: 使用者/密碼。
* POSTGRES\_INITDB\_ARGS: 初始化資料庫(initdb)的參數。

## 預設資料庫(database)

PostgreSQL安裝好後，會先創建postgres，template0和template1三個空資料庫。&#x20;

* postgres用作登錄時的預設資料庫，也包含了資料庫的設定表格。
* template0和template1是範本資料庫，用於創建其他資料庫。&#x20;
* 資料庫的資訊存儲在pg\_database表中，也可以在psql下使用\list命令查看。

## 參考資料

### 手冊

* [PostgreSQL official site](https://www.postgresql.org/)
  * [版本特性矩陣](https://www.postgresql.org/about/featurematrix/)
* [\[docker\] Postgres](https://hub.docker.com/\_/postgres)
* [\[official\] PgSQL 14.0 文件(en)](https://www.postgresql.org/docs/14/index.html)
* [\[official\] PgSQL 14.0 文件(cn)](http://www.postgres.cn/docs/14/index.html)
* [\[github\] 德哥blog](https://github.com/digoal/blog)
* [\[Pg16\] PostgreSQL正體中文使用手冊](https://docs.postgresql.tw/)
* [https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072](https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072)

### 新聞

* [PostgreSQL 16 五大更新－權限管理、邏輯複寫、使用體驗與效能升級](https://www.omniwaresoft.com.tw/product-news/edb-news/postgresql-16-latest-release/)

