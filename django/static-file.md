# 靜態檔案

## settings.py中的設定

### STATIC\_URL

`STATIC_URL = '/static/'`

網址使用位於 STATIC\_ROOT 中的靜態檔案時要使用的 URL，在template中，帶入`{% load static %`}後會自動轉換。



## template中\{%load static %\}標籤的用途為何？

答：需要有\{% load static %\} 這一行，才能在\<head> 和\<body> 之類的元素中參考靜態檔案。 如果沒有，則在應用程式執行時會出現例外狀況。

