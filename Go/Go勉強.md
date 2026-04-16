Go言語自体の標準ライブラリが強くフレームワークなどに依存しなくても開発が容易である
5年 10年続くプロジェクトではフレームワークが古くなってしまうことが最大のリスクになってしまう

go言語には例外がない
goの関数には戻り値に==err==と書くことが多い
```go
if err != nil {
	return err
}
```

### 予約後が圧倒的に少ない
```go
break default func interface select case defer go map struct chan else goto package switch const fallthrough if range type continue for import return var
```
javaは50個
c++ 70個以上ある

### Goのフォーマッタ
goでは公式がgoのフォーマッたを提供しているため、書き方の流派が生まれない
誰が描いてもコードの見た目が同じようになる
```bash
go fmt <file名>
go fmt ./... #全てのファイルに対してフォーマットを適応する
```
[[セミコロン挿入規則]] 補足の話

### Githubからそのままimportできる
Goはパッケージ管理がシンプルで、**GitHubのURLをそのまま import** できる。
依存関係がコード上で明示されていて、どこから来たか一瞬で分かる。
```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "GitHubからインポートしたGinで動いています！",
		})
	})
	r.Run()
}
```

### 並列処理が容易
```go
package main

import (
	"fmt"
	"time"
)

func doSomething() {
	for i := 0; i < 5; i++ {
		fmt.Println("goroutine:", i)
		time.Sleep(500 * time.Millisecond)
	}
}

func main() {
	go doSomething() // 👈 並列実行スタート！

	for i := 0; i < 5; i++ {
		fmt.Println("main:", i)
		time.Sleep(500 * time.Millisecond)
	}
}
```


goはpackageで構成されています

## package
```go
package main
```
プログラムはmainパッケージから始まる と言いつつも

 ```go
 my-project/
├── main.go
└── auth/
      ├── auth.go  (package auth)
      └── util.go  (package auth)
 ```
ディレクトリpakageの名前は変更される
## Imports
```go
import "fmt"
import "math"

import (
	"fmt"
	"math"
)
```
importステートメントは複数個宣言することもできる

自分で作成したパッケージを取得する場合は以下のように書く
 ```go
 package main

import (
    "fmt" 
    //インポートパスはモジュール名とフォルダ名
    "/domain"
)
 ```

もしfmtって名前でディレクトリを作った場合は　エイリアスをつけて回避することができる
```go
mport ( 
	"fmt"
	myfmt "my-project/fmt" // 自作の fmt に 'myfmt' という別名をつける 
)
```
## Exported names
```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Println(math.pi)
}
```
最初の文字が大文字で始まる名前は参照できるエクスポートされた名前です
小文字から始まる場合は外部から参照できない

## Functions
```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```
変数名の後に関数を書く

## Functions continued
```go
package main

import "fmt"

//　省略することが可能である
func add(x, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```
型の省略ができる。　x,yがint型である場合は ==x,y int==とすることができる
## Multiple results
```go
package main

import "fmt"

//　複数行の値を返すことができる
func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```
:= は宣言代入をすることができる、暗黙的なものであるためあまり推奨されない

## Named return values
```go
package main

import "fmt"

//x,y intは関数の先頭でローカルで宣言された扱いになります
func split(sum int) (x, y int) {
	x = sum * 4 / 999_999_999_999
				  9999999999999
	y = sum - x
	return 
}
// 長い数字リテラルを読みやすくするために、整数リテラルの任意の箇所に「_」をかけます、ただし、先頭と最後には書けません「_」を連続させることもできない 1234は1_234とも表すことができる、数値に影響はない


func main() {
	fmt.Println(split(17))
}
```

## Variables
```go
package main

import "fmt"

//形宣言だけしたい時に使う 0が明示的に入る
//　グローバル変数は varを使わなければいけない
var c, python, java bool

func main() {
	//変数宣言
	var i int
	fmt.Println(i, c, python, java)
}
```

## Variables with initializers
```go
package main

import "fmt"

var i, j, k int = 0, nil, ""

func main() {
	//複数の値を同時に宣言と代入することができる
	var c, python, java bool
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

## Short variable declarations
```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	k = 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

## Basic Types
```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 の別名

rune // int32 の別名
     // Unicode のコードポイントを表す

float32 float64

complex64 complex128
```
runeは古代文字を表す言葉(runes)ですが、Goでは文字そのものを表すためにruneという言葉を使います　complex64は複素数、実部と虚部を持っている(float32,float32)で宣言されている


```Go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
	fmt.Printf("Type: %T Value: %v\n", z, z) //2+3i
}
```

## Type conversions
```go
package main

import "fmt"

func main() {
	var i int = 42
	
	// 形変換するときは明示的に記述しなければいけない、
	// この場合は右辺から型を推測する
	var f_64 float64 = float64(i)
	var str string = string(i)
	fmt.Printf("%T,%T,%T", i, f_64, str)
	fmt.Println(str)
}
```

## Type inference
```go
package main

import "fmt"

func main() {
	//右辺から値を検知する
	v := 42 
	var v1 = 42
	// どちらも同じ結果になる
	
	fmt.Printf("v is of type %T\n", v)
}
```
:= はvarがなくても右辺の型から暗黙的に型を検知して代入する

## Constants
```go
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```
==const==のキーワードを使って変数と同じように宣言します
文字　文字列　boolean 数値で使える

## Numeric Constants
```go
package main

import "fmt"

const (
	// Create a huge number by shifting a 1 bit left 100 places.
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	// Shift it right again 99 places, so we end up with 1<<1, or 2.
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}

```

---
##  基本構文

## for文
```go
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}

```
for文はほぼ一緒 for文で型宣言はできない　for i int = 0これで宣言する事はできない
```go
	for ; sum < 1000; {
		sum += sum
	}
```
初期化と後処理のステートメント記述は不要

## while文
```go
	for sum < 1000 {
		sum += sum
	}
```
go言語にwhilel自体は存在せず、for文で解決する

```go
	for {
	}
```
これは ==whire (true)==と一緒

## if文
```go
if i != 10 {
	return true
}
```

## If with a short statement
```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	//ifの前に簡単なステートメントを記述することができる
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

ステートメントは 
```go
int x = 10;      // これは「代入ステートメント」
if x > 5 {     // これは「if ステートメント」
    printf("ok"); // これは「関数呼び出しステートメント」
} else {
	fmt.println("hello")
}

```

## Switch
```go
package main

import "fmt"

func main(){
	x := 3

	swich x {
	case 1,2,3:
		fmt.Println("x is either 1, 2, or 3")
		fallthrough
	case 4,5,6:
		fmt.Println("x is either 4, 5, or 6")
	case 7,8,9:
		fmt.Println("x is either ")
	default
		fmt.Println("x is not a number")
	}
}

```

## Defer
```go
package main

import "fmt"

func main() {
	//この関数の実行を呼び出し元の関数がreturnされるまで遅延させたもの
	//この場合だと"hello would"になる
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

## Pointers
```go
//　初期値は nil
var p *int

i := 42
p = &i
```
cと変わらないが　ポインタ減算がgoには存在しない↓のような
```c
int arr[] = {10, 20, 30, 40, 50};
int *p = arr;      // p は arr[0] を指す
int *q = p + 2;    // ポインタ演算 arr[2]を示す
```

## 構造体
```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
	
	// 明示的に代入することもできる
	v := Vertex{Y: 2, X: 1}
	
	// 片方を宣言することもできる
	fmt.Println(v.X)
}
```


## レシーバ
特定の型に紐付いた関数を定義する仕組みです
 ```go
 package main
 
 import "fmt"
 import "math"
 
 type Vertex struct {
	 X int
	 Y int
 }
 
 // 自分の型にも適応可能
func (v Vertex) Abs() int {}
func Abs(v Vertex) int {}
 
 func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
 }
 
 func main (){
	 
 }
 
 ```

## Pointers and functions
 ```go
 type Vertex struct {
	X, Y float64
 }
 
 func Scale(v *Vertex, f float64) float64　{
	 return math.Sqrt(v.X*v.X + v.Y*v.Y)
 }
 
 func main (){
	 v := Vertex{4,2}
	 fmt.Pritnln(Sclae(&v, 10))
 }
 ```
※goにオーバーロードは存在しません

## 配列リテラル
```go
var x = [3]int{10,20,30} // 10 20 30
var x = [12]int{1,5:4,6,10:100,15} // 1, 0, 0, 0, 0, 4, 6, 0, 0, 0, 100, 15
var x = [...]int{10,20,30} //この場合は10,20,30の[3]が作られる
fmt.Prinln(len(x)) // x = 3
```

## range
```go
var x = [3]int{1,2,3} 

func main () {
	for i,v := range x{
		//iはindex
		//vはvalue
	}
}
```

[[クリーンアーキテクチャ]]


#レイヤードアーキテクチャ
上から下への一方通行のアーキテクチャ
UI -> ビジネス -> DBの順番

#クリーンアーキテクチャ
中身を守るエン構造

#ヘキサゴナルアーキテクチャ

#オニオンアーキテクチャ
ドメイン駆動開発