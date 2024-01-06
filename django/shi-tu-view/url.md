# url

## url路由基礎

* 在settings.py檔案中有一個root\_urlconf設定，設定的是在訪問**時通過哪一個url檔案去匹配所請求的。**
*

## 簡介

在 Django 2 以前， 網址的寫法都是用 `url()` 搭配 正規表示法(regular expression)。

```python
from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^articles/2003/$', views.special_case_2003),
    url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.article_detail),
]
```

想法相當直觀，今天欲查詢的頁面，會一個一個做配對，直到找到與自己配對的網址，在根據後方的views來做相對的回應。

而在 Django 2，url已經正式被打入冷宮了，而是由另外兩個函數來代替，分別是：&#x20;

* path
* re\_path：url函數還在，但內部其實是呼叫repath實作。

```python
#原本的寫法
url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),

#修改後的寫法
path('articles/<int:year>/', views.year_archiv),
path('articles/<int:year>/<int:month>', views.montu_archiv
```

[int:year](int:year) 及 [int:month](int:month) 前面所代表的就是資料型態，後方代表的是資料變數，而這些變數就是要傳給views做處理的。

也就是說，網址是 articles/XXXX 及 articles/XXXX/XX (XX裡填的 都要是 整數)，分別就會對應到year\_archive 及 month\_archive。

也就是說，我們在相同目錄下的views裡所定義的函式，會多帶這些參數：

```python
# 這裡多傳入 year
def year_archive(request, year):
    ...

# 這裡多傳入 year 及 month
def month_archive(request, year, month):
    ...
```

能夠傳入的參數型態當然不只有囉! 還有這一些型態可以使用

* str : 怎麼能缺少字串呢! 你說對吧! 而且當你沒有設定型態的話，預設就是用 str 。
* slug : 類似於 str，但不一樣的地方在於，slug還多包含了ASCII符號，例 : coding-is-so-fun 。
* uuid : 用來辨識用的資訊，為了就是避免發生碰撞囉。

## 參考資料

* [https://docs.djangoproject.com/zh-hans/4.0/topics/http/urls/](https://docs.djangoproject.com/zh-hans/4.0/topics/http/urls/)
