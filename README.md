# 從 PHP 到 Golang 的筆記

## 定義變數－Variables

在 PHP 裡面你想建立一個變數就直接建立，夭壽讚，是嗎？

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

## 關聯陣列－Associative Array

這真的是很常用到的功能，就像物件一樣有著鍵名和鍵值，在 PHP 裡面你很簡單的就能靠*陣列（Array）*辦到。

```php
$data = ['username' => 'YamiOdymel',
         'password' => '2016 Spring'];
         
echo $data["username"]; // 輸出：YamiOdymel
```

真是太棒了，那麼 Golang 呢？完全不是這麼一回事，還記得 Golang 是強型別語言嗎，

在 Golang 裡面**陣列僅有索引**，並沒辦法將其賦予鍵名，取而代之的是**建構體（Struct）**，

你需要先替你的「關聯陣列」建立一個建構體。

```go
type Data struct {
    username string
    password string
}

func main() { 
    data := Data {
        username: "YamiOdymel",
        password: "2016 Spring"}
    
    fmt.Println(data.username) // 輸出：YamiOdymel
}
```

好吧⋯⋯就目前來說還沒有那麼地糟。

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

> `*`：還給我

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
