# 資料庫正規化

## 簡介

資料庫正規化，又稱正規化、標準化，是資料庫設計的一系列原理和技術，以減少資料庫中數據冗餘，增進數據的一致性。 現在資料庫設計最多滿足3NF，普遍認為正規化過高，雖然具有對數據關係更好的約束性，但也導致數據關係表增加而令資料庫IO更易繁忙。

將原先關聯表格的所有資訊，在分解成其他表格後，仍可以透過「合併」新關聯表格的方式得到相同的資訊，亦為「無損失分解」（Lossless Decomposition）。

正規化的目的就是為了刪除「重複的資料」(Data Redundancy)及「避免更新異常」(Anomalies)，每個階段都是以「欄位的相依性」作為分割資料表的依據，在實務上通常以滿足3NF即可。

## 不當設計造成的異常(CRUD異常)&#x20;

新增異常：新增時，資料不齊全。

刪除異常：刪除時，造成資料遺失。

更新異常：更新時，可能會漏改。

## 正規化步驟

先把原始資料儲存在資料表內（未正規化的資料表)。

### 未符合第一正規化造成的問題&#x20;

單一個欄位內有超過1個以上的值。&#x20;

欄位長度無法確定（可能很多，也可能很少），必須預留空間，造成儲存空間的浪費。

### 第一正規化（1NF）&#x20;

定義&#x20;

1. 要排除重複群的出現，要求每一列資料的欄位值都只能是單一值（基元值）。&#x20;
2. 沒有任何兩筆以上的資料完全重複。&#x20;
3. 資料表中需有主鍵（唯一值），其他所有欄位都相依於主鍵。&#x20;
4. 同一張資料表內，不建議用多個欄位表達同一個事情。

作法&#x20;

* 確認是否有重複表達的欄位。&#x20;
* 將欄位內重複的資料分別存為不同的Data Row資料。

### 第二正規化（2NF）

定義&#x20;

1. 符合1NF。&#x20;
2. 消除「部分功能相依」，每一個非鍵欄位必須完全相依主鍵（學號=>學生，課程=>學分…），通常「主鍵有多個欄位」組成時會發生「部分功能相依」。

**作法**

1. 檢查是否存在「部分功能相依」（可從多個欄位組成的主鍵開始檢查）。
2. 將「部分功能相依」的欄位分割出去，另外組成新的資料表。

## 第三正規化（3NF）&#x20;

定義&#x20;

1. 符合2NF。&#x20;
2. 各欄位之間沒有存在「遞移相依」的關係，也就是與「主鍵」無關的相依性。

作法&#x20;

1. 檢查是否存在「遞移相依」的欄位。&#x20;
2. 將「遞移相依」的欄位分割出去，另外組成新的資料表。

## Boyee-Codd正規化（BCNF）&#x20;

視情況使用，實務上大多都只會做到3NF。

定義&#x20;

1. 3NF的改良式(必須滿足3NF)。&#x20;
2. 主鍵中的各欄位（單獨看）不可以相依於其他非主鍵的欄位。

作法&#x20;

1. 確認由多個欄位組成的主鍵是否有其他欄位相依於主鍵中的每個欄位。&#x20;
2. 如有獨立相依的情況，新增一個獨立的主鍵欄位。
