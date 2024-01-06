# 模型(model)

Django框架中的Model(資料模型)，其實就是利用ORM(Object Relational Mapping)的程式設計技術，將資料庫、資料表及欄位等，抽象化為程式設計中的類別(Class)和屬性(Attribute)，除了降低Django專案對於資料庫的相依性外，在開發的過程中，也能夠輕鬆的利用物件來操作資料。

## 模型定義

模型通常在 app 中的 `models.py` 檔案中定義。它們是繼承自 `django.db.models.Model`的子類， 可以包括屬性，方法和描述性資料(metadata)。

```python
from django.db import models

class MyModelName(models.Model):
    """A typical class defining a model, derived from the Model class."""

    # Fields
    my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')
    ...

    # Metadata
    class Meta:
        ordering = ['-my_field_name']

    # Methods
    def get_absolute_url(self):
         """Returns the url to access a particular instance of MyModelName."""
         return reverse('model-detail-view', args=[str(self.id)])

    def __str__(self):
        """String for representing the MyModelName object (in Admin site etc.)."""
        return self.field_namepy
```

## 欄位

模型可以有任意數量的欄位、任何類型的欄位 — 每個欄位都表示我們要存放在我們的一個資料庫中的一欄數據(a column of data)。每筆資料庫記錄（列 row）將由每個欄位值之一組成。

在上面例子中，有個叫 `my_field_name` 的單一欄位，其類型為 `models.CharField` — 這意味著這個欄位將會包含字母、數字字串。欄位類型還可以獲取參數，進一步指定欄位如何存放或如何被使用。

欄位名稱用於在視圖和模版中引用它。欄位還有一個標籤，它被指定一個參數（verbose\_name），或者通過大寫字段的變量名的第一個字母，並用空格替換下劃線（例如my\_field\_name 的預設標籤為 My field name ）。

### 資料欄位型別

* [AutoField](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#autofield):一個 IntegerField，根據可用的 ID 自動遞增。你通常不需要直接使用它；如果你沒有指定，主鍵欄位會自動新增到你的模型中
* [BigAutoField](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/#bigautofield):一個 64 位整數，與 AutoField 很相似，但保證適合 1 到2\*\*64 的數字。
* SmallAutoField:  就像一個 AutoField，但只允許值在一定（依賴於資料庫）的限制下。1 到 32767 的值在 Django 支援的所有資料庫中都是安全的。



* BigIntegerField: 一個 64 位的整數，和 IntegerField 很像，只是它保證適合從-2**64到2\*\***64-1的數字。
* BinaryField:一個用於儲存原始二進位制資料的欄位。可以指定為 bytes、bytearray 或 memoryview。(這個欄位 不能 代替正確的靜態檔案處理。)
* BooleanField:一個 true／false 欄位。
* CharField:一個字串欄位，適用於小到大的字串。對於大量的文字，應使用 TextField。有些後端資料庫對 max\_length 有限制。
* TextField: 一個大的文字欄位。該欄位的預設表單部件是一個 Textarea。
* DateField: 一個日期，在 Python 中用一個 datetime.date 例項表示。有一些額外的、可選的引數。
* DateTimeField: 一個日期和時間，在 Python 中用一個 datetime.datetime 例項表示。與 DateField 一樣，使用相同的額外引數。
* TimeField: 一個時間，在 Python 中用 datetime.time 例項表示。接受與 DateField 相同的自動填充選項。
* DecimalField: 一個固定精度的十進位制數，在 Python 中用一個 Decimal 例項來表示。它使用 DecimalValidator 驗證輸入。
* DurationField: 一個用於儲存時間段的欄位——在 Python 中用 timedelta 建模。當在 PostgreSQL 上使用時，使用的資料型別是 interval。
* EmailField: 一個 CharField，使用 EmailValidator 來檢查該值是否為有效的電子郵件位址。
* FileField: 一個檔案上傳欄位。
* FilePathField: 一個 CharField，其選擇僅限於檔案系統中某個目錄下的檔名。有一些特殊的引數，其中第一個引數是 必須的。
* FloatField: 在 Python 中用一個 float 例項表示的浮點數。FloatField 內部使用 Python 的 float 型別，而 DecimalField 則使用 Python 的 Decimal 型別。
* GenericIPAddressField: IPv4 或 IPv6 位址，字串格式（如 192.0.2.30 或 2a02:42fe::4 ）。該欄位的預設表單部件是一個 TextInput。
* ImageField: 繼承 FileField 的所有屬性和方法，但也驗證上傳的物件是有效的影象。ImageField 也有 height 和 width 屬性。
* IntegerField: 一個整數。從 -2147483648 到 2147483647 的值在 Django 支援的所有資料庫中都是安全的。
* JSONField: 一個用於儲存 JSON 編碼資料的欄位。在 Python 中，資料以其 Python 本地格式表示：字典、列表、字串、數字、布林值和 None。
* PositiveBigIntegerField: 就像一個 PositiveIntegerField，但只允許在某一特定點下的值（依賴於資料庫）。0 到 9223372036854775807 的值在 Django 支援的所有資料庫中都是安全的。
* PositiveIntegerField: 就像 IntegerField 一樣，但必須是正值或零（ 0 ）。從 0 到 2147483647 的值在 Django 支援的所有資料庫中都是安全的。出於向後相容的原因，接受 0 的值。
* PositiveSmallIntegerField: 就像一個 PositiveIntegerField，但只允許在某一特定（資料庫依賴的）點下取值。0 到 32767 的值在 Django 支援的所有資料庫中都是安全的。
* SmallIntegerField: 就像一個 IntegerField，但只允許在某一特定（依賴於資料庫的）點下取值。從 -32768 到 32767 的值在 Django 支援的所有資料庫中都是安全的。
* SlugField: Slug 是一個報紙術語。slug 是一個簡短的標簽，只包含字母、數字、下劃線或連字元。它們一般用於 URL 中。
* URLField: URL 的 CharField，由 URLValidator 驗證。
* UUIDField: 一個用於儲存通用唯一識別符號的欄位。使用 Python 的 UUID 類。當在 PostgreSQL 上使用時，它儲存在一個 uuid 的資料型別中，否則儲存在一個 char(32) 中。

### 關系欄位

* ForeignKey: 一個多對一的關系。需要兩個位置引數：模型相關的類和 on\_delete 選項。
  * 在 ForeignKey 上會自動建立一個資料庫索引。你可以通過設定 db\_index 為 False 來禁用它。
  * 在幕後，Django 在欄位名後附加 "\_id" 來建立資料庫列名。
* ManyToManyField: 一個多對多的關系。需要一個位置引數：模型相關的類，它的工作原理與 ForeignKey 完全相同，包括 遞迴 和 惰性 關系。
  * 在幕後，Django 建立了一個中間連線表來表示多對多的關系。預設情況下，這個表名是使用多對多欄位的名稱和包含它的模型的表名生成的。由於有些資料庫不支援超過一定長度的表名，這些表名將被自動截斷，並使用唯一性雜湊
* OneToOneField: 一對一的關系。概念上，這類似於 ForeignKey 與 unique=True，但關系的“反向”將直接返回一個單一物件。
  * 最有用的是作為某種方式“擴充套件”另一個模型的主鍵；
  * 如果沒有為 OneToOneField 指定 related\_name 引數，Django 將使用當前模型的小寫名作為預設值。

### 元數據(Metadata)

此元數據最有用的功能之一是控制在查詢模型類型時返回之記錄的默認排序。你可以透過在`ordering` 屬性的欄位名稱列表中指定匹配順序來執行此操作，如上所示。

另一個常見的屬性是 verbose\_name ,一個 verbose\_name 說明單數和複數形式的類別。

其他有用的屬性允許你為模型創建和應用新的“訪問權限”（預設權限會被自動套用），允許基於其他的欄位排序，或聲明該類是”抽象的“（你無法創建的記錄基類，並將由其他型號派生）。

許多其他元數據選項控制模型中必須使用哪些數據庫以及數據的存儲方式。（如果你需要模型對映一個現有數據庫，這會有用）。

## 方法(Methods)

一個模型也可以有方法。

最起碼，在每個模型中，你應該定義標準的Python 類方法`__str__()` ，來為每個物件返回一個人類可讀的字串。此字元用於表示管理站點的各個記錄（以及你需要引用模型實例的任何其他位置）。通常這將返回模型中的標題或名稱欄位。

Django 方法中另一個常用方法是 `get_absolute_url()` ，這函數返回一個在網站上顯示個人模型記錄的 URL（如果你定義了該方法，那麼 Django 將自動在“管理站點”中新增“在站點中檢視“按鈕在模型的記錄編輯欄）。

### \_\__str\_\__與 unicode\_\_方法

[\[官網資料\]](https://docs.djangoproject.com/zh-hans/4.0/ref/models/instances/#other-model-instance-methods)

每當你對一個物件實例呼叫 str() 時，就會呼叫 **str**() 方法。Django 在很多地方使用了 str(obj) 方法。最主要的是，在 Django 管理站點中顯示一個物件，以及作為模板顯示對象時插入的值。因此，你應該總是從 **str**() 方法中返回一個漂亮的、人類可讀的模型表示。

在Django中，如果用的是Python3的話就只能用\_\_str\_\_方法，如果是Python2的話就使用\_\_unicode\_\_方法。因為更安全一些。

Django 模型有個預設的 **str**() 方法 會去呼叫 **unicode**() 並將結果轉換為 UTF-8 編碼的字串。這就意味著 unicode(p) 會返回一個 Unicode 字串，而 str(p) 會返回一個以 UTF-8 編碼的普通字串。

## 模型關聯性

一旦我們已經決定了我們的模型和欄位，我們需要考慮它們的關聯性。Django允許你來定義一對一的關聯（OneToOneField），一對多（ForeignKey）和多對多（ManyToManyField）。

## 創建和修改記錄

```python
# Create a new record using the model's constructor.
record = MyModelName(my_field_name="Instance #1")

# Save the object into the database.
record.save
```

如果沒有任何的欄位被宣告為主鍵，這筆新的紀錄會被自動的賦予一個主鍵並將主鍵欄命名為 id。上例的那筆資料被儲存後，試著查詢這筆紀錄會看到它被自動賦予 1 的編號。

```python
# Access model field values using Python attributes.
print(record.id) #should return 1 for the first record.
print(record.my_field_name) # should print 'Instance #1'

# Change record by modifying the fields, then calling save().
record.my_field_name = "New Instance Name"
record.save()
```

## 搜尋紀錄

你可以使用模型的 objects 屬性(由 base class 提供)搜尋符合某個條件的紀錄。

我們可以使用`objects.all()`取得一個模型的所有紀錄，回傳值為一個 QuerySet 。 QuerySet 是一個可迭代的物件，表示他含有多個物件，而我們可以藉由迭代/迴圈取得每個物件。

```python
all_books = Book.objects.all()
```

Django的 `filter()` 方法讓我們可以透過符合特定文字或數值的欄位篩選回傳的QuerySet。

```python
wild_books = Book.objects.filter(title__contains='wild')
number_wild_books = Book.objects.filter(title__contains='wild').count()
```



## 參考資料

* [\[MDN\] 模型入門](https://developer.mozilla.org/zh-TW/docs/Learn/Server-side/Django/Models)
* [\[django\] model field選項說明](https://docs.djangoproject.com/zh-hans/4.0/ref/models/fields/)
