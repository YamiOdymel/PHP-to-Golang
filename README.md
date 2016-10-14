![](images/logo.png)

# 從 PHP 到 Golang 的筆記

* [定義變數－Variables](#定義變數variables)

* [定義變數－Variables](#定義變數variables)

* [多資料儲存型態－Stores](#多資料儲存型態stores)

* [迴圈－Foreach](#迴圈foreach)

* [日期－Date](#日期date)

* [切割字串－Split（Explode）](#切割字串splitexplode)

* [關聯陣列－Associative Array](#關聯陣列associative-array)

* [是否存在－Isset](#是否存在isset)

* [指針－Pointer](#指針pointer)

&nbsp;

## 我為什麼從 PHP 轉到 Golang？

PHP 和模塊之間的關係令人感到煩躁，假設你要讀取 `yaml` 檔案，你需要有一個 `yaml` 的模塊，為此，你還需要將其編譯然後將編譯後的模塊擺放至指定位置，之後換了一台伺服器你還要重新編譯，這點到現在還是沒有改善；順帶一提之後出了 PHP 7 效能確實提升了許多（比 Python 3 快了些），但 PHP 仍令我感到臃腫，我覺得是時候 <br>（轉行）了。

PHP 和 Golang 的效能我想毋庸置疑是後者比較快（而且是以倍數來算），也許有的人會認為兩種不應該被放在一起比較，但 Golang 本身就是偏向 Web 開發的，所以這也是為什麼我考慮轉用 Golang 的原因，起初我的考慮有幾個：Node.js 和 Rust 還有最終被選定的 Golang；先談談 Node.js 吧。

### Node.js

Node.js 的效能可以說是快上 PHP [3.5倍至6倍左右](http://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=php&lang2=node)，而且撰寫的語言還是 JavaScript，蒸蚌，如此一來就不需要學習新語言了！搭配 Babel 更可以說是萬能，但相較於 Golang 之下，Node.js 似乎就沒有那麼吸引我了。你確實可以用 Node.js 寫出很多東西，不過那 V8 引擎的效能仍然有限，而且要學習新的事物，不就應該是「全新」的嗎 ;)？

### Rust

在拋棄改用 Node.js 之後我曾經花了一天的時間嘗試 Rust 和 Iron 框架，嗯⋯⋯Rust 太強大了，強大到讓我覺得 Rust 不應該用在這裡，這想法也許很蠢，但 Rust 讓我覺得適合更應該拿來用在系統或者是部分底層的地方，而不應該是網路服務，順帶一提 Rust 快過於 Golang [1倍至1.5倍左右](http://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=rust&lang2=go)。

### Golang

Golang 是我最終的選擇，主要在於我花了一天的時間來研究的時候意外地發現 Golang 夭壽簡潔（[關鍵字只有25個](https://astaxie.gitbooks.io/build-web-application-with-golang/content/zh/02.8.html)），相較之下 Rust 太過於「強大」令我怯步；而且 Golang 帶有許多工具，例如 `go fmt` 會自動幫你整理程式碼、`go doc` 會自動幫你生產文件、`go test` 可以自動單元測試並生產覆蓋率報表、也有 `go get` 套件管理工具（雖然沒有版本功能），不過都很實用，而且也不需要加上分號（`;`），真要說不好的地方⋯⋯大概就是強迫你花括號不能換行放吧（沒錯，我就是花括號會換行放的人）。

&nbsp;

## 定義變數－Variables

你能夠在 PHP 裡面想建立一個變數的時候就直接建立，夭壽讚，是嗎？

```php
$a = "foo";
$b = "bar";
```

蒸蚌！那麼 Golang 呢？在 Golang 中變數分為幾類：「新定義」、「預先定義」、「自動新定義」、「覆蓋」。

讓我們來看看範例：

```go
// 新定義：定義新的 a 變數為字串型別，而且值是「foo」
var a string = "foo"

// 預先定義：先定義一個新的 b 變數為字串型別但是不賦予值
var b string

// 自動新定義：讓 Golang 依照值的內容自己定義新變數的資料型態
c := "bar"

// 覆蓋：先前已經定義過 a 了，所以可以像這樣直接覆蓋其值
a = "fooooooo"
```

&nbsp;

## 多資料儲存型態－Stores

主要是 PHP 的陣列能做太多事情了，所以在 PHP 裡面要儲存什麼用陣列就好了。

```php
$array  = [1, 2, 3, 4, 5];
$array2 = ['username' => 'YamiOdymel', 
           'password' => '2016 Spring'];
```

在 Golang 裡⋯⋯沒有這麼萬能的東西，首先要先了解 Golang 中有這些型態：`array`, `slice`, `map`, `interface`，

你他媽的我到底看了三洨，首先你要知道 Golang 是個強型別語言，意思是你的陣列中**只能有一種型態**，什麼意思？當你決定**這個陣列是用來擺放字串資料的時候，你就只能在裡面放字串**，沒有數值，沒有布林值，就像你沒有女朋友一樣。

### 陣列－Array

> 一個存放固定長度的陣列。

先撇開 PHP 的「萬能陣列」不管，Golang 中的陣列既**單純卻又十分腦殘**，在定義一個陣列的時候，你必須給他一個**長度**還有**其內容存放的資料型態**，你的陣列內容**不一定要填滿其長度**，但是你的**陣列內容不能超過你當初定義的長度**。

如果你在 PHP 中這麼寫：

```php
$a = ["foo", "bar"];

echo $a[0]; // 輸出：foo
```

那麼這就是你在 Golang 如何將其實作：

```go
var a [2]string

a[0] = "foo"
a[1] = "bar"

fmt.Println(a[0]) // 輸出：foo
```

### 切片－Slice

> 可供「裁切」而且供自由擴展的陣列。

切片⋯⋯這聽起來也許很奇怪，但是你確實可以「切」他，讓我們先談談「切片」比起「陣列」要好在哪裡：「**你不用定義其最大長度**，而且你可以**直接賦予值**」，沒了。

所以你在 PHP 中這麼寫（*靠腰 啊不是跟剛才一樣*）：

```php
$a = ["foo", "bar"];

echo $a[0]; // 輸出：foo
```

你會在 Golang 中這樣撰寫：

```go
a := []string{"foo", "bar"}

fmt.Println(a[0]) // 輸出：foo
```

我們剛才有提到你可以「切」他，記得嗎？這有點像是 PHP 中的 `array_slice()`，但是 Golang 直接讓 Slice「*內建*」了這個用法，其用法是：`slice[開始:結束]`。

```go
p := []int{1, 2, 3, 4, 5, 6}

fmt.Println(p[0:1]) // 輸出：[1]
fmt.Println(p[1:1]) // 輸出：[]  （！注意這跟 PHP 不一樣！）
fmt.Println(p[1:])  // 輸出：[2, 3, 4, 5, 6]
fmt.Println(p[:1])  // 輸出：[1]
```

在 PHP 中倒是沒有那麼方便，在下列 PHP 範例中你需要不斷地使用 `array_slice()`。

```php
$p = [1, 2, 3, 4, 5, 6];

echo array_slice($p, 0, 1); // 輸出：[1]
echo array_slice($p, 1, 1); // 輸出：[2]
echo array_slice($p, 1);    // 輸出：[2, 3, 4, 5, 6]
echo array_slice($p, 0, 1); // 輸出：[1]
```

### 映照－Map

> 有鍵名和鍵值的陣列。

你可以把「映照」看成是一個有鍵名和鍵值的陣列，但是記住：「**你需要事先定義其鍵名、鍵值的資料型態**」，這仍限制你**沒辦法**在映照中存放多種不同型態的資料。

讓我們先來個 PHP 範例：

```php
$data["username"] = "YamiOdymel";
$data["password"] = "2016 Spring";

echo $data["username"]; // 輸出：YamiOdymel
```

在 Golang 裡可就沒這麼簡單了，你需要先用 `make()` 宣告 `map`。

```go
data := make(map[string]string)

data["username"] = "YamiOdymel"
data["password"] = "2016 Spring"

fmt.Println(data["username"]) // 輸出：YamiOdymel
```

### 接口－Interface

> 終於；一個可存放多種資料型態的陣列，但難以捉模（幹）。

也許你不喜歡「接口」這個詞，但用「介面」我怕會誤導大眾，所以，是的，接下來我會繼續稱其為「接口」。

還記得你可以在 PHP 的關聯陣列裡面存放任何型態的資料嗎，像下面這樣？

```php
$mixedData  = ["foobar", 123456];
$mixedData2 = ['username' => 'YamiOdymel', 
               'time'     => 123456];
```

現在你有福了！正因為 Golang 中的 `interface{}` **可以接受任何內容**，所以你可以把它拿來**存放任何型態的資料**。

```go
mixedData := []interface{"foobar", 123456}

mixedData2 := make(map[string]interface{})
mixedData2["username"] = "YamiOdymel"
mixedData2["time"]     = 123456
```

&nbsp;

## 不定值－Mixed Type

有時候你也許會有個不定值的變數，在 PHP 裏你可以直接將一個變數定義成字串、數值、空值、就像你那變心的女友一樣隨時都在變。

```php
$mixed = 123;
echo $mixed; // 輸出：123

$mixed = "Moon, Dalan!";
echo $mixed; // 輸出：Moon, Dalan!

$mixed = ["A", "B", "C"];
echo $mixed; // 輸出：["A", "B", "C"]
```

在 Golang 中你必須給予變數一個指定的資料型別，不過還記得剛才提到的：「Golang 中有個 `interface{}` 能夠**存放任何事物**」嗎（*雖然也不是真的任何事物啦⋯⋯*）？

```go
var mixed interface{}

mixed = 123
fmt.Println(mixed) // 輸出：123

mixed = "Moon, Dalan!"
fmt.Println(mixed) // 輸出：Moon, Dalan!

mixed = []string{"A", "B", "C"}
fmt.Println(mixed) // 輸出：["A", "B", "C"]
```

&nbsp;

## 迴圈－Foreach

這個迴圈和 `for()` 不同的是 `foreach()` 能夠直接給你值和鍵名，在 PHP 中用起來十分簡單：

```php
$data = ['a', 'b', 'c'];

foreach($data as $index => $value)
    echo $index . $value . '|' ; // 輸出：0a|1b|2c|

foreach($data as $index => $value)
    echo $index . '|' ; // 輸出：0|1|2|
    
foreach($data as $value)
    echo $value . '|' ; // 輸出：a|b|c|
```

Golang 中僅有 `for` 一種迴圈但卻能夠達成 `foreach`, `while`, `for` 多種用法，

在這裡你可以利用 Golang 中的 `range` 達成和 PHP 一樣的 `foreach` 方式：

```go
data := []string{"a", "b", "c"}

for index, value := range data {
    fmt.Printf("%d%s|", index, value)  // 輸出：0a|1b|2c|
}

for index := range data {
    fmt.Printf("%d|", index)  // 輸出：0|1|2|
}

for _, value := range data {
    fmt.Printf("%s|", value)  // 輸出：a|b|c|
}
```

&nbsp;

## 日期－Date

在 PHP 中我們可以透過 `date()` 像這樣取得目前的日期：

```php
echo data("Y-m-d H:i:s"); // 輸出：2016-07-13 12:59:59
```

在 Golang 就稍微有趣點了，因為 Golang 會**自動偵測格式**，像這樣：

```go
fmt.Println(time.Now().Format("2006-2-1 03:04:00"))          // 輸出：2016-07-13 12:59:59
fmt.Println(time.Now().Format("Mon, Jan 2, 2006 at 3:04pm")) // 輸出： Mon, Jul 13, 2016 at 12:59pm
```

&nbsp;

## 切割字串－Split（Explode）

俗話說：「爆炸就是藝術」，我們可愛的 PHP 用詞真的很大膽，像是：`explode()`（爆炸）、`die()`（死掉），

回歸正傳，如果你想在 PHP 裡面將字串切割成陣列，你可以這麼做：

```php
$data  = "a, b, c, d";
$array = explode(", ", $data);
```

簡單的就讓一個字串給「爆炸」了，那麼 Golang 呢？

```go
data  := "a, b, c, d"
array := strings.Split(data, ", ")
```

對了，**記得引用 `strings` 套件**。

&nbsp;

## 關聯陣列－Associative Array

這真的是很常用到的功能，就像物件一樣有著鍵名和鍵值，在 PHP 裡面你很簡單的就能靠*陣列（Array）*辦到。

```php
$data = ['username' => 'YamiOdymel',
         'password' => '2016 Spring'];
         
echo $data["username"]; // 輸出：YamiOdymel
```

真是太棒了，那麼 Golang 呢？用 `map` 是差不多啦。如果有必要的話，你可以稍微複習一下先前提到的「多資料儲存型態－Stores」。

```go
data := make(map[string]string)

data["username"] = "YamiOdymel"
data["password"] = "2016 Spring"

fmt.Println(data["password"])
```

&nbsp;

## 是否存在－Isset

你很常會在 PHP 裡面用 `isset()` 檢查一個索引是否存在，不是嗎？

```php
// 如果 $data['username'] 存在
if(isset($data['username']))
{
    $username = $data['username'];
}
```

在 Golang 裡面很簡單的能夠這樣辦到（僅適用於 `map`）：

```go
username, exists := data["username"]

if !exists {
    fmt.Printf("你要找的資料不存在。")
}
```

&nbsp;

## 指針－Pointer

指針（也做參照）是一個像是「變數別名」的方法，這種方法讓你不用整天覆蓋舊的變數，

讓我們假設 `A = 1; B = A;` 這個時候 `B` 會複製一份 `A` 且**兩者不相干**，

倘若你希望修改 `B` 的時候實際上也會修改到 `A` 的值，就**會需要指針**。

在 PHP 的實際範例是這樣：

```php
function zero(&$number) // & 即是指針
{
    $number = 0;
}

$A = 5;
zero($A);

echo $A; // 輸出：0
```

在 Golang 你需要用上 `*` 還有 `&` 符號，你可以稍微這樣區分這兩者：

> `&`：我給你

> `*`：還給你

```go
func zero(number *int) {
    *number = 0
}

func main() {
    A := 5;
    zero(&A)

    fmt.Printf("%d", A) // 輸出：0
}
```

--

**額外參考**

[《Effective Go》中英双语版](https://bingohuang.gitbooks.io/effective-go-zh-en/content)

[Go語言聖經（中文版）](https://wizardforcel.gitbooks.io/gopl-zh/content)

[Go Web 编程](https://astaxie.gitbooks.io/build-web-application-with-golang/content)
