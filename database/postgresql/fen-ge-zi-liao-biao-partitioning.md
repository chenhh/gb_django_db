# 分割資料表(partitioning)

## 簡介

PostgreSQL 11.x後支援。

分割資料表指的是將一個大型資料表以邏輯規則實體拆分為較小的資料庫。

通常只有在資料表很大的情況下，這些好處才是值得的。資料表可以從分割區中受益的確切評估點取決於應用程式，<mark style="color:blue;">經驗法則是資料表的大小超過資料庫伺服器的記憶體大小的時候才分割表格</mark>。

什麼是分表： 把一個大表分成若干分區，使其物理(硬碟)上分散，邏輯上連續。各分區繼承一個母表，對母表進行的操作會自動同步到各分區（包括插入數據時按照分表方式自動導入不同分區、建立索引等）。

## 分表的優點

單表資料量過大，會影響查詢效能。

&#x20;分表的優點：

* 顯著提升查詢效能，尤其是當頻繁訪問的記錄在同一個分區或少數幾個分區中時。&#x20;
* 分區索引在一定程度上替代了傳統的索引，可以減小索引佔用的空間。
* 批次讀取和刪除可以以分區為單位完成，例如detach partition這種操作速度遠遠快於bulk delete操作。

## 支援的分表型式

PostgreSQL 內建支援以下形式的分割方式：

* <mark style="color:red;">Range Partitioning</mark>：此資料庫表的分割區以一個欄位為鍵或一組欄位定義的「range」來分配，分配給不同分割區的範圍之間沒有重疊。例如，可以按日期範圍或特定業務對象的標識範圍進行分割。
* <mark style="color:red;">List Partitioning</mark>：透過明確列出哪些鍵值出現應該在哪個分割區中來對資料表進行分割。
* <mark style="color:red;">Hash Partitioning</mark>：透過為每個分割區指定除數和餘數來對資料表進行分割。每個分割區將保留其分割鍵的雜湊值除以指定的除數所產生指定的餘數的資料列。

## 創建分區表

by range

```sql
-- 建立母表
CREATE TABLE measurement (
    city_id         int not null,
    logdate         date not null,
    peaktemp        int,
    unitsales       int
) PARTITION BY RANGE (logdate);

-- 建立分區
CREATE TABLE measurement_y2006m02 PARTITION OF measurement
    FOR VALUES FROM ('2006-02-01') TO ('2006-03-01');

CREATE TABLE measurement_y2006m03 PARTITION OF measurement
    FOR VALUES FROM ('2006-03-01') TO ('2006-04-01');
```

## 創建索引

對母表進行操作即可，已存在的分區和後續創建的分區上也會包含該索引。

```sql
CREATE INDEX ON measurement (logdate);
```

## 刪除分區

可以快速刪除數以百萬計的數據。(較少使用)

```sql
DROP TABLE measurement_y2006m02;
```

但通常使用另一種方式（detach）：將分區分離出來作為獨立的表，可以在刪除前進行備份、聚合、統計等操作。

```sql
ALTER TABLE measurement DETACH PARTITION measurement_y2006m02;
```

## 在哪些列上建立分區

經常出現在WHERE子句中的列（或多個列）適合。這樣可以快速排除掉不相關的分區。此外，將分區作為整體進行刪除非常快，所以如果有能一起移除的數據可以放在同一個分區（如業務數據按時間分區）。

## 分區數量

分區數量過多或過少都會影響效能。分區數量過多會導致planning time變長（可用explain命令檢視，參考）、記憶體開銷變大。（文檔原文：可以很好地處理a few thousand個分區）&#x20;

使用hash方法時建議為2的倍數，這樣未來如果有分區過大需要進一步分區時，可以直接分裂分區，而不需要將資料跨分區遷移。
