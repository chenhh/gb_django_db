---
description: host-based authentication
---

# pg\_hba.conf

## 簡介

用戶端身份驗證由組態檔案控制，組態檔案通常名稱為 pg\_hba.conf，並儲存在資料庫叢集的資料目錄中。 （HBA 代表 host-based authentication。）當 initdb 初始化資料目錄時，將安裝預設的 pg\_hba.conf 檔案。但是，可以將身份驗證組態檔案放在其他路徑；

每條記錄指定連線類型，用戶端 IP 位址範圍（如果與連線類型相關）、資料庫名稱、使用者名稱以及符合這些參數的連線身份驗證方法。具有符合的連線類型、用戶端位址、要求的資料庫和使用者名稱的第一個記錄用於執行身份驗證。沒有“fall-through”或“replication”：如果選擇了一條記錄而認證失敗，就不再考慮後續記錄。如果沒有記錄匹配，則拒絕存取。

記錄可以是下面的七種格式之一：

```
local      database  user  auth-method  [auth-options]
host       database  user  address  auth-method  [auth-options]
hostssl    database  user  address  auth-method  [auth-options]
hostnossl  database  user  address  auth-method  [auth-options]
host       database  user  IP-address  IP-mask  auth-method  [auth-options]
hostssl    database  user  IP-address  IP-mask  auth-method  [auth-options]
hostnossl  database  user  IP-address  IP-mask  auth-method  [auth-options]
```

## 參考資料

* [https://docs.postgresql.tw/server-administration/client-authentication/the-pg\_hba.conf-file](https://docs.postgresql.tw/server-administration/client-authentication/the-pg\_hba.conf-file)
