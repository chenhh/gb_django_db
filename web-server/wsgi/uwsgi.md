# uwsgi

## 簡介

Wsgi (Web Server Gateway Interface) 是同步通訊服務規範，它是 Python應用程式（application）或框架（如 Django）和 Web服務之間的一種介面，它是一種協議/規範，是在 PEP 333提出的，並在 PEP 3333 進行補充。

WSGI 介面有服務端和應用端兩部分，服務端也可以叫網關端，應用端也叫框架端。

* 服務端調用一個由應用端提供的可調用物件。如何提供這個物件，由服務端決定。例如某些服務器或者網關需要應用的部署者寫一段腳本，以創建服務器或者網關的實例，並且為這個實例提供一個應用實例。
* 另一些服務器或者網關則可能使用配置檔案或其他方法以指定應用實例應該從哪裡導入或獲取。

有兩種方式配置uWSGI伺服器：

* <mark style="color:blue;">uWSGI作為中間件</mark>，它用到了uwsgi協議(與nginx通訊)，wsgi協議(呼叫Flask或django)。當有客戶端發來請求，nginx先做處理(靜態資源是nginx的強項)，nginx無法處理的請求轉發給後端的uWSGI伺服器(反向代理)，處理完成後，最後的回應也是nginx回覆客戶端。
  * 反向代理的優點：提高web server效能(uWSGI處理靜態資源不如nginx，nginx會在收到一個完整的http請求後再轉發給wWSGI)。
  * nginx可以做負載平衡。
  * 保護了實際的web伺服器(客戶端是和nginx互動而不直接與uWSGI互動)。
* <mark style="color:blue;">uWSGI伺服器直接處理http請求與回覆</mark>。當有請求發來，uWSGI 接受請求，呼叫 flask或django app 得到回應，之後回應傳給客戶端。

## 參考資料

* [the uWSGI project](https://uwsgi-docs.readthedocs.io/en/latest/)
