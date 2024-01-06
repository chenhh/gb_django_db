# html

## 全域屬性

指所有元素都可以使用的屬性。

#### accesskey

設定按鍵組合，執行將焦點指向此表單物件，如下使用時為`alt + e`。

* `<input type="reset" value="取消" accesskey="e">`

#### class

指定元素的類別。多個元素可使用相同的類別。

#### contenteditable

設定元素是否可編輯，其值為true/false。

#### contextmenu(firefox only)

元素上按下右鍵時，出現快顯功能表。

#### dir

設定文字方向，預設為左至右(ltr)，也可設定為右至左(rtl)。

#### draggable

設定元素是否可被拖曳移動，可選true/false/auto(由browser決定)。

#### hidden

隱藏元素(不佔空間)，值為true/false，一般由javascript控制。

#### id

設定元素的id，必須唯一。

#### lang

設定元素的語系。

#### spellcheck

對元素做語法檢查，其值為true/false，可對input, textarea, 與可編輯的文字元素內容檢查。

#### style

套件到文字的css樣式表。

#### tabindex

可設定按鍵盤的tab鍵時，焦點的移動順序，其值為整數。

#### title

元素的額外訊息，如果滑鼠移到此處時，會出現訊息。

## 事件屬性(event attributes)

可針對元素的特定事件，以script控制行為。

### 視窗事件(window event attribute)

主要是用在body元素。

* onafterprint：文件列印後所觸發的事件。
* onbeforeprint：文件列印前所觸發的事件。
* onbeforeunload：文件缷載前所觸發的事件。
* onerror：
