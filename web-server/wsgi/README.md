# wsgi

## 簡介

WSGI，全稱 Web Server Gateway Interface，或者 Python Web Server Gateway Interface ，是為 Python 語言定義的 Web 伺服器和 Web 應用程式或框架之間的一種簡單而通用的介面，也就是閘道器。閘道器的作用就是在協議之間進行轉換。

很多框架都自帶了 WSGI server ，比如 Flask，webpy，Django、CherryPy等等。當然效能都不好，自帶的 web server 更多的是測試用途，釋出時則使用生產環境的 WSGI server或者是以 nginx 做 uwsgi的前端 。

WSGI就像是一座橋樑，一邊連著web伺服器，另一邊連著使用者的應用。但是呢，這個橋的功能很弱，有時候還需要別的橋來幫忙才能進行處理。

## WSGI的作用

WSGI有兩方：

* 服務方：“伺服器”/“閘道器”，
* 應用方：“應用程式”/“應用框架”
* 服務方呼叫應用方，提供環境資訊，以及一個回撥函式（提供給應用程式用來將訊息頭傳遞給伺服器方），並接收Web內容作為返回值。

所謂的 <mark style="color:red;">WSGI中介軟體同時實現了API的兩方，因此可以在WSGI服務和WSGI應用之間起調解作用</mark>：

* 從WSGI伺服器的角度來說，中介軟體扮演應用程式，
* 而從應用程式的角度來說，中介軟體扮演伺服器。

## wsgi server

可理解為一個符合wsgi規範的web server，接收request請求，封裝一系列環境變數，按照wsgi規範呼叫註冊的wsgi app，最後將response返回給客戶端。

### 工作流程

1. 伺服器建立socket，監聽port，等待client 連線。
2. 當request過來時，server解析client msg放到環境變數environ中，並呼叫繫結的handler來處理。
3. handler解析這個http request，將request訊息例如method、path等放到environ中
4. wsgi handler再將一些server端訊息也放到environ中，最後server msg，client msg，以及本次請求msg 全部都儲存到了環境變數envrion中；
5. wsgi handler呼叫註冊的wsgi app，並將envrion和回撥函式傳給wsgi app
6. wsgi app將reponse header/status/body回傳給wsgi handler
7. handler 通過socket將response msg返回到client

## WSGI Application

wsgi application就是一個普通的callable物件，當有請求到來時，wsgi server會呼叫這個wsgi app。這個物件接收兩個引數，通常為environ,start\_response。environ就像前面介紹的，可以理解為環境變數，

跟一次請求相關的所有資訊都儲存在了這個環境變數中，包括伺服器資訊，客戶端資訊，請求資訊。start\_response是一個callback函式，wsgi application通過呼叫start\_response，將response headers/status 返回給wsgi server。此外這個wsgi app會return 一個iterator物件 ，這個iterator就是response body。
