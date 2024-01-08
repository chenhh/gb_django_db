# postgresql.conf

## 簡介

docker中的範本路徑為：`/usr/share/postgresql/postgresql.conf.sample`。

設定檔的路徑不限，通常放在：`/etc/postgresql/postgresql.conf`。但在執行時要用參數-c明確指向設定檔路徑。

```bash
$ # get the default config
$ docker run -i --rm postgres cat \
  /usr/share/postgresql/postgresql.conf.sample > my-postgres.conf

$ # customize the config

$ # run postgres with custom config
$ docker run -d --name some-postgres -v \
  "$PWD/my-postgres.conf":/etc/postgresql/postgresql.conf \
  -e POSTGRES_PASSWORD=mysecretpassword postgres \
  -c 'config_file=/etc/postgresql/postgresql.conf'
```

## 優化參數選項

| 選項                             | 預設值   | 說明                                                            | 是否優化 | 原因                                       |
| ------------------------------ | ----- | ------------------------------------------------------------- | ---- | ---------------------------------------- |
| max\_connections               | 100   | 允許使用者端連線的最大數目                                                 | 否    | 通常100個連線已經足夠                             |
| fsync                          | on    | 強制把資料同步更新到磁碟                                                  | 是    | 因為系統的IO壓力很大，為了更好的測試其他設定的影響，把改引數改為off     |
| shared\_buffers                | 24MB  | 決定有多少記憶體可以被PostgreSQL用於快取資料（推薦記憶體的1/4)                        | 是    | 在IO壓力很大的情況下，提高該值可以減少IO                   |
| work\_mem                      | 1MB   | 使內部排序和一些複雜的查詢都在這個buffer中完成                                    | 是    | 有助提高排序等操作的速度，並且減低IO                      |
| effective\_cache\_size         | 128MB | 優化器假設一個查詢可以用的最大記憶體，和shared\_buffers無關（推薦記憶體的1/2)              | 是    | 設定稍大，優化器更傾向使用索引掃描而不是順序掃描                 |
| maintenance\_work\_mem         | 16MB  | 這裡定義的記憶體只是被VACUUM等耗費資源較多的命令呼叫時使用                              | 是    | 把該值調大，能加快命令的執行                           |
| wal\_buffer                    | 768kB | 紀錄檔快取區的大小                                                     | 是    | 可以降低IO，如果遇上比較多的並行短事務，應該和commit\_delay一起用 |
| checkpoint\_segments           | 3     | 設定wal log的最大數量數（一個log的大小為16M）                                 | 是    | 預設的48M的快取是一個嚴重的瓶頸，基本上都要設定為10以上           |
| checkpoint\_completion\_target | 0.5   | 表示checkpoint的完成時間要在兩個checkpoint間隔時間的N%內完成                     | 是    | 能降低平均寫入的開銷                               |
| commit\_delay                  | 0     | 事務提交後，紀錄檔寫到wal log上到wal\_buffer寫入到磁碟的時間間隔。需要配合commit\_sibling | 是    | 能夠一次寫入多個事務，減少IO，提高效能                     |
| commit\_siblings               | 5     | 設定觸發commit\_delay的並行事務數，根據並行事務多少來設定                           | 是    | 減少IO，提高效能                                |

## Linux核心參數

可先用`sysctl -a|grep shm` 檢查共享記憶體的參數。

主要看 `kernel.shmmax`與`kernel.shmall` 是否符號需求值，預設應該足夠。

## pgbench參數調校

首先建立資料庫pgbench，然後使用pgbench初始化測試資料。

#### 初始化選項

* `-i`:表示要進行資料庫初始化。會建立 4 個表格：pgbench\_accounts、pgbench\_branches、pgbench\_history、以及 pgbench\_tellers。
* `-n`:在初始化後不要進行資料庫整理（vacuum）的動作。
* `-s`:資料的數量是以 scale factor 的倍數來計算的。預設為1即在pgbench\_accounts中產生1萬筆資料。

#### 評估階段專用選項

* `-c`:模擬用戶的數量，指的是同一時間連入資料庫的連線數。預設為 1。
* `-C`:在每一個交易執行前都重新建立連線，而不是都在同一個用戶連線中完成全部交易。這在測試連線成本時特別有用。
* `-j`:執行緒的數量，能夠有效利用多 CPU 的運算能力。模擬用戶會盡可能平均分配在不同執行緒中執行。預設值為 1。。
* `-r`:回報每一個指令中每個語的平均回應時間。
* `-t`:每一個模擬用戶端要執行的交易數量，預設為 10。
* `-T`:執行限時測試（以秒為單位），而不是固定的交易數量。-t 和 -T 是互斥的選項。

```
# 使用pgadmin或psql建立pgbench
# 初始化資料庫，-s為scaling factor，即建立幾倍(1倍為10萬筆)的測試資料。
pgbench -i -s 20 pgbench

/bin/pgbench -h 192.168.0.167 -p 5432 -U postgres -r -j8 -c20 -T60 pgbench
```

pgbench 所產出的，TPS (Transactions per second)，越高越好。「including」和「excluding」則分別代表了「including connections establishing」及「excluding connections establishing」，也就是是否採計建立連線所需時間。很明顯就可以看到，連線時間的成本並不低。

測試的過程中，主要的瓶頸就在系統的IO，如果需要減少IO的負荷，最直接的方法就是把fsync關閉，但是這樣就會在掉電的情況下，可能會丟失部分資料。

以下為eva01主機使用postgresql 16.1(docker參數未調整，使用內網ip)的結果：

| 參數集               | TPS(不含建立連線) | TPS(-C, 含建立連線) |
| ----------------- | ----------- | -------------- |
| 預設參數              | 4349        | 584            |
| pgtune的參數         | 4507        |                |
| pgtune, fsync=off | 5780        | 549            |

可在psql或在pgadmin中，使用`SHOW ALL` 查看資料庫參數是否正確設定。

## pgadmin in docker

當使用的資料庫版本太新時，舊版的pgadmin可能無法連線，可改用docker版本的pgadmin。

```bash
#!/bin/bash
# PGADMIN_DATA的資料夾擁有者要自行改為5050:0,否則docker logs pgadmin會報錯
docker run -p 127.0.0.1:5050:80 \
    -v ${PGADMIN_DATA}:/var/lib/pgadmin \
    -e 'PGADMIN_DEFAULT_EMAIL=my@mail.com' \
    -e 'PGADMIN_DEFAULT_PASSWORD=mypwd' \
    --name pgadmin \
    -d dpage/pgadmin4

```

pgbench 所產出的TPS (Transactions per second)，越高越好。「including」和「excluding」則分別代表了「including connections establishing」及「excluding connections establishing」，也就是是否採計建立連線所需時間。

## 參考資料

* [https://www.postgresql.org/docs/current/config-setting.html](https://www.postgresql.org/docs/current/config-setting.html)
* [https://pgtune.leopard.in.ua/](https://pgtune.leopard.in.ua/)
* [https://www.pgadmin.org/docs/pgadmin4/latest/container\_deployment.html](https://www.pgadmin.org/docs/pgadmin4/latest/container\_deployment.html)

pgbench

* [https://docs.postgresql.tw/reference/client-applications/pgbench](https://docs.postgresql.tw/reference/client-applications/pgbench)
* [https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072](https://pankajconnect.medium.com/performance-tuning-postgresql-containers-in-a-docker-environment-89ca7090e072)
