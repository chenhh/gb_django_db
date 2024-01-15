# CSS

## 基本語法

`selector {property1: value; property2: value; ...}`

* selector：選擇器，指樣式表要套用的元素。
* {}稱為宣告區塊(declaration block)：每組屬性(property)與值(value)之間以冒號:隔開。多個屬性之間以分號;隔開。
* 註解以`/* comment */`區隔。

## 選擇器可共用宣告區塊

```css
h1, h2, h3 {color:blue; text-align:center;}
```

## 顏色設定

1. 直接用顏色的英文名稱，如red, green, blue等設定。
2. 使用`rgb(0~255, 0~255, 0~255)`設定顏色。
3. 使用16進位 `#ff0000, #00ff00, #0000ff`等設定顏色。
4. 使用`rgba(0~255, 0~255, 0~255, 0~1)`，最後一個參數是透明度(alpha)，0為透明，1.0為不透明。
5. `hsl(0~360, 0%~100%, 0%~100%)`，為色調(hue)、飽和度(saturation)、亮度(lightness)。
6. `hsla(0~360, 0%~100%, 0%~100%, 0~1)`，同上，最後一個參數為透明度。

### 網路安全色

RGB的顏色有256\*256\*256=2\*\*24種顏色，而早期的電腦只支援256色，因此有256種保證所有營幕都能顯示正確顏色的色碼。

## 使用CSS的方法

#### 行內樣式(inline style)

將樣式視為元素的屬性，直接設定屬性值。建議少用，因為難以將內容與樣式分開。

```css
<h1 style="color: blue; text-align:center;"> title </h1>
```

#### 內部樣式表(interal style sheet)

將樣式表寫在head的style區塊中，使用時以元素名稱、id、class等方式套用。

#### 外部樣式表(external style sheet)

副檔名稱.css，內容和內部樣式表相同，使用時在head區塊加入：

```html
<link rel="stylesheet" href="mystyle.css">
```

也可以使用@import方式(避免使用)將樣式表讀入，在head區塊中加入：

```html
<style>
  @import url(mystyle.css);
</style>
```

* @import是 CSS 提供的語法規則，只有匯入樣式表的作用；link是HTML提供的標籤，不僅可以載入 CSS 檔案，還可以定義 RSS、rel等屬性 。
* 載入頁面時，link標籤引入的 CSS 被同時載入；而@import引入的 CSS 會等到頁面全部被下載完再被載入。該規則必須在樣式表頭部最先宣告。並且其後的分號是必需的，如果省略了此分號，外部樣式表將無法正確匯入，並會生成錯誤資訊。
* @import是 CSS2.1提出的語法，故只可在 IE5+ 才能識別；link標籤作為 HTML 元素，不存在相容性問題。
* 可以通過 JS 操作 DOM ，插入link標籤來改變樣式；由於 DOM 方法是基於檔案的，無法使用@import的方式插入樣式。

## 選擇器

### class選擇器

class可套用至多個元素，而一個元素也可以有多個class。

```html
<!DOCTYPE html>
<html lang="zh-tw">
<head>
<style>
  .myclass {color:red;}
  .myclass2{text-align: center;}
</style>
</head>

<body>
<p class="myclass myclass2"> content </p>
</body>
</html>
```

### id選擇器

元素的id必須唯一。

```html
<!DOCTYPE html>
<html lang="zh-tw">
<head>
<style>
  #myid {color:red;}
</style>
</head>

<body>
<p id="myid"> content </p>
</body>
</html>
```

### 屬性選擇器

將具有\[屬性]的所有元素均套用指定的樣式。

```html
<style>
  /* 將具有class屬性的所有元素背景設為藍色 */
  [class] {background: blue;}
</style>
```

也可限定特定屬性才套用模式。

```html
<style>
  /* 將class=location屬性的所有元素背景設為藍色 */
  [class="location"] {background: blue;}
</style>
```

### 全域選擇器

套用在所有元素上的樣式。

```html
<style>
  * {background: blue;}
</style>
```

### 虛擬選擇器

一般的選擇器是套用在html的元素或屬性上，虛擬類別選擇器將樣式套用在目前的狀態。

#### 超連結的虛擬類別

* `a:link`：超連結尚未被點選時的樣式。
* `a:visited`：超連結被點選後的樣式。

#### 動作虛擬類別

* `{element}:hover`：當滑鼠位於指定元素上時的樣式。
* `{element}:active`：點選元素時的樣式。
* `{element}:focus`：獲得元素焦點時的樣式。

#### 目標虛擬類別

* `:target`：可為移動的目標選定樣式。

## 複合選擇器

### 鄰接同層選擇器(Adjacent sibling combinator)

```css
/* Paragraphs that come immediately after any image */
img + p {
  font-weight: bold;
}

li:first-of-type + li {
  color: red;
}
```

### 通用同層選擇器(General sibling combinator)

```css
/* Paragraphs that are siblings of and subsequent to any image 
   只要在同一層中即可，不一定要立即在後面
*/
img ~ p {
  color: red;
}

p ~ span {
  color: red;
}
```

#### 直屬選擇器(Child combinator)

```css
/* List items that are immediate children of the "my-things" list */
ul.my-things > li {
  margin: 2em;
}
/* span為div的第一個子代時套用 */
div > span {
  background-color: yellow;
}
```

#### 後代選擇器

␣ 組合符號 (代表空白, 或更精準地說，代表一或多個空白字元) 結合了兩種選擇器，選擇了只有當第二個選擇器的目標為第一個選擇器目標的後裔時的元素，後代選擇器跟直屬選擇器相似，但是不要求披對的元素要是嚴格是父子關係。

```css
div span { background-color: DodgerBlue; }
```
