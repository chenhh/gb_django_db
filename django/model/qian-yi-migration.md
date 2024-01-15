# 遷移(migration)

## 簡介

Migration(資料遷移)實際上是一個Python檔案，用來同步Django專案下models.py中的類別(Class)及資料庫。也就是說，<mark style="color:blue;">只要Django專案中的models.py有任何的變動，都要執行一次Migration(資料遷移)，來同步資料庫</mark>。

```bash
python manage.py makemigrations
```

其中所建立的Migration(資料遷移)檔案，存放在應用程式(APP)下的migrations資料夾中。

Migration(資料遷移)檔案中，定義了將要在資料庫執行的動作。<mark style="color:blue;">而接下來所要執行的migrate指令，則會依照此Migration(資料遷移)檔案，同步models.py中所建立的類別至資料庫中</mark>

```python
python manage.py migrate
```

從執行結果可以看到，posts應用程式(APP)的Migration(資料遷移)已成功執行。

如果想要知道執行Migration(資料遷移)時，背後所產生的SQL指令，則可以利用sqlmigrate指令，加上應用程式(APP)名稱及Migration(資料遷移)檔案的前方代碼：

```bash
python manage.py sqlmigrate posts 0001
```

## 還原作業

用manage.py把資料結構回到上個migration。會需要這樣做的原因是，Django會在資料庫中記錄現在執行到哪一步，他會把缺的部份自己補齊，所以我們要經由manage.py回覆到我們需要的版本，並且跟資料庫說要更換記錄點。

* `python + manage.py + migrate + {app_name} + {還原點}`
* `python manage.py migrate account 0007_customer_customer_order_no`

Database的資料結構回去後，再從備份資料的地方把資料回倒，確定資料都是齊全的。

## 參考資料

* [https://docs.djangoproject.com/zh-hans/4.0/topics/migrations/](https://docs.djangoproject.com/zh-hans/4.0/topics/migrations/)
