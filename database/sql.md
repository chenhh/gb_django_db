# SQL

## SELECT子句與順序

| 子句       | 說明          | 是否必要使用       |
| -------- | ----------- | ------------ |
| SELECT   | 指定要返回的欄或表達式 | 是            |
| FROM     | 從中檢索資料的表    | 在從表格中選取資料時使用 |
| WHERE    | 列級過濾        | 否            |
| GROUP BY | 分組說明        | 在按組計算聚集時使用   |
| HAVING   | 組級(GROUP)過濾 | 否            |
| ORDER BY | 輸出排序順序      | 否            |

### DISTINCT&#x20;

```sql
-- 選出唯一的vend_id
SELECT DISTINCT vend_id FROM Products;
```

### LIMIT, OFFSET

```sql
-- 選出前5筆資料(從offset 0開始)
SELECT prod_name FROM Products LIMIT 5;
 
-- 選出前5筆資料(從offset 2開始)
SELECT prod_name FROM Products LIMIT 5 OFFSET 2;
```

### ORDER BY

```sql
-- 先依價格降序排列，再依產品名稱升序排列
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price DESC, prod_name ASC;
 
-- 排序也可以用欄位號碼，不必用欄位名稱
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY 2 DESC, 3 ASC;
```

### WHERE

```sql
SELECT prod_name, prod_price FROM Products WHERE prod_price = 3.49;
 
SELECT prod_name, prod_price FROM Products WHERE prod_price < 10;
 
-- <> 等價於 != 運算符
SELECT vend_id, prod_name FROM Products WHERE vend_id <> 'DLL01';
```

### BETWEEN AND

```sql
-- BETWEEN 前面的數字必須小於後面的數字
SELECT prod_name, prod_price
FROM Products
WHERE prod_price BETWEEN 5 AND 10;
```

### NULL

```sql
-- NULL表示為沒有值，與空字串, 0意義不同
SELECT prod_name FROM Products WHERE prod_price IS NULL;
```

### AND

```sql
-- AND條件
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' AND prod_price <= 4;
```

### OR

```sql
-- OR 條件
SELECT prod_name, prod_price
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = ‘BRS01’;
```

### AND優先權高於OR

```sql
/* 列出價格10元以上，且由DLL01 或BRS01 制造的所有產品
 * 因為AND的優先權較高，所以必須將OR用括號提升優先權才可得到正確結果
 */
SELECT prod_name, prod_price
FROM Products
WHERE (vend_id = 'DLL01' OR vend_id = 'BRS01')
AND prod_price >= 10;
```

### IN

```sql
-- 此處IN的功能等價於vend_id='DLL01' OR vend_id='BRS01
SELECT prod_name, prod_price
FROM Products
WHERE vend_id IN ( 'DLL01', 'BRS01' )
ORDER BY prod_name;
```

### NOT

```sql
-- NOT條件
SELECT prod_name
FROM Products
WHERE NOT vend_id = 'DLL01' -- 等價於 !=, <>
ORDER BY prod_name;
```

### LIKE

* `%`表示任意字元可以出現零次以上。
* 當LIKE以`%`開頭的字串搜尋時，通常不會使用索引。
* `_`代表任意字元出現一次。

```sql
-- 以Fish開頭的字串
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE 'Fish%';

-- 包含bean bag的字串
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '%bean bag%';

-- 匹配剛好有2個字元的字串
SELECT prod_id, prod_name
FROM Products
WHERE trim(prod_name) LIKE '__ inch teddy bear';l
```
