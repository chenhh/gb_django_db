---
description: http連線壓力測試
---

# ab

## 簡介

ab，即 Apache Benchmark，只要我們安裝了 Apache，就能夠在 Apache 的安裝目錄中找到它。

```bash
# 同時 10 個連線，連續點擊 10000 次 ( 每個 Request 執行完畢後都會自動斷線，然後再重新連線 )
$ab -n 10000 -c 10 http://www.example.com/index.aspx

# 同時 10 個連線，連續點擊 10000 次，並且使用 Keep-Alive 方式連線
#（當 Web Server 有支援 Keep-Alive 功能時 ApacheBench 會在同一個連線下連續點擊該網頁）
ab -n 10000 -c 10 -k http://www.example.com/index.aspx

# 將測試的效能原始資料匯出成 CSV 檔
ab -e output.csv -n 10000 -c 10 http://www.example.com/index.aspx
```

## 測試報告

壓力測試的核心在於如何分析結果。如果你只要看重點的話，可以只看 Failed requests、Requests per second、與 Time per request 這三個參數。

其中的 Failed requests 的數量太高的話，很有可能代表 Web Application 的穩定度不夠，而導致使用大量要求時無法回應需求 。而 Request per second 代表每秒可送出的回應數有多少，代表 Web Application 的承載量有多少（在不考慮頻寬限制的情況下）。

```bash
Benchmarking localhost (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests

Server Software:        Apache/2.2.25 (伺服器軟體名稱及版本資訊)
Server Hostname:        localhost (伺服器主機名)
Server Port:            80 (伺服器連接埠)
Document Path:          /index.php (供測試的URL路徑)
Document Length:        10 bytes (供測試的URL返回的文件大小)
Concurrency Level:      100 (並行數)
Time taken for tests:   0.247 seconds (壓力測試消耗的總時間)
Complete requests:      1000 (壓力測試的總次數)
Failed requests:        0 (失敗的請求數)
Write errors:           0 (網路連線寫入錯誤數)
Total transferred:      198000 bytes (傳輸的總資料量)
HTML transferred:       10000 bytes (HTML文件的總資料量)
Requests per second:    4048.34 [#/sec] (mean) (平均每秒的請求數)
Time per request:       24.701 [ms] (mean) (所有並行使用者(這裡是100)都請求一次的平均時間)
Time per request:       0.247 [ms] (mean, across all concurrent requests) (單個使用者請求一次的平均時間)
Transfer rate:          782.78 [Kbytes/sec] received (傳輸速率，單位：KB/s)
Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.3      0       1
Processing:     6   23   4.2     24      30
Waiting:        5   20   5.3     21      29
Total:          6   23   4.2     24      30

Percentage of the requests served within a certain time (ms)
  50%     24
  66%     25
  75%     26
  80%     26
  90%     27
  95%     27
  98%     28
  99%     29
 100%     30 (longest request)
```

### 增加Linux連結數

如果請求量較大 linux 客戶連接埠會報錯連結太多，可執行命令調整上限：`ulimit -n 20000`。



## 參考資料

* [https://httpd.apache.org/docs/2.4/programs/ab.html](https://httpd.apache.org/docs/2.4/programs/ab.html)
