# 管理頁面(admin)

## &#x20;建立管理員

`python manage.py createsuperuser`

輸入帳號、Email、密碼等資訊，就完成 superuser 的新增了

## 註冊 Model class

我們需要在讓 Django 知道，有哪些 Model 需要管理後台。

```python
from django.contrib import admin
from .models import (Exchange, Group, Symbol, Profile, Statement, Price)

models = (Exchange, Group, Symbol, Profile, Statement, Price)
for model in models:
    admin.site.register(model)
```

## 進入管理後台

連至 http://127.0.0.1:8000/admin，可以看到管理後台的登入頁面。
