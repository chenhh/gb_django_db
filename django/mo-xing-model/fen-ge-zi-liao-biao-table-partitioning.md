# 分割資料表(table partitioning)

## 簡介

目前在django只有postgresql(>=11)支援分割資料表。

分割資料表指的是將一個大型資料表以邏輯規則實體拆分為較小的資料庫。分割資料表可以帶來以下好處。優點如下：

* 在某些情況下，尤其是當資料表中大多數被頻繁存取的資料位於單個分割區或少量的分割區之中時，查詢效能可以顯著地提高。
* 當查詢或更新存取單個分割區的很大一部分時，可以透過對該分割區進行循序掃描而不是使用索引和遍及整個資料表的隨機讀取來提高效能。
* 如果計劃程序將這種需求計劃在分割區的設計中，則可以透過增加或刪除分區來完成批次加入和刪除。使用 ALTER TABLE DETACH PARTITION 或使用 DROP TABLE 刪除單個分割區比批次操作要快得多。這些命令還完全避免了由批次 DELETE 所增加的 VACUUM 成本。

資料表可以從分割區中受益的確切評估點取決於應用程式，<mark style="color:red;">**經驗法則是資料表的大小超過資料庫伺服器的記憶體大小的時候**</mark>。

## PostgreSQL內建支援分割資料表

PostgreSQL 內建支援以下形式的分割方式：

* <mark style="color:red;">range partitioing</mark>：此資料庫表的分割區以一個欄位為鍵或一組欄位定義的「range」來分配，分配給不同分割區的範圍之間沒有重疊。例如，可以按日期範圍或特定業務對象的標識範圍進行分割。
* <mark style="color:red;">list partitioning</mark>：透過明確列出哪些鍵值出現應該在哪個分割區中來對資料表進行分割。
* <mark style="color:red;">hash partitioing</mark>：透過為每個分割區指定除數和餘數來對資料表進行分割。每個分割區將保留其分割鍵的雜湊值除以指定的除數所產生指定的餘數的資料列。

1. 宣告分割資料表

```sql
CREATE TABLE measurement (
    city_id         int not null,
    logdate         date not null,
    peaktemp        int,
    unitsales       int
) PARTITION BY RANGE (logdate);
```

2\. 建立分割

```sql
-- 建立分割的邊界
CREATE TABLE measurement_y2006m02 PARTITION OF measurement
    FOR VALUES FROM ('2006-02-01') TO ('2006-03-01');

CREATE TABLE measurement_y2006m03 PARTITION OF measurement
    FOR VALUES FROM ('2006-03-01') TO ('2006-04-01');

...
CREATE TABLE measurement_y2007m11 PARTITION OF measurement
    FOR VALUES FROM ('2007-11-01') TO ('2007-12-01');

-- 可指定tablespace
CREATE TABLE measurement_y2007m12 PARTITION OF measurement
    FOR VALUES FROM ('2007-12-01') TO ('2008-01-01')
    TABLESPACE fasttablespace;

CREATE TABLE measurement_y2008m01 PARTITION OF measurement
    FOR VALUES FROM ('2008-01-01') TO ('2008-02-01')
    WITH (parallel_workers = 4)
    TABLESPACE fasttablespace;
```

也可宣告第二層的分割

```sql
CREATE TABLE measurement_y2006m02 PARTITION OF measurement
    FOR VALUES FROM ('2006-02-01') TO ('2006-03-01')
    PARTITION BY RANGE (peaktemp);
```

3\. 建立索引

```sql
CREATE INDEX ON measurement (logdate);
```

## 維護分割表

### 刪除分割表

```sql
DROP TABLE measurement_y2006m02;
```

### 移出分割表但不刪除

```sql
ALTER TABLE measurement DETACH PARTITION measurement_y2006m02;
```

## 分割區資料表的限制

* 無法建立跨所有分割區的限制條件。只能單獨對每個分割區設定。&#x20;
* <mark style="color:red;">分割區資料表上的唯一性限制條件必須包含所有分割主鍵欄位</mark>。存在此限制是因為 PostgreSQL 只能在每個分割區中個別實施唯一性。 必要時，必須在單個分割區（而不是分割資料表）上定義 BEFORE ROW 觸發器。&#x20;
* 不允許在同一分割區中混合臨時和永久關連。因此，如果分割資料表是永久性的，則分割區也必須是永久性的，或者都臨時的。使用臨時關連時，分割資料表的所有成員必須來自同一個資料庫連線。

## django建立分割表



## 參考資料

* [django-postgres-extra](https://django-postgres-extra.readthedocs.io/en/master/table\_partitioning.html)
*
