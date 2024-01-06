---
description: 4.0 version
---

# Django

## 建立新的專案

```bash
django-admin startproject myproject
```

![Django新專案的結構](.gitbook/assets/django\_arch-min.PNG)

* `manage.py`: 管理 Django 專案的命令列工具，內有許多子命令。
* `myproject/settings.py`是整個專案的設定檔。
* `myproject/wsgi.py`是wsgi的設定檔，使用預設內容即可。
* `myproject/asgi.py`是asgi的設定檔，也是使用預設內容即可。
* `myproject/urls.py`是專案的root url dispatch設定檔。

## mange.py的子命令

* inspectdb&#x20;
* loaddata&#x20;
* makemessages&#x20;
* makemigrations&#x20;
* migrate&#x20;
* sendtestemail&#x20;
* shell&#x20;
* showmigrations&#x20;
* sqlflush&#x20;
* sqlmigrate&#x20;
* sqlsequencereset&#x20;
* squashmigrations&#x20;
* startapp：建立新的應用程式，app的資料夾建議移至project中。
* startproject ：建立新的專案。
* test&#x20;
* testserver

\[sessions]&#x20;

* clearsessions

\[staticfiles]&#x20;

* collectstatic&#x20;
* findstatic&#x20;
* runserver

## 參考資料

* [Django official site](https://www.djangoproject.com/)
* [\[Django\] 文檔(4.0)](https://docs.djangoproject.com/zh-hans/4.0/)
* [\[MDN\] Django 介紹](https://developer.mozilla.org/zh-TW/docs/Learn/Server-side/Django/Introduction)

