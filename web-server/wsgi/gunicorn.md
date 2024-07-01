# gunicorn

## 簡介

```bash
# 使用 Gunicorn 伺服器運行 Django 應用程式
cd ~/myprojectdir
gunicorn -w 2 --bind 0.0.0.0:8000 myproject.wsgi
```

但是在這裡使用 Gunicorn 伺服器時，靜態（static）檔案會出現找不到的問題，這是正常的。這裡我們同樣檢查 http://server\_domain\_or\_IP:8000/ 這個頁面，除了靜態檔案之外是否都可以正常運作，若正常就表示沒問題。

gunicorn --worker-class=gevent --worker-connections=1000 --workers=3 main:app

worker-connections 是對於 gevent worker 類的特殊設定。(2\*CPU)+1 仍然是建議的workers 數量。

## Gunicorn 各個 worker type 適合的情境

根據底層運作的原理可以將 worker 分成三種類型：

* `sync` 底層實作是每個請求都由一個 process 處理。&#x20;
* `gthread`則是每個請求都由一個 thread 處理。
* `eventlet`、`gevent`、`tarnado` 底層則是利用非同步 IO 讓一個 process 在等待 IO 回應時繼續處理下個請求。

### sync

用 process 處理請求。當 gunicorn worker type 使用 sync 時，web 啟動時會預先開好對應數量的 process 處理請求，理論上 concurrency 的上限等同於 worker 數量。

* 這種類型的好處是錯誤隔離高，一個 process 掛掉只會影響該 process 當下服務的請求，而不會影響其他請求。
* 壞處則為 process 資源開銷較大，開太多 worker 時對記憶體或 CPU 的影響很大，因此 concurrency 理論上限極低。

### gthread

當 gunicorn worker type 用 gthread 時，可額外加參數 --thread 指定每個 process 能開的 thread 數量，此時 concurrency 的上限為 worker 數量乘以給個 worker 能開的 thread 數量。

這種類型的 worker 好處是 concurrency 理論上限會比 process 高，壞處依然是 thread 數量，OS 中 thread 數量是有限的，過多的 thread 依然會造成系統負擔。

### 非同步IO

當 gunicorn worker type 用 eventlet、gevent、tarnado 等類型時，每個請求都由同一個 process 處理，而當遇到 IO 時該 process 不會等 IO 回應，會繼續處理下個請求直到該 IO 完成，理論上 concurrency 無上限。

但當面臨 CPU bound 請求時，則會退化成用 process 處理請求一樣，concurrency 上限為 worker 數量。

因此使用非同步類型的 worker 好處和壞處非常明顯，對 IO bound task 的高效能，但在 CPU bound task 會不如 thread。

### 小結

* 需要穩定的系統時， 用 process 處理請求可以保證一個請求的異常導致程式 crash 不會影響到其他請求。
* 當 web 服務內大部分都是 cpu 運算時，用 thread 可以提供不錯的效能。
* 當 web 服務內大部分都是 io 時，用非同步 io 可以達到極高的 concurrency 數量。

## gunicorn配置nginx

## 參考資料

* [gunicorn offical site](https://gunicorn.org/)
* [https://officeguide.cc/ubuntu-linux-django-with-postgres-nginx-and-gunicorn-development-deployment-tutorial-examples/](https://officeguide.cc/ubuntu-linux-django-with-postgres-nginx-and-gunicorn-development-deployment-tutorial-examples/)
