# nginx

### docker安裝

從docker hub可查詢[nginx](https://hub.docker.com/\_/nginx)目前最新版本，如果要抓取特定版本使用`docker pull nginx:1.27.0`，或者直接以`docker pull nginx` 抓取最新版本。

下載完成後，可使用`docker images` 檢視已抓取的映像版本。

建議新增使用者nginx專用管理nginx伺服器，記得要加入docker群組。

* 新增使用者且指定群組：`sudo useradd -g docker nginx`。預設建立家目錄在`/home/nginx`。
* 修改密碼：`sudo passwd nginx`。

#### nginx容器的設定

nginx容器內的設定檔可用host設定檔覆寫：`-v ${SERVER_DIR}/my_nginx.conf:/etc/nginx/conf.d/default.conf`。

可先將容器內的設定檔拷貝出來再修改：`docker run --rm --entrypoint=cat nginx /etc/nginx/nginx.conf > /${SERVER_DIR}/my_nginx.conf`。

容器內部的資料夾也可以用host資料夾：`-v ${SERVER_DIR}/my_static/:/opt/static/` 。

容器的log檔(access.log與error.log)預設是在容器中，只要刪除容器就會消失，如果要永久儲存要掛載到host資料夾：`-v ${SERVER_LOG_DIR}:/var/log/nginx/`。

### 常用nginx命令

首先進入nginx容器內：`docker exec -it nx /bin/bash`。

```bash
root@6ea974697f67:/etc/nginx# nginx -h   
nginx version: nginx/1.25.3
Usage: nginx [-?hvVtTq] [-s signal] [-p prefix]
             [-e filename] [-c filename] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /etc/nginx/)
  -e filename   : set error log file (default: /var/log/nginx/error.log)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
```

#### 查看編譯的選項

`nginx -V`。

```bash
root@6ea974697f67:/etc/nginx# nginx -V
nginx version: nginx/1.25.3
built by gcc 12.2.0 (Debian 12.2.0-14) 
built with OpenSSL 3.0.9 30 May 2023 (running with OpenSSL 3.0.11 19 Sep 2023)
TLS SNI support enabled
configure arguments: 
--prefix=/etc/nginx 
--sbin-path=/usr/sbin/nginx 
--modules-path=/usr/lib/nginx/modules 
--conf-path=/etc/nginx/nginx.conf 
--error-log-path=/var/log/nginx/error.log 
--http-log-path=/var/log/nginx/access.log 
--pid-path=/var/run/nginx.pid 
--lock-path=/var/run/nginx.lock 
--http-client-body-temp-path=/var/cache/nginx/client_temp 
--http-proxy-temp-path=/var/cache/nginx/proxy_temp 
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp 
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp 
--http-scgi-temp-path=/var/cache/nginx/scgi_temp 
--user=nginx 
--group=nginx 
--with-compat 
--with-file-aio 
--with-threads 
--with-http_addition_module 
--with-http_auth_request_module 
--with-http_dav_module 
--with-http_flv_module 
--with-http_gunzip_module 
--with-http_gzip_static_module 
--with-http_mp4_module 
--with-http_random_index_module 
--with-http_realip_module 
--with-http_secure_link_module 
--with-http_slice_module 
--with-http_ssl_module 
--with-http_stub_status_module 
--with-http_sub_module 
--with-http_v2_module 
--with-http_v3_module 
--with-mail 
--with-mail_ssl_module 
--with-stream 
--with-stream_realip_module 
--with-stream_ssl_module 
--with-stream_ssl_preread_module 
--with-cc-opt='-g -O2 -ffile-prefix-map=/data/builder/debuild/nginx-1.25.3/debian/debuild-base/nginx-1.25.3=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' 
--with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie'

```

#### 啟動、停止和重新載入組態

命令：`nginx -s ${SIGNAL}`。

* `stop`：快速關機。
* `quit`：正常關機。
* `reload`：重新載入設定檔。
* `reopen`：重新開啟記錄檔。

#### 查詢設定檔路徑

nginx 的設定檔名為 `nginx.conf`，會依據安裝方式導致被放置的路徑不同，可以透過 `nginx -t` 來查詢。&#x20;

```bash
nginx -t
# nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
# 表示設定檔路徑為 /usr/local/etc/nginx/nginx.conf
```

### main全域設定檔

* Nginx 的主要設定檔通常會放置在 `/etc/nginx/nginx.conf`。
* 另外在 `/etc/nginx/conf.d/*.conf` 則會放置不同域名的設定檔。然後在主設定檔中的 http context 加入一行 `include /etc/nginx/conf.d/*.conf;`即可將不同域名的設定引入，達成方便管理與修改不同域名設定的特性。

```nginx
user  nginx;
# 預設為process，不是thread
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    use epoll;
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

### user與worker\_processes設定

預先產生的行程數量，一般設置為auto即可，可使用`ps -ef | grep nginx`指令查看。

可看到除了pid為1的行程的使用者為root之外，其它行程的使用者均為nginx。

容器中可能沒有ps指令，可安裝`apt-get update && apt-get install -y procps`。

```bash
root@6ea974697f67:~# ps -ef|grep nginx
root         1     0  0 Jun25 ?        00:00:00 nginx: master process nginx -g daemon off;
nginx       68     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       69     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       70     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       71     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       72     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       73     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       74     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       75     1  0 02:46 ?        00:00:00 nginx: worker process
nginx       76     1  0 02:46 ?        00:00:00 nginx: worker process
```

### 主要上下文(main context)內容

設定檔是由一連串的指令(directive)所組成的。 指令針對特定的部分作設定，分為兩種：簡單指令(<mark style="background-color:green;">simple directive)</mark> 及 區塊指令(<mark style="background-color:green;">block directive)</mark>。

#### 常用簡單指令

* [user](https://nginx.org/en/docs/ngx\_core\_module.html#user)是指定行程的使用者。
* [worker\_processes](https://nginx.org/en/docs/ngx\_core\_module.html#worker\_processes)是工作行程的數量，可用auto設定即可。
* [error\_log](https://nginx.org/en/docs/ngx\_core\_module.html#error\_log)是記錄所有的錯誤，等級為debug, info, notice, warn, error, crit, alert, 或emerg。
* [pid](https://nginx.org/en/docs/ngx\_core\_module.html#pid)設定工作行程ID(PID)記錄檔存放的位置。

常用的全域簡單為以上部份，其它部份就是event與http區塊的設定。

因此設定檔中顯眼的 http、server 及 location 都是區塊指令。它們有著從屬關係。而最頂層的區塊指令只會有兩種：<mark style="color:red;">http 及 event，稱之為主要(全域)上下文(main context)</mark>。

```nginx
events {
     worker_connections 1024;
     use epoll;
}
http {
    # server 一定在 http 裡面
    server {
    # location 一定在 server 裡面
        location {}
    }
}
```

### event區塊

event區塊指定了影響連線處理的指令。

Linux下預設(kernel >=2.6)使用[epoll](https://nginx.org/en/docs/events.html)方式連線。

一般只會設定[worker\_connections](https://nginx.org/en/docs/ngx\_core\_module.html#worker\_connections)。設定工作行程可以連線的最大同時連線數。此連線數包括所有連線（例如與代理伺服器的連線等），而不僅僅是與客戶端的連線。另一個注意事項是，實際同時連線數不能超過當前開啟檔的最大數量限制，該限制可以通過worker\_rlimit\_nofile進行更改。



## Server block

當設定檔中有多個 `server {...}` 區塊，nginx選擇區塊與其中的listen指令有關。

* 預設的 listen 通常是 `listen 80 default_server`;&#x20;
*   但是一個完整的描述方法是: `listen IP:PORT;`而缺少的部分(不管是缺少IP還是Port號)Nginx會自動使用預設值補齊。&#x20;

    * 缺少IP 如 `listen 80`;，Nginx會自動補0.0.0.0，變成 : `listen 0.0.0.0:80`;
    * 缺少Port 如 `listen 10.1.1.1`;，Nginx會自動補80 Port號，變成 : `listen 10.1.1.1:80`;
    * 兩個都缺 也就是沒有使用 listen ，nginx會自動補上 `listen 0.0.0.0:80`;



首先 Nginx會先檢查 IP:Port 的匹配。 選擇順序為 listen 有指定IP (如 10.1.1.1) listen 無指定或使用0.0.0.0。第二 比對 server\_name 當第一個IP:Port匹配檢查完後，發現有多個符合的結果，才會繼續比對。

## Location block

在nginx內，`$host_name`沒有帶Port號，`$server_name`有帶Port號。

* <mark style="color:blue;">最普通的寫法(none)</mark>：location後面沒有任何特殊符號，如 `location /var/www {...}`。
* <mark style="color:blue;">必須完全一致</mark>：location後面帶`=`符號，如 `location = /var/www {...}。`
* <mark style="color:blue;">排除正規式的匹配，優先權高於普通的寫法</mark>：location後面帶 `^~` 符號，如 `location ^~ /var/www {...}。`&#x20;
* <mark style="color:blue;">正規式的匹配</mark>：location後面帶 \~ 或\~\* 符號，如 `location ~ /var/www {...}`。

選擇機制：

1. 先搜尋一次全部的none location。如 `location /var/www`。
2. location 後面使用 = 符號，並且精確匹配到，則使用並停止。如 location = /var/www。
3. 匹配none locaiotn ，並且匹配到最長符合的項目。
   1. 這個 "最長符合的項目" 如果是^，則使用並停止。如`location ^ /var/www`。
   2. 把這個 "最長符合的項目" 儲存起來。如 : `location /var` 和 `location /var/www`，`location /var/www` 會被儲存。
4. 尋找全部的正規表示法，第一個被匹配到的，則使用並停止。因此正規表示法的前後順序很重要。
5. 全部的正規表示法都找不到，使用 剛剛儲存的"最長符合的項目" 的none location。 如`location /var/www`。

### location 範例說明

```nginx
location / {...}
location /image {...}
location = /image/server1/png {...}
location ^~ /image/server1 {...}
location ~* ^/image {...}
location ~* ^/image/server[1-3] {...}
```

Client 送一個請求為，`http://abc.com/image/server1/png/logo.png`，這個請求的URI為 : `/image/server1/png/logo.png`。

依照機制 :

1. 找 = 符號的 location，`location = /image/server1/png {...}`，但是這個沒有精確匹配。
2. 找最長符合，找到 `location ^~ /image/server1 {...}` ，因為是用 ^\~ 符號，因此使用這個locaiotn。

Client 送一個請求為，http://abc.com/image/server2/png/logo.png，這個請求的URI為 : `/image/server2/png/logo.png`。

依照機制 :

1. 找 = 符號的 location，`location = /image/server1/png {...}`，但是這個沒有精確匹配。
2. 找最長符合，找到 `location /image {...}` ，因為沒有符號，因此先儲存。
3. 尋找正規表示的location，第一個匹配成功的是，`location ~* ^/image {...}`，因此使用這個locaiotn。

Client 送一個請求為，`http://abc.com/www/index.html`，這個請求的URI為`/`:clap::clap:`ww/index.html`

依照機制 :

1. 找 = 符號的 location，location = /image/server1/png {...}，但是這個沒有精確匹配到
2. 找最長符合，找到 location / {...} ，因為沒有符號，因此先儲存。
3. 尋找正規表示的location，但是找不到匹配的。
4. 使用原本的 "最長符合" 的location，location / {...}。

## 內部重新導向

所謂 "內部重新導向" 就是原本在某一個 location 內處理，因為設定的語法會跑到其它 location 或再一次進到相同的 location 進行處理。

會發生內部重新導向的狀況主要是有底下幾個語法 : (執行的優先順序也會照這個順序進行)

* rewrite
* try\_file
* index
* error\_page

### `rewrite`

舉個例子 有一個請求是 /example/default 且設定檔如下

```nginx
root /var/www/html;
location / {
    rewrite ^/example/(.*)$ /$1 last;
}

location /default {
    index index.html;
}
```

請求會先進到 "location /" 裡面，因為有使用 rewrite 語法，經過rewrite修改後的請求會變成 "/default"。 並且 rewrite 後面帶的參數是 last，因此Nginx會產生內部重新導向，跳到 "location /default"，繼續進行請求的處理。

### try\_file

try\_file可以讓Nginx進行一系列的檔案或資料夾的查詢，並可在最後放置一個URI讓Nginx進行內部重新導向。<mark style="color:blue;">當try\_file和rewrite寫在同一個location時，會優先執行rewrite</mark>。

如 : `try_file $uri $uri/ /default/error.html;`。

```nginx
root /var/www/html;
location / {
    try_file $uri $uri/ /default/error.html;
}

location /default {
    index index.html;
}
```

舉個例子 有一個請求是 `/example/default`。

請求會先進到 "location /" 裡面，並且依序尋找 :&#x20;

* `"/var/www/html/example/default"` 這個檔案。
* `"/var/www/html/example/default/"` 這個資料夾。
* 當上面兩個都找不到時，重新導向到 `/default/error.html`。

### index

一般 index 是用做當請求的最後一個文字是 "/" 符號時，進行一系列檔案的查詢。 如 "/example/default/" 或 最基本的 "/"。

index 也可以包含URI在裡面，如 index index.html /default/index.html，當找不到index.html，就會被內部重新導向到/default。

```nginx
root /var/www/html;
index index.html /default/index.htm;
location / {
    access_log /var/log/nginx/access.log;
}

location /default {
    access_log /var/log/default.log;
}
```

有一個請求是 /example/default/。

請求會先進到 "location /" 裡面，並且依序尋找:

* &#x20;"/var/www/html/example/default/index.html" 這個檔案。
* &#x20;"/var/www/html/default/index.htm" 這個檔案。

### error\_page

這個功能是定義當出現錯誤代碼時，要導到那個頁面。!!注意 : 404 錯誤回傳的網頁大小不能大於512K。

```nginx
root /var/www/html;
index index.html /default/index.htm;
location / {
    error_page 404 /fail/page.html;
}

location /fail {
    access_log /var/log/404.log;
}
```

有一個請求是 /example/default。請求會先進到 "location /" 裡面，並且尋找 : "/var/www/html/example/default" 這個檔案。當找不到時，Nginx會產生404錯誤碼，這時會觸發 error\_page 404。 被重新導向到 "/fail/page.html"。

## 設定 Let's Encrypt HTTPS nginx certbot SSL

條件：

* 有 sudo 的權限，並安裝 nginx。
* 擁有該網域。 設定好正確的 DNS record，比如你要幫 www.synology.me加上 https，就必須先把 DNS 設定到對應的 IP，因為 Let's Encrypt 在發憑證前會先來 challenge 這個網域。
* Let’s Encrypt 的免費憑證（有效期限 90 天)。

certbot 在申請完憑證之後，會自動幫你修改 nginx 的設定，前提是你的 nginx 原本就要有該網域的相關設定。certbot 會去找 nginx 設定中 server block 裡 server\_name directive 相符的設定去作修改。

```nginx
server {
# server_name 和產生的憑證網域相符，certbot 會把設定加在這個 block 裡
server_name www.synology.me
}
```

* 產生憑證：sudo certbot --nginx -d www.synology.me
*   如果這是你第一次執行 certbot 的話，它會請你同意使用者條款和輸入 email 位址，方便寄信聯絡。

    接著會讓你選需不需要作 https 轉址的設定。

## Nginx Reverse Proxy

反向代理 (Reverse Proxy): 網域往往只能連到一台入口主機，但當我們後端有很多網站及服務分配到多台主機時，這時候就需要透過路徑上的代理來轉發還有配置附載平衡。

* `least_conn` 選擇最少連線數，連線進來時會把 Request 導向連線數較少的 Server。
* least\_time 回應時間&#x20;
* weight 倍數

```nginx
upstream mycluster {
    server srv1.example.com weight=3;
    server srv2.example.com;
    server srv3.example.com;
    least_conn;
}

server {
    listen 80;
    server_name SERVER_IP;
    root /home/myhome;
    
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  
    location / {
            proxy_pass http://mycluster/;
      }
}
```

### proxy\_pass 導流到根目錄

```nginx
location /app/ {
    proxy_pass      http://192.168.1.100/;
}

test.com/app/xxxxx =>  http://192.168.1.100/xxxxx
```

### proxy\_pass 導流到特定目錄

```nginx
location /app/ {
    proxy_pass       http://192.168.1.100/maped_dir/;
}

test.com/app/xxxxx =>   http://192.168.1.100/maped_dir/xxxxx
```

## gzip

gzip 其實就是在做壓縮（compression），減少 payload 或者檔案的大小以增進傳輸的效率。壓縮的任務其實可以交給 Nginx Web Server 去完成。

```nginx
server {
...
    gzip on;
    gzip_types text/plain application/xml application/json;
    gzip_comp_level 9;
    gzip_min_length 1000;
...
}
```

## Nginx 效能優化設定

主要的配置檔位置在 Linux 環境中會在 /etc/nginx 底下，除了 epoll 可以從機制上影響效能外，也可以從系統服務的性質從核心的設定以及相關設定優化效能。

* `worker_processes` 就是配合主機核心數，若是 4 核心就可以設置 `worker_processes 4`。
* `worker_rlimit_nofile` 是可以開啟的檔案數量。
* `client_max_body_size` 限制檔案上傳大小。

connection

* `keepalive_requests`: 預設值 100 可以邊壓力測試邊調整。
* `keepalive_timeout`: 多久要切掉連線。

## Nginx Epoll

```nginx
events {
	use epoll;
	worker_connections 1024;
	multi_accept on;
}
```

## Linux 系統核心設定

編輯檔案 `/etc/sysctl.conf`。

* `sys.fs.file-max` 最大開檔上限， sysctl -w fs.file-max=50000 (可以暫時測試重開機後會消失)。
* `net.core.somaxconn`: 能被 nginx queue 接受的最大連線數，可以設定成 512，超過還需要設定 listen 的 backlog 參數，因為除了 FreeBSD, DragonFly BSD, macOS 其他預設值是 511。
* `net.core.netdev_max_backlog`: 網路卡的 backlog，加大會增加效能，但不瞭解網路卡的極限就容易出現錯誤。
* nofile 也跟開檔數有關，在 `/etc/security/limits.conf`設定。

## Nginx 監控報表

* 透過 Elastic 提供的 Filebeat 和 Metricbeat 可以從狀態、日誌即時的去監控 Nginx。
* 使用開源的 GoAccess 加上系統排程定時更新資料。
  * 能夠分析 Log 並且產生報表的靜態網頁，不過由於是靜態網頁，所以使用上就需要透過設定作業系統排程來定時跑腳本更新頁面。

## Nginx GIXY

Gixy 是一個分析 Nginx 配置的自動化缺陷檢測工具，主要目標是避免錯誤和配置導致資安漏洞。有一個練習用的 Repo[ vulnerable-nginx](https://github.com/detectify/vulnerable-nginx) 可以拿來測試常見的問題。

## 參考資料

* [nginx offical site](https://www.nginx.com/)
* [\[docker\] nginx offical image](https://hub.docker.com/\_/nginx)

SSL

* [How To Secure Nginx with Let's Encrypt on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)
