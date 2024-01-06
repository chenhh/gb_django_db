# 靜態檔案

## template中\{%load static %\}標籤的用途為何？

答：需要有\{% load static %\} 這一行，才能在\<head> 和\<body> 之類的元素中參考靜態檔案。 如果沒有，則在應用程式執行時會出現例外狀況。

