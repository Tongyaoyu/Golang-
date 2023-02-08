# Go语言学习指南

## 1.基础类型和变量声明

### 1.1 内置类型

#### 1.1.1 零值

go语言会给声明但未赋值的变量默认赋零值

|   类型   |         零值         |
| :------: | :------------------: |
| bool类型 |        false         |
|   整形   |          0           |
|  浮点型  |          0           |
|  字符串  |          ""          |
|   字符   | 0(rune是int32的别名) |
|   复数   |   实部和虚部均为0    |

#### 1.1.2 字面量 

一个值的字面形式称为一个字面量

1. 整数字面量默认情况是十进制的数字序列，使用不同前缀代表不同进制。0b或0B代表二进制，0o或0O或0代表八进制，0x或0X代表十六进制。go语言可以将作为字面量中的间隔来增强可读性，且没有任何副作用。例如：1_234、1_2_3_4，只要不在开头和结尾就可以。
2. 浮点数字面量由整数、小数点、小数部分组成。也可以表示成指数形式，如6.03e3，也可以使用_分割较长的浮点数字面量。
3. 字符字面量由被一堆单引号包围的字符组成，例如Unicode字符('a')，8位八进制数('\141')，8位十六进制数('\x61')。
4. 字符串字面量有两种格式：原始字符串字面量和解释型字符串字面量。前者可以包含除了反引号之外的任意字符，例如\`Greetings and "Salutations"`,后者需要转义，例如"Greetings and \n\\"Salutations\\""

需要说明的是，Go中字面量是无类型的，Go会为未声明类型的变量赋予默认类型，如var x=10的默认类型就是int类型

#### 1.1.3 布尔型

布尔型只有两种状态值:false和true

#### 1.1.4 数值类型

**1.整形**

包含有符号和无符号整形。包含有int8 int16 int32 int64 uint8 uint16 uint32 uint64，其中有几个比较特殊

- byte是uint8别名，二者之间可以赋值、比较或者执行数学运算
- int在32位cpu等同于int32，在64位cpu等同于int64，由于不同平台的差异，在不做类型转换的情况下，几个类型间不能相互赋值、运算等。
- uint只能是0或者合法取值范围以内的正数
- 最后两个是rune和uintptr

整形操作符包含有+、-、*、/、%、+=、-=、\*=、/=、%=，位运算符包括<<、>>、&、|、^、&^，他们也可以右边加等号

**2.浮点型**

两种：float32和float64，尽量使用float64，精度高，浮点数可以进行除了%意外的任意数学运算或比较运算。有几点除法特性

- 任何非0的数除以浮点数0，将返回+Inf或-Inf
- 0除以0时得到NaN
- 最好不用==和!=比较运算符，因为计算机除了浮点数会丢失精度

**3.复数**

两种：complex(64)和complex(128)，前者实部和虚部为float32，后者是float64

- 当使用无类型常量或者字面量作为参数，complex也是无类型，默认是128
- 当两个参数都是float32类型时，函数将返回complex64类型的值
- 任何一个参数是float32类型时，函数将返回complex64类型的值
- 其他情况都是complex128类型的值

```
x:=complex(2.5,3.1)
```

#### 1.1.5 字符串和字符

**1.字符串** 

string类型，可以使用比较运算符，使用+将字符串合并成一个新的字符串。字符串不可变，一旦修改了一个 字符串变量的值后，字符串其实已经不是原来的字符串了

**2.字符**

字符串中每一个Unicode字符被称为字符（rune），而且字符是一种特殊类型，它其实是int32的别名，建议使用rune

#### 1.1.6 显示类型转换

Go语言不允许变量之间的自动类型转换，例如java的隐式类型转换，必须是显示的类型转换

```go
var x int = 10
var y float64 = 30.2
var z float64 = float64(x) + y //40.2
var d int = x + int(y) //40
```

### 1.2 var与:=

var有以下几种使用方式

```go
var x int = 10 

var x = 10  //10的默认类型时整型

var x int //int零值是0

var x,y int = 10,20 //多个相同类型变量

var x,y int //多个零值

var x,y = 10, "hello" //多个类型变量不一致

var(
	x   int
    y       = 20
    z   int = 30
    d,e     = 40,"hello"
	f,g string
)
```

:= 有以下几种使用方式

```go
x := 10

x,y := 10,"hello"

//:=允许对已赋值的变量再次赋值
x := 10
x,y := 30, "hello"
```

短声明格式只能用来声明在函数内部的局部变量，如果在包级别声明变量，只能使用var

### 1.3 const关键字

```go
const x int64 = 10
```

常量和var一样，可以再包级别声明，也可以在函数中声明。const关键字在编译的时候赋值，可以使用以下方式赋值：

- 数字字面量
- true和false
- 字符串
- 字符
- 内置函数complex、real、img、len和cap
- 包含预声明（内置）的值和运算符的表达式 

为了避免参数传递给函数的变量被修改，go语言函数调用是值传递

### 1.4 有类型的常量和无类型的常量

无类型声明如下

```go
const x = 10
//此时x类型不确定，所以以下赋值操作合法
var y int = x
var z float64 = x
var d byte = x
```

有类型常量声明如下

```go
const y int = 10
```

### 1.5 未使用的变量

go语言要求每个声明的局部变量必须被读取（read），声明局部变量而不读取其值会导致编译时错误

### 1.6 常量与变量的命名

以字母或者下划线开头，名称中可以包含任意数字、下划线或者字母，go语言的字母和数字有更广泛的定义没任何Unicode字符的字母或者字符都是允许的。

> 约定使用驼峰命名法indexCounter，而不是蛇形命名法index_counter



## 2.复合类型

### 2.1 数组

声明方式如下

```go
var x [3]int //都是0

var x = [3]int{10，20，30}

var x = [12]int{1,5:4,6,10:100,15} //[1,0,0,0,0,4,6,0,0,0,100,15]

var x = [...]int{10,20,30} //不定义具体长度

//可以使用==和!=来比较数组
var x = [...]int{1,2,3}
var y = [3]int{1,2,3}
fmt.Println(x==y) //true

len(x) //数组长度
```

> 需要注意的是
>
> go认为数组的长度属于其类型的一部分，[3]int与[4]int是不同的类型，也意味着不能使用变量去指定数组的长度，因为类型必须在编译时而不是运行时解析

数组可以用==、!=来比较

```go
var x = [...]int{1,2,3}
var y = [3]int{1,2,3}
fmt.Println(x == y)	//true
```

### 2.2 切片

与数组不同，切片长度不是切片类型的一部分，定义方式如下

1.声明定义切片字面量，不指定他的长度

```go
var x = []int{10,20,30}

var x = []int{1,5:4,6,10:100,15}

var x = [][]int{}	//定义多维切片
```

2.声明一个不带字面量的切片

```go
var x []int
```

因为未赋值，x被赋予切片的零值nil。nil与其他语言的null不同，他代表某些类型没有值，nil没有类型，因此他能被赋值或者和其他类型比较。如果一个切片不包含任何内容，那么值就是nil。

**切片不能被比较，不能使用==和!=比较切片，切片只能和nil比较**

```go
fmt.Println(x == nil)	//true
```

#### 2.2.1 len

如果对len传入值为nil的切片，返回0

```go
var x []int
len(x) //0
```

len的参数可以是：数组、切片、字符串、映射、通道类型

**2.2.2 append**

append用于为切片增加元素，他至少接受两个参数：切片、切片元素类型的值

```go
var []int x
x = append(x,10)

var x = []int{1,2,3}
x = append(x,4)

x = append(x,5,6,7)

y := []int{20,30,40}
x = append(x,y...)	//代表把y展开 
```

> 需要注意
>
> go是传值调用语言，append传入的切片是源切片的拷贝，返回的也是这个拷贝的值，最终需要将拷贝的新切片重新赋值给源切片，否则会编译时错误

#### 2.2.3 容量

当切片通过append添加元素时，go运行时需要时间来分配新的内存，并将数据从就的内存空间拷贝到新内存空间

使用cap函数返回当前切片的容量，cap也可以接受数组参数，但是对数组使用cap和len结果一样，因为数组容量就是数组长度

```go
var x []int
fmt.Println(len(x),cap(x))	//0 0

x = append(x,10,20,30,40,50)
fmt.Println(len(x),cap(x))	//5 6
```

#### 2.2.4 make

切片的另一种声明方法，对于已知需要切片有多少元素，使用make指定类型、长度、可选容量

```go
x := make([]int,5)	//长度为5，容量为5的整形切片，元素全为0

x = append(x,10)	//x是[0,0,0,0,0,10]，长度为6，容量为10

x := make([]int,5,10)	//长度为5，容量为10的整型切片

x := make([]int,0,10)
x = append(x,5,6,7,8)	//x是[5,6,7,8]
```

#### 2.2.5 切片的声明

1.声明一个nil切片

```go
var data []int
```

2.声明空字面量切片，这是零长度切片，并不是nil

````go
var x = []int{}
````

3.声明有默认值切片

```go
x := []int{2,4,6,8}
```

4.声明具体容量切片

```go
x := mak([]int,5,10)
```

#### 2.2.6 派生切片

从一个切片派生另一个切片，注意参数是左闭右开

```go
x := []int{1,2,3,4}
y := x[:2]	//[1,2]
z := x[1:]	//[2,3,4]
d := x[1:3]	//[2,3]
e := x[:]	//[1,2,3,4]
```

需要注意，派生切片会共享父切片内存

```go
x := []int{1,2,3,4}
y := x[:2]
fmt.Println(cap(x),cap(y))	//4 4
y = append(y,30)
fmt.Println(x)	//x:[1,2,30,4]
fmt.Println(y)	//y:[1,2,30]
```

> 子切片容量是父切片容量减去子切片的其实偏移量

使用完全派生表达式避免append影响，最后的数字定义了父切片容量中最后位置，将其减去起始偏移量就是子切片的实际容量

```go
x := make([]int,0,5)
x = append(x,1,2,3,4)
y := x[:2:2]	//y[1,2] 容量：2-0
z := x[2:4:4]	//z[3,4] 容量：4-2
y = append(y,30,40,50)	//y[1,2,30,40,50]
x = append(x,60)	//x[1,2,3,4,60]
z = append(z,70)	//z[3,4,70]
```

原因是y和z的容量都为2，添加新元素容量不够，会创建新切片，这样和父切片就不会相互影响

#### 2.2.7 数组转换为切片

注意，数组派生也存在内存共享问题

```go
x := [4]int{5,6,7,8}
y := x[:2]	
z := x[2:]
x[0] = 10

//x[10,6,7,8] y[10,6] z[7,8]
```

#### 2.2.8 copy

copy创建一个独立于源切片的切片，他返回拷贝元素的数量

```go
x := []int{1,2,3,4}
y := make([]int,2)
num := copy(y,x)
fmt.Println(y,num)	//[1,2] 2

y := make([]int,2)
copy(y,x[2:])	//y[3,4]

num = copy(x[:3],x[1:]) 
fmt.Println(x,num)	//x[2,3,4,4] 3
```

copy也可以用于数组

```go
x := []int{1,2,3,4}
d := [4]int{5,6,7,8}
y := make([]int,2)
copy(y,d[:])
copy(d[:],x)
fmt.Println(y)	//[5,6]
fmt.Println(d)	//[1,2,3,4]
```

### 2.3 字符串、字符和字节

使用方法如下

```go
//通过索引取单个值
var s string = "Hello there"
var b byte = s[6]

//派生
var s string = "Hello there"
var s2 string = s[4:7]	//s2 o t
var s3 string = s[:5]	//s3 Hello
var s4 string = s[6:]	//s4 there
```

go是用一系列字节来表示字符串，而不是字符。UTF-8的码位长度为1~4个字节，上面的例子是一个字节长的UTF-8码位 

```go
var s string = "Hello ☀"
var s2 string = s[4:7]	//s2 o �
var s3 string = s[:5]	//s3 Hello
var s4 string = s[6:]	//s4 ☀

var s string = "Hello ☀"
fmt.Println(len(s))	//10 因为☀在utf8中是4个字节
```

一个字符或字节可以转换为字符串

```go
var a rune = 'x'
var s string = string(a)	//x
var b byte = 'y'
var s2 string = string(b)	//y

var x int = 65
var y = string(x)
fmt.Println(y)	//A
```

将字符串转换为切片

```go
var s string = "Hello ☀"
var bs []byte = []byte(s)
var rs []rune = []rune(s)
fmt.Println(bs)
fmt.Println(rs)

//bs [72 101 108 108 111 32 226 152 128]
//rs [72 101 108 108 111 32 9728]
```

### 2.4 映射

1.var声明一个值为零值的映射变量

```go
var nilMap map[string]int
```

映射的零值为nil，值为nil的映射长度为0。尝试读取一个nil映射总是会返回改映射值类型的零值，尝试对nil映射写入会导致崩溃。

2.使用var和:=，通过映射字面量来创建映射变量

```go
totalWins := map[string]int{}
var m =   map[string]int{}
```

上述这种情况创建了一个长度为0的映射字面量，可以进行读写

```go
//创建非空映射字面量
teams := map[string][]string{
		"Orcas":   []string{"Fred", "Ralph", "Bijou"},
		"Lions":   []string{"Sarah", "Peter", "Billie"},
		"Kittens": []string{"Waldo", "Raul", "Ze"},
	}
```

> 注意：最后一行也需要逗号，否则会报错

3.使用make创建一个有默认长度的映射

```go
ages := make(map[int][]string, 10)
fmt.Println(len(ages))	//0
```

使用make创建的映射长度为0，并且长度可以超过初始定义长度。

> 映射不可被比较，映射的可以使任何可比较的类型，这意味着切片和映射都不能最为映射的值

#### 2.4.1 映射的读写操作

```go
totalWins := map[string]int{}
totalWins["Orcas"] = 1
totalWins["Lions"] = 2
fmt.Println(totalWins["Orcas"])
fmt.Println(totalWins["Kittens"])
totalWins["Kittens"]++
fmt.Println(totalWins["Kittens"])
totalWins["Lions"] = 3
fmt.Println(totalWins["Lions"])

//结果为
1
0
1
3
```

上例可以看到，尝试读取一个不存在的键时，映射会返回映射的值类型的零值

#### 2.4.2 逗号和ok模式

go提供了逗号和ok模式来检测返回零值是由于存在零值关联的键还是因为这个键不存在。ok为true代表该键存在，ok为false代表该键不存在

```go
m := map[string]int{
    "hello":5,
    "world":0,
}
v,ok := m["hello"]
fmt.Println(v,ok)	//5 true

v,ok := m["world"]
fmt.Println(v,ok)	//0 true

v,ok := m["goodbye"]
fmt.Println(v,ok)	//0 false
```

#### 2.4.3 从映射中删除

```go
m := map[string]int{
    "hello":5,
    "world":0,
}
delete(m,"hello")
```

如果这个键在映射中不存在或者映射本身为nil，则什么事情都不会发生。

#### 2.4.4 映射模拟集合

集合就是set，集合内的值唯一，并不保证顺序。go没有集合，可以用映射模拟，值作为键、布尔值作为值的类型

```go
inSet := map[int]bool{}
vals := []int{5, 10, 2, 5, 8, 7, 3, 9, 1, 2, 10}
for _, v := range vals {
    inSet[v] = true
}
fmt.Println(len(vals), len(inSet))
fmt.Println(inSet[5])
fmt.Println(inSet[500])
if inSet[100] {
    fmt.Println("100 is int the set")
}

//结果是
11 8
true 
false
```

### 2.5 结构体

```go
type person struct {
    name string
    age int
    pet string
}
```

定义了新的结构体类型后，我们可以对这个变量使用这个类型

```go
var fred person
```

fred没有赋值，所以他的值是person结构体类型的零值，即结构体中每个字段都设置为零值

```go
//赋值结构体字面量
bob := person{}
```

与映射不同的是，赋值为空结构体和不赋值是一样的，这两种情况都会将接固体的所有字段设置为零值。

有两种方式定义非空结构体字面量

方式一：需要对每个字段赋值，并且赋值顺序需要与声明时字段顺序保持一致

```go
julia := person{
    "Julia",
    40,
    "cat",
}
```

方式二：与顺序无关

```go
beth := person{
    age : 30,
    name : "Beth"
}
```

使用字段

```go
bob.name = "Bob"
```

#### 2.5.1 匿名结构体

可以为一个变量指定一个结构体，而不需要用type来定义结构体名字，这就是匿名结构体：

```go
//方式一
var person struct{
    name string
    age int
    pet string
}
person.name = "bob"
person.age = 50
person.pet = "dog"

//方式二
pet := struct{
    name string
    kind string
}{
    name:"Fido",
    kind:"dog",
}
```

#### 2.5.2 比较和转换结构体

判断结构体是否可以比较取决于结构体的字段，若结构体中所有字段都是可以比较的类型，那么结构体就可以比较；如果字段中有映射或者切片则不能比较

go不可以比较两个不同基础类型的变量，也不可以比较两个不同类型的结构体变量。但是go可以做到结构体间的类型转换，只需满足：两个结构体的所有字段、名称、顺序、类型相同。如下

```go
type firstPerson struct{
	name string
	age int
}

type secondPerson struct{
	name string
	age int
}

//可以使用类型转换将firstPerson实例转换为secondPerson，但是不能直接用==比较firstPerson和secondPerson
x := firstPerson{}
y := secondPerson(x)

//不能将firstPerson实例转换为thirdPerson，因为字段顺序不同
type thirdPerson struct{
	age int
    name string
}

//不能将firstPerson实例转换为fourthPerson，因为他有一个额外的字段
type fourthPerson struct{
	firstName string
	age int
}

//不能将firstPerson的实例转换为fifthPerson，因为他有一个额外的字段
type fifthhPerson struct{
	name string
	age int
    favoriteColor string
}
```



## 3.代码块、影子变量和流程控制

### 3.1 代码块

每个使用声明的区域称为代码块，在函数之外声明的变量、常量、类型和函数的区域称为包代码块。

当代码块外部又有与代码块内部同名的变量标识符时会发生什么呢？答案是代码块外部的变量将会被覆盖

#### 3.1.1 变量覆盖

```go
func main() {
	x := 10
	if x > 5 {
		fmt.Println(x)	//10
		x := 5			//影子变量
		fmt.Println(x)	//5
	}
	fmt.Println(x)	//10
}
```

影子变量是指这个变量在其代码块外部拥有相同的变量。上例中if中的x把外部的x覆盖了，if执行结束后，影子变量x结束了覆盖，需要注意x既没有消失也没有被重新赋值，只是在被覆盖的代码块内无法被访问。

> 避免使用:=给已声明的变量赋值，因为变量容易被意外的覆盖而变成影子变量
>
> 我们可以使用:=创建多个变量并赋值，并且:=左边可以不都是新变量，只要有一个新变量就是合法的

```go
//多值覆盖
func main() {
	x := 10
	if x > 5 {
		x,y := 5,20			//影子变量
		fmt.Println(x,y)	//5 20
	}
	fmt.Println(x)	//10
}
```

尽管if外已有x的定义，但是x仍然在if语句中被覆盖，这是因为:=仅重用在当前块中声明的变量。使用:=时，清确保左侧没有任何来自当前代码块外部的变量，否则这些变量会被覆盖成影子变量。

### 3.2 if 语句

go语言的不需要在条件两端加括号

```go
n := rand.Intn(10)
if n == 0 {
   fmt.Println("low")
} else if n > 5{
   fmt.Println("too big",n)
}else{
   fmt.Println("good",n)
}
```

go可以再if-else中声明变量，并且只在改代码块中使用

```go
if n := rand.Intn(10); n == 0 {
   fmt.Println("low")
} else if n > 5{
   fmt.Println("too big",n)
}else{
   fmt.Println("good",n)
}
```

### 3.3 for 语句

#### 3.3.1 使用标准形式

```go
for i:=0;i<10;i++ {
    fmt.Println(i)
}

for i,j := 0,1; i < 10; i++ {
    fmt.Println(i,j)
}
```

#### 3.3.2 使用条件比较

类似于其他语言的while语句

```go
i := 1
for i < 100 {
    fmt.Println(i)
    i = i*2
}
```

#### 3.3.3 使用无限循环

```go
for i<100 {
    fmt.Println("hello")
}
```

#### 3.3.4 break和continue

```go
 //break
for {
    if !CONDITION{
        break
    }
}

//continue
for i:=0;i<10;i++ {
    if i%3 == 0{
        fmt.Println(i);
        continue;
    }
}
```

#### 3.3.5 使用for-range

for-range有两个变量，第一个是数据在整个被迭代数据的位置，第二个是该位置的值

```go
evenVals := []int{2,4,6,8,10,12}
for i,v := range envenVals {
    fmt.Println(i,v);
}
//结果
0 2
1 4
...
```

for-range可以忽略索引变量

```go
for _,v := range evenVals {
	fmt.Println(i,v);
}
//结果
2
4
...
```

for-range可以直接互虐第二个变量的值，只要索引值

```go
uniqueNames := map[string]bool{"Fred":true,"Raul":true,"Wilma":true}
for k:= range uniqueNames{
    fmt.Println(k);
}
```

**迭代映射类型**

无序的迭代，这是go语言为了防止哈希碰撞的DoS攻击做出的调整

```go
m := map[string]int{
    "a":1,
    "c":3,
    "b":2
}

for i:=0;i<3;i++ {
    fmt.PrintLn("Loop",i)
    for k,v := range m{
        fmt.Println(k,v);
    }
}

//结果
Loop 0
c 3
b 2
a 1
Loop 1
a 1 
c 3
b 2
Loop 2
b 2
a 1
c 3
```

**迭代字符串**

```go
samplles := []string{"hello","apple_n!"}
for _,sample := range samples{
    for i,r := range sample{
         fmt.Println(i,r,string(r))
    }
    fmt.Println()
}
//结果
0 104 h
1 101 e
2 108 l
3 108 l
4 111 o
```

第一列是索引，第二列是字母的Unicode编码数值，第三列是将字母的Unicode编码数值转换成的字符串，后面的结果如下

```go
0 97  a
1 112 p
2 112 p
3 108 l
4 101 e
5 95 _
6 960 π
8 33 !
```

π的编码远大于一个字节的长度，所以for-range遍历字符串时，遍历的是字符，而不是字节.

**for-range的值是副本**

for-range循环遍历复合类型时，都会将值从符合类型中复制到值变量中，修改值变量不会导致符合类型中的数据变更

```go
evenVals := []int{2,4,6,8,10,12}
for i,v := range envenVals {
    v *= 2
}
fmt.Println(evenVals)	//[2,4,6,8,10,12]
```

#### 3.3.6 for语句的标签

```go
func main() {
	samples := []string{"hello", "apple_π"}
outer:
	for _, sample := range samples {
		for i, r := range sample {
			fmt.Println(i, r, string(r))
			if r == 'l' {
				continue outer
			}
		}
		fmt.Println()
	}
}
//结果
0 104 h
1 101 e
2 108 l
0 97 a 
1 112 p
2 112 p
3 108 l
```

### 3.4 switch语句

```go
words := []string{"a", "cow", "smile", "gopher", "octopus", "anthropologist"}
	for _, word := range words {
		switch size := len(word); size {
		case 1, 2, 3, 4:
			fmt.Println(word, "is a short word")
		case 5:
			wordLen := len(word)	//只在该case中可见
			fmt.Println(word, "is exactly the right length:", wordLen)
		case 6, 7, 8, 9:
		default:
			fmt.Println(word, "is a long word")
		}
	}
//结果
a is a short word
cow is a short word                 
smile is exactly the right length: 5
anthropologist is a long word 
```

> 任何可以使用==比较的类型均可以在switch语句中使用，包括所有内置类型，除了切片、映射、通道、函数，以及包含以上这些类型字段的结构体

> go语言switch默认相当于每个case最后带有break，匹配成功后不会向下继续执行其他的case语句，而是跳出整个switch，但是可以使用fallthrough强制执行后面的case语句中的代码

如果for循环中有一个switch语句，可以再for循环上价格标签，并在break语句处使用这个标签

```go
loop:
for i:= 0;i<10;i++{
    ...
    case: i%7 == 0:
    	...
    	break loop
    ...
}
```

### 3.5 默认的switch语句

你可以写一个switch语句不指定比较的值，这叫做默认的switch。

```go
words := []string{"a", "cow", "smile", "gopher", "octopus", "anthropologist"}
	for _, word := range words {
		switch wordLen := len(word); {
		case wordLen < 5:
			fmt.Println(word, "is a short word")
		case wordLen > 10:
			fmt.Println(word, "is a long word")
		default:
			fmt.Println(word, "is exactly the right length:", wordLen)
		}
	}
```

switch后的变量声明作为默认的比较变量

### 3.6 goto语句

goto语句指定一个指向代码行的标签，然后执行跳转到改行。然而，你不能在任何地方跳转。go语言严禁跳过变量声明、跳转到内部或平行代码块。

```go
	a := 10
	goto skip
	b := 20
skip:
	c := 30
	fmt.Println(a, b, c)
	if c > a {
		goto inner
	}
	if a < b {
    inner:
		fmt.Println("a is less than b")
	}
//报错
```

正确使用

```go
	a := rand.Intn(10)
	for a < 100 {
		if a%5 == 0 {
			goto done
		}
		a = a*2 + 1
	}
	fmt.Println("loop completes")
done:
	fmt.Println("something wrong")
	fmt.Println(a)
```

> goto能不用就不用

## 4.函数

### 4.1 函数的声明和调用

函数声明有四个部分：关键字func、函数名、传入参数和返回类型

```go
func div(num1 int, num2 int) int {
	if num2 == 0{
		return 0
	}
	return num1/num2
}
```

多个相同类型可以这样声明

```go
func div(num1, num2 int) int { }
```

#### 4.1.1 模拟命名参数和可选参数

go中没有的两个功能：命名输入参数和可选输入参数

*命名参数*是C#中的，使我们可以轻松地记住或查找被调用方法的参数列表中的参数顺序。每个参数的参数可以通过参数名称指定。

```c#
NamedParameterFunction(firstName: "Hello", lastName: "World")
```

go语言可以用结构体模拟

```go
type MyFuncOpts struct {
	FirstName string
	LastName string
	Age int
}
func MyFunc(opts MyFuncOpts) error {
	fmt.Println(opts)
	return nil
}
func main(){
    MyFunc(MyFuncOpts{
        LastName:"peter"
        Age:50
    })
}
```

*可选参数*可以不传入值

#### 4.1.2 可变长输入参数与切片

可变长参数必须是输入参数列表中的最后一个或者唯一一个参数

```go
，func addTo(base int, vals ...int) []int {
	out := make([]int, 0, len(vals))
	for _, v := range vals {
		out = append(out, base+v)
	}
	return out
}

func main(){
    fmt.Println(addTo(3))
	fmt.Println(addTo(3, 2))
	fmt.Println(addTo(3, 2, 4, 6, 8))
	a := []int{4, 3}
	fmt.Println(addTo(3, a...))
	fmt.Println(addTo(3, []int{1, 2, 3, 4, 5}...))
}

//结果
[]
[5]        
[5 7 9 11] 
[7 6]      
[4 5 6 7 8]
```

> 需要注意，变量和切片字面量后面必须加上三个点，否则会编译错误

#### 4.1.3 多返回值

```go
func div2(num1 int, num2 int) (int, int, error) {
	if num2 == 0 {
		return 0,0,errors.New("cannot divide by zero")
	}
	return num1 / num2,num1%num2,nil	//返回值不需要加括号
}
```

> 按照约定，error总是函数的最后一个或者唯一一个返回值

#### 4.1.4 忽略返回值

如果不需要所有返回值，可以这么写

```go
result,_ := div2(5,2)
```

#### 4.1.5 命名返回值

```go
func div2(num1 int, num2 int) (result int, remainder int, err error) {
	if num2 == 0 {
		err = errors.New("cant divide by 0")
		return result,remainder,err
	}
	result,remainder = num1 / num2, num1 % num2
	return result, remainder, nil
}
```

可以对返回值命名，但是没必要

```go
func div2(num1 int, num2 int) (result int, remainder int, err error) {
    result,remainder = 20,30
	if num2 == 0 {
		err = errors.New("cant divide by 0")
		return result,remainder,err
	}
	result,remainder = num1 / num2, num1 % num2
	return result, remainder, nil
}

div2(5,2) //2,1
```

#### 4.1.6 永远不要使用空返回值

如果命名了返回值，可以直接return而不指定返回值。

```go
func div2(num1 int, num2 int) (result int, remainder int, err error) {
	if num2 == 0 {
		err = errors.New("cant divide by 0")
		return result,remainder,err
	}
	result,remainder = num1 / num2, num1 % num2
	return 
}
```

> 空返回值，会使得数据流更难理解，不要用

### 4.2 函数即值

将函数作为值

```go
func add(i int, j int) int { return i + j }

func sub(i int, j int) int { return i - j }

func mul(i int, j int) int { return i * j }

func div(i int, j int) int { return i / j }

var opMap = map[string]func(int, int) int{
	"+": add,
	"-": sub,
	"*": mul,
	"/": div,
}

func main() {
	expressions := [][]string{
		[]string{"2", "+", "3"},
		[]string{"2", "-", "3"},
		[]string{"2", "*", "3"},
		[]string{"2", "/", "3"},
		[]string{"2", "%", "3"},
		[]string{"two", "+", "three"},
		[]string{"5"},
	}

    for _, expression := range expressions {
            if len(expression) != 3 {
                fmt.Println("invalid expression:", expression)
                continue
            }
            p1, err := strconv.Atoi(expression[0])	//将string转为int
            if err != nil {
                fmt.Println(err)
                continue
            }
            op := expression[1]
            opFunc, ok := opMap[op]
            if !ok {
                fmt.Println("unsupported operator:", op)
                continue
            }
            p2, err := strconv.Atoi(expression[2])
            if err != nil {
                fmt.Println(err)
                continue
            }
            result := opFunc(p1,p2)
            fmt.Println(result)
        }
	}
```

> 这段代码16行都用于错误简称和数据验证，错误处理是走向专业的第一步

#### 4.2.1 声明函数类型

type关键字既可以第一struct也可以定义函数类型

```go
type opFuncType func(int,int) int
```

定义新的函数类型后可以重新将opMap声明为

```go
var opMap = map[string]opFuncType int{
	"+": add,
	"-": sub,
	"*": mul,
	"/": div,
}
```

#### 4.2.2 匿名函数

匿名函数没有名字，也不必赋值给变量，可以以inline的方式将其立即执行

```go
func main() {
	for i := 0;i < 5;i++{
		func(j int){
			fmt.Println("printing",j,"from inside of an anonymous function")
		}(i)
	}
}
```

### 4.3 闭包

**闭包是指在函数内部声明的函数能够访问且修改在外部函数声明的变量**

#### 4.3.1 函数即参数

排序

```go
type Person struct {
	FirstName string
	LastName  string
	Age       int
}

func main() {
	people := []Person{
		{"Pat", "Patterson", 37},
		{"Tracy", "Bobbert", 23},
		{"Fred", "Fredson", 18},
	}
	fmt.Println(people)
	sort.Slice(people, func(i, j int) bool {
		return people[i].LastName > people[j].LastName //为false交换
	})
	fmt.Println(people)
}
```

#### 4.3.2 函数作为返回值

```go
func makeMult(base int) func(int) int {
	return func(factor int) int {
		return base * factor
	}
}

func main() {
	twoBase := makeMult(2)
	threeBase := makeMult(3)
	for i := 0; i < 3; i++ {
		fmt.Println(twoBase(i), threeBase(i))
	}
}
```

### 4.4 defer

在Go中使用defer关键字将清理代码附加到函数中即可完成清理

```go
func main() {
	if len(os.Args)  < 2{
		log.Fatal("no file specified")
	}
	f,err := os.Open(os.Args[1])
	if err != nil {
		log.Fatal(err)
	}
	defer f.Close()
	...
}
```

> Go语言可以在一个函数中defer多个闭包。他们采用后进先出的顺序，即最后一个注册的defer函数先运行。defer闭包中的代码在return语句之后运行。

go中常见的一个模式是，分配资源的函数同时返回一个清理资源的闭包。

```go
func getFile(name string)(*os.File, func(), error){
	file,err := os.Open(name)
	if err != nil {
		return nil,nil,err
	}
	return file,func(){
		file.Close()
	},err
}

func main() {
	f, closer, err := getFile(os.Args[1])
	if err != nil {
		log.Fatal(err)
	}
	defer closer()
}
```

> 注意，别忘记指定闭包的时候加上圆括号

### 4.5 函数的值调用

Go是一种值传递语言，这意味着当你提供一个变量给函数当参数时，Go总是复制该变量的值。基础类型和包含基础类型的结构体变量都适用。

但是映射和切片有所不同

```go
func modMap(m map[int]string) {
	m[2] = "hello"
	m[3] = "goodbye"
	delete(m, 1)
}
func modSlice(s []int) {
	for k, v := range s {
		s[k] = v * 2
	}
	s = append(s, 10)
}

func main() {
	m := map[int]string{
		1: "first",
		2: "second",
	}
	modMap(m)
	fmt.Println(m)

	s := []int{1, 2, 3}
	modSlice(s)
	fmt.Println(s)
}

/*
    map[2:hello 3:goodbye]
    [2 4 6]
*/
```

> 由于映射和切片都是有指针实现，所以可以改变元素。
>
> 但是切片只能修改元素，不能增加长度



## 5.指针

### 5.1 指针快速入门

```go
var x = int 32 = 10
var y bool = true
pointerX := &x	//取地址
pointerY := &y

var pointerZ *string	//声明这是指针类型
```

> 指针的零值是nil，切片、映射、函数的零值都是nil

**&是地址运算符，返回存储该值的内存位置的地址**

```go
x := "hello"
pointerToX := &x
```

***是间接寻址运算符。它位于指针类型变量之前，并返回所指向的值。这称为解引用：**

```go
func main() {
	x := 10
	pointerToX := &x
	fmt.Println(pointerToX)
	fmt.Println(*pointerToX)
	z := 5 + *pointerToX
	fmt.Println(z)
}

//0xc0000180d8
//10
//15
```

**内置函数new可以创建一个指针变量。他返回一个指向所提供类型的零值实例的指针**

```go
var x = new(int)
fmt.Println(x == nil) //false
fmt.Println(*x) //0
```

> 无法在基本字面量（数字，布尔，字符串）或常量前使用&，因为他们没有内存地址，只在编译的时候存在

如果有一个结构的字段是指向基本类型的指针，就不能直接向该字段赋值一个字面量

```
type person struct{
	FirstName string
	MiddleName *string
	LastName string
}

p := person{
	FirstName: "Pat"
	MiddleName: "Perry",	//编译错误 即使是&"Perry"也是错误
	LastName: "Peterson"
}
```

有两种方法可以解决：第一种是引入一个变量来保存常量，第二种是写一个辅助函数，接受一个布尔型、数字型或字符串型，并返回一个指向该类型的指针

```go
func stringp(s string) *string{
    return &s
}

p := person{
	FirstName: "Pat"
    MiddleName: stringp("Perry"),	
	LastName: "Peterson"
}
```

**使用辅助函数将常量变为指针**

### 5.2 不畏惧指针

**和Java一样，Go语言也是值传递，Java的基本类型是参数复制的是值，对象参数复制的是这个实例的地址，即指针，Go语言也一样。**

**区别在于，Go可以选择对基本类型和结构体使用指针或值**

### 5.3 指针即可变的参数

当一个指针被传递给一个函数时，该函数会得到指针的一个副本。但由于指针仍然指向原始数据，所以原始数据可以被调用的函数修改

这其中有几个含义

第一个，当把一个nil指针传递给一个函数式，不能讲这个值改变为非nil的。如果已经对该指针赋值，只能重新赋值。

```go
func failedUpdate(g *int){
    x := 10
    g = &x
}

func main(){
    var f *int //f is nil
    failedUpdate(f)
    fmt.Println(f)//prints nil
}
```

第二个，如果希望对指针参数进行的修改在退出函数时依然有效，则必须对指针解引用并设置该值

```go
func failedUpdate(px *int){
    x2 := 20
    px = &x2
}

func update(px *int){
    *px = 20
}

func main(){
    x := 10
    failedUpdate(&x)
    fmt.Println(x)	//10
    update(&x)
    fmt.Println(x)	//20
}
```

### 5.4 指针传递可提高性能

对于所有大小的数据来说，将指针传入函数的时间是恒定的，大约是1ns。

将值传入函数需要更多的时间，10MB左右的数据，需要大约1ms

对于小于1MB的数据，使用指针会比值慢。例如，100B的数据结构返回值需要10ns左右，而返回指针需要30ns。一旦数据结构大于1MB，性能优势就会反转。

### 5.5 映射与切片的区别

映射：传入函数的映射所做的任何修改都会对传入的原始变量生效。

切片：任何对切片内容的改变都会对原始变量产生影响，但使用append来给切片增加新元素并不会对原始变量产生影响，即使切片容量大于他的长度。

这是因为一个切片被实现为一个有三个字段的结构：表示长度的int字段，表示容量的int字段以及一个指向内存块的指针。

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230206144809763.png" alt="image-20230206144809763" style="zoom: 25%;" />

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230206144843287.png" alt="image-20230206144843287" style="zoom:25%;" />

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230206144940418.png" alt="image-20230206144940418" style="zoom:25%;" />

对长度和容量的改变不会反映在原切片上，因为它们只在副本中。改变容量意味着指向了一个新的更大的内存块。

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230206145126882.png" alt="image-20230206145126882" style="zoom:25%;" />

如果切片副本的元素增加，并且有足够的容量不分配新的切片，那么副本的长度就会改变，新的值会被存储在共享的内存块中，但是源切片看不到这些值。

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230206145306270.png" alt="image-20230206145306270" style="zoom:25%;" />

### 5.6 切片用作缓冲区

为了避免不必要的内存分配（节省垃圾回收时间），应该一次性创建一个字节切片，将其作为缓冲区

```go
	file,err := os.Open("fileName")
	if err != nil {
		return err
	}
	defer file.Close()
	data := make([]byte, 100)
	for{
		count,err := file.Read(data)
		if err != nil {
			return err
		}
		if count == 0 {
			return nil
		}
		process(data[:count])
	}
```

> 当把切片传递给一个函数时，不能改变它的长度或者容量，但可以改变其内容，直至他的长度

## 6.类型、方法与接口

### 6.1 Go语言的类型

定义一个结构体

```go
type Person struct {
	FirstName string
	LastName  string
	Age       int
}
```

上面声明了一个用Person命名的自定义类型，它以结构体字面量作为底层类型。除了结构体字面量之外，还可以用任何基本类型或复合字面量来定义具体类型。

```go
type Score int 
type Converter func(string)Score
type TeamScores map[string]Score
```

Go允许在任何代码块级（从包代码块开始）声明类型。但是只有在其作用域范围内才能访问该类型。除非从包级代码块导出

### 6.2 方法

Go支持在自定义类型中定义方法。一个类型的方法是在包代码块级别定义的

```go
type Person struct {
	FirstName string
	LastName  string
	Age       int
}

func (p Person) String string {
    return fmt.Sprintf("%s %s,age %d",p.FirstName,p.LastName,p.Age)
}
```

**方法的声明比函数多了一个额外的部分：接受者。它处于func和方法名之间。**

> 方法与函数都不能被重载。可以对不同类型使用相同方法名，但不能对同一类型中的两个不同方法使用相同方法名。

#### 6.2.1 指针接受者和值接收者

```go
type Counter struct {
	total int
	lastUpdate time.Time
}

func (c *Counter) Increment(){
	c.total++
	c.lastUpdate = time.Now()
}

func (c Counter) String() string {
	return fmt.Sprintf("total: %d, last update: %v",c.total,c.lastUpdate)
}
```

- 如果方法修改了接受者，那必须使用指针接收者
- 如果方法需要处理nil实例，那么必须使用指针接收者
- 如果方法不修改接收者的值，则可以使用接收者

```go
func main() {
	var c Counter
	fmt.Println(c.String())
	c.Increment()
	fmt.Println(c.String())
}

//打印
total: 0, last update: 0001-01-01 00:00:00 +0000 UTC
total: 1, last update: 2023-02-07 11:13:00.7178739 +0800 CST m=+0.004355401
```

**上面的代码中c是一个值类型，当对一个值类型的局部变量使用指针接收者方法时，go会将其自动转换为指针类型**，**这种情况下，c.Increment()被转换为(&c).Increment()**

值得注意的是，向函数传递参数的规则仍然适用。

```go
func main() {
	var c Counter
	doUpdateWrong(c)
	fmt.Println("in main:", c.String())
	doUpdateRight(&c)
	fmt.Println("in main:", c.String())
}
func doUpdateWrong(c Counter) {
	c.Increment()
	fmt.Println("in doUpdateWrong:", c.String())
}

func doUpdateRight(c *Counter) {
	c.Increment()
	fmt.Println("in doUpdateRight:", c.String())
}

//打印
in doUpdateWrong: total: 1, last update: 2023-02-07 11:19:39.3725314 +0800 CST m=+0.004999301
in main: total: 0, last update: 0001-01-01 00:00:00 +0000 UTC                                
in doUpdateRight: total: 1, last update: 2023-02-07 11:19:39.3991278 +0800 CST m=+0.031595701
in main: total: 1, last update: 2023-02-07 11:19:39.3991278 +0800 CST m=+0.031595701         
```

> 不要为Go编写getter和setter方法，除非你需要它们来满足一个接口。Go鼓励直接访问一个字段来更新值，而对于复杂的业务逻辑来说需要使用方法。

#### 6.2.2 在方法中处理nil实例

在一个nil实例上调用一个方法时会发生什么？

1. 如果方法使用值接收者，则会得到一个panic，因为指针没有指向任何值
2. 如果方法使用指针接收者，则可以在方法中检查实例为nil的情况（方法的参数复制这个nil指针的值给新的变量，此时参数相当被声明为nil）

``` 
type IntTree struct {
    val int
    left,right *IntTree
}

func (it *IntTree) Insert(val int) *IntTree {
	if it == nil {
		return &IntTree{val:val}
	}
	...
}
```

#### 6.2.3 方法即函数

当有一个函数类型的变量或参数时，你可用方法来代替函数

```go
type Adder struct {
    start int
}

func (a Adder) AddTo(val int) int {
    return a.start + val
}

//创建实例 调用方法
myAdder := Adder{start:10}
fmt.Println(myAdder,AddTo(5))	//10

f1 := myAdder.AddTo	//f1类型是func(int) int
fmt.Println(f1(10)) //20
```

也可以从类型本身创建一个函数。这称为方法表达式，第一个参数时方法的接收者。函数签名是func(Adder,int)int

```go
f2 := Adder.AddTo
fmt.Println(f2(myAdder,15)) //25
```

#### 6.2.4 方法与函数的区别

区别在于是否依赖于其他数据。如果依赖其他数据，这些数据就应该存储在一个结构中，并且该逻辑一个被实现为一个方法。如果逻辑只依赖于输入参数，那么他应该是一个函数。

#### 6.2.5 类型声明不是一种继承

前面定义过两个类型，可以根据另一个自定义类型来声明一个自定义类型：

```go
type HighScore Score
type Employee Person
```

基于一个类型来声明另一个类型看起来有点像继承，但这不是继承。这两个类型有系统的底层类型，仅此而已。

#### 6.2.6 iota可用于枚举

Go中没有枚举类型，Go语言在用iota为一组常量赋一个累加的值

```go
const (
		Uncategorized int = iota
		Personal
		Spam
		Social
		Advertisements
	)
```

const代码块声明一组常量，第一个常量有指定类型，赋值为iota，之后每一行都没有指定类型的值。代码块中后续的常量使用相同的类型，编译器会对并在每一行累加iota的值。第一个赋值为0，第二个为1，依次递增。

使用iota前提是值不是很重要，只是用常量名来区分。如果值很重要，那需要显式地指定值。

```go
const (
		Uncategorized string = "12345"
		Personal
		Spam
		Social
		Advertisements
	)
```

### 6.3 使用内嵌进行组合

软件工程建议“使用对象组合优于类继承”。Go语言没有继承性，所以Go语言鼓励使用组合方式重用代码。

```go
type Employee struct {
	Name string
	ID string
}

func (e Employee) Description() string  {
	return fmt.Sprintf("%s (%s)",e.Name,e.ID)
}

type Manager struct {
	Employee
	Reports []Employee
}

func (m Manager) FindNewEmployees() []Employee{
	//code...
}
```

Employee成为一个内嵌字段，在内嵌字段上声明的任何字段或方法都会被提升到所包含的结构体上，并可以直接对其进行调用，以下代码有效

```go
m := Manager{
    Employee: Employee{
        Name:	"Bob Bobson",
        ID:		"12345",
    },
    Reports: []Employee{},
}

fmt.Println(m.ID)
fmt.Println(m.Description())
```

> 我们可以在一个结构体在内嵌任何类型，而不仅仅是另一个结构体。这都可以将内嵌类型上的方法提升到包含它的结构体上。

如果包含的结构体字段或方法与内嵌的字段或方法同名，则需要使用内嵌字段类型来应用被覆盖的字段或方法

```go
type Inner struct {
    X int
}

type Outer struct {
    Inner
    X int
}

o := Outer{
    Inner:Inner{
        X: 10,
    },
    X: 20,
}

fmt.Println(o.X)
fmt.Println(o.Inner.X)
```

### 6.4 接口

接口定义，通常一“er”结尾命名，io.Reader、io.Closer、http.Handler

```go
type Stringer interface {
    String() string
}
```

Go语言接口的实现是隐式的实现，如果具体类型的方法集包含接口方法集中的所有方法，那么该具体类型就实现了该接口。

> 这意味着该具体类型可以被赋值给一个被声明为该接口类型的变量或字段。

对于一个满足接口定义的类型来说，指定不属于接口的额外方法是完全可以的。有些调用可能无需关注这些方法，但有些调用需要关注。

例如，io.File类型也满足io.Writer接口，但是如果你的代码只关心从文件中读取数据，那么就使用io.Reader接口来引用文件实例，忽略其他方法。

### 6.5 内嵌与接口

Go可以将一个接口内嵌到另一个接口中

```go
type Reader interface {
    Read(p []byte) (n int, err Error)
}

type Closer interface {
    Close() error
}

type ReadCloser interface {
    Reader
    Closer
}
```

### 6.6 接口与nil

Go中用nil来表示接口实例的零值，但这不像对具体类型那样简单。

一个接口被认为是nil，则其类型和值都必须是nil。

```go
var s *string
fmt.Println(s == nil)	//true
var i interface{}
fmt.Println(i == nil)	//true
i = s
fmt.Println(i == nil)	//false
```

在Go运行时，接口被实现为一对指针，一个指向底层类型，一个指向基础值。只要类型是非空的那么接口就是非空的，因为你不可能指向一个没有类型的变量。如果值的指针是非空的，那么类型的指针也总是非空的。

### 6.7 空接口

在静态语言中，有时需要一种方法来说明一个变量可以存储任何类型的值。这就是Go语言中interface{}的作用。

```go
var i interface{}
i = 20
i = "hello"
i = struct {
    FirstName string
    LastName string
}{"Fred","Fredson"}
```

interface{}并不是特例语法。空接口类型只是声明了该变量可以存储任何实现任意方法的类型的值。

个人理解就是，interface{}这个接口没有声明方法，空的，相当于所有类型都实现了这个接口，所有interface{}类型的变量可以接受所有具体类型

空接口常见的一个语法是当从外部读取一些数据时作为不确定数据的占位符

```go
data := map[string]interface{}{}
```

另一个常用的用途是在自定义数据结构中存储值

```go
type LinkedList struct {
    Value interface{}
    Next *LinkedList
}
```

### 6.8 类型断言与type-switch

Go提供了两种方法来判断一个接口类型是否有特定的具体类型，或者具体类型是否实现了另一个接口。

1.类型断言。

```go
value, ok := x.(T)
```

其中，x 表示一个接口的类型，T 表示一个具体的类型（也可为接口类型）。

该断言表达式会返回 x 的值（也就是 value）和一个布尔值（也就是 ok），可根据该布尔值判断 x 是否为 T 类型：

- 如果 T 是具体某个类型，类型断言会检查 x 的动态类型是否等于具体类型 T。如果检查成功，类型断言返回的结果是 x 的动态值，其类型是 T。
- 如果 T 是接口类型，类型断言会检查 x 的动态类型是否满足 T。如果检查成功，x 的动态值不会被提取，返回值是一个类型为 T 的接口值。
- 无论 T 是什么类型，如果 x 是 nil 接口值，类型断言都会失败。

```go
func main() {
    var x interface{}
    x = 10
    value, ok := x.(int)
    fmt.Print(value, ",", ok)	//10,true
}

func main() {
    var x interface{}
    x = "Hello"
    value := x.(int)
    fmt.Println(value) //panic: interface conversion: interface {} is string, not int
}
```

2.type-switch

```go
type animal interface {
	MakeNoise()
}
type Dog struct{}

func (d Dog) MakeNoise() {
	fmt.Println("汪汪汪")
}

type Cat struct{}

func (c Cat) MakeNoise() {
	fmt.Println("喵喵喵")
}

func typeAssert(a animal) {
	// a为 animal接口实现类，但是我们想实现诸如：对不同的实现类又不一样的操作这个就需要根据不同的实现类来进行不同的操作
	switch a.(type) {
	case Dog:
		fmt.Println("the is a dog")
		a.MakeNoise()
	case Cat:
		fmt.Println("this is a cat")
		a.MakeNoise()
	}
}

func main() {
	smallDog := Dog{} 
	typeAssert(smallDog)	//the is a dog 汪汪汪
}
```

### 6.9 函数类型实现接口

Go允许在任何自定义类型上添加方法，包括自定义的函数类型，这允许函数来实现接口。例如HTTP处理程序

```go
type Handler interface {
    ServeHTTP(http.ResponseWriter,*http.Request)
}

//自定义函数类型
type HandlerFunc func(http.ResponseWriter,*http.Request)
//实现接口
func (f HandlerFunc) ServeHTTP(w http.ResponseWriter,r *http.Request){
    f(w,r)
}
```

函数的声明不能直接实现接口，需要将函数定义为类型后，使用类型实现结构体，当类型方法被调用时，还需要调用函数本体

```go
// 函数定义为类型
type FuncCaller func(interface{})
// 实现Invoker的Call
func (f FuncCaller) Call(p interface{}) {
    // 调用f()函数本体
    f(p)
}
```

上面代码只是定义了函数类型，需要函数本身进行逻辑处理，FuncCaller 无须被实例化，只需要将函数转换为 FuncCaller 类型即可，函数来源可以是命名函数、匿名函数或闭包，

```go
// 将匿名函数转为FuncCaller类型, 再赋值给接口
var invoker = FuncCaller(func(v interface{}) {
    fmt.Println("from function", v)
})

// 使用接口调用FuncCaller.Call, 内部会调用函数本体
invoker.Call("hello")
```



## 7.错误

### 7.1 如何处理错误

当一个函数正确执行之后，错误对应的返回值就是nil，而如果发生了任何问题，就会返回一个错误值。调用函数之后将错误值与nil进行比较，要么处理错误，要么工具这个错误返回一个自定义的错误。**Go语言中，始终用if表达式判断错误变量的值是否为nil。**

```go
func div2(num1 int, num2 int) (result int, remainder int, err error) {
	if num2 == 0 {
		err = errors.New("cant divide by 0")
		return result, remainder, err
	}
	result, remainder = num1/num2, num1%num2
	return result, remainder, nil
}
```

error是一个内置接口，并且只定义了一个函数

```go
type error interface {
    Error() string
}
```

### 7.2 使用字符串创建简单错误

Go的标准库提供了两种方式使用字符串创建错误

第一种，errors.New

```go
func doubleEven(i int) (int,error) {
    if i % 2 != 0 {
        return 0, errors.New("only even numbers are processed")
    }
    return i * 2,nil
}
```

第二种，fmt.Errorf

```go
func doubleEven(i int)(int,error){
     if i % 2 != 0 {
        return 0, fmt.Errorf("%d isnt an even number",i)
    }
    return i * 2,nil
}
```

### 7.3 哨兵错误

一个哨兵错误是一个命名的错误值。通常这些错误是导出的标识符，因为我们的想法是，使用你的API的用户可以将错误与这个给定值进行比较。

按照约定，他们的名字都以Err开头，除了io.EOF这一例外情况

```go
var ErrFoo = errors.New("this is fine")
```

一旦定义了哨兵错误，这个错误就作为公共API的一部分了，因此要确保之后发布的版本都能向后兼容这个值。

我们使用 == 来判断调用函数返回的错误是不是对应的哨兵错误

### 7.4 错误是值

自定义错误

```go
// 定义枚举
type Status int
const (
	InvalidLogin Status = iota + 1
	NotFound
)

// 定义一个实现error接口的结构体
type StatusErr struct {
	Status  Status
	Message string
}

func (se StatusErr) Error() string {
	return se.Message
}

// 定义一个函数，可以展现更多错误细节
func LoginAndGetData(uid, pwd, file string) ([]byte, error) {
	err := login(uid, pwd)
	if err != nil {
		return nil, StatusErr{
			Status:  InvalidLogin,
			Message: fmt.Sprintf("invalid credentials for user %s", uid),
		}
	}
	data, err := getData(file)
	if err != nil {
		return nil, StatusErr{
			Status:  NotFound,
			Message: fmt.Sprintf("file %s not found", file),
		}
	}
	return data, nil
}
```

即使使用自定义的错误类型，也始终要用error作为返回错误结果的类型。这允许你从函数返回不同类型的错误。

不要声明一个类型为自定义错误类型的变量，并将这个变量返回，看例子

```go
func GenerateError(flag bool) error {
	var genErr StatusErr
	if flag {
		genErr = StatusErr{
			Status: NotFound,
		}
	}
	return genErr
}

func main() {
	err := GenerateError(true)
	fmt.Println(err != nil)	
	err = GenerateError(false)
	fmt.Println(err != nil)
}

//true true
```

为什么第二个传入false也是true呢？因为返回的是StatusErr类型的值，被error类型的返回参数接收，6.6小结说过，只有实际类型和值都为nil，返回的接口类型的结果变量才会是nil。如何解决呢？

方式一：函数成功执行返回nil

```go
func GenerateError(flag bool) error {
	//var genErr StatusErr	
	if flag {
		return StatusErr{
			Status: NotFound,
		}
	}
	return nil
}

//我觉得也可以这样写，但是很傻逼
func GenerateError(flag bool) error {
	var genErr StatusErr
	if flag {
		genErr = StatusErr{
			Status: NotFound,
		}
		return genErr
	}
	return nil
}
```

方式二：返回值的类型设置为error

```go
func GenerateError(flag bool) error {
	var genErr error
	if flag {
		genErr = StatusErr{
			Status: NotFound,
		}
	}
	return genErr
}
```

### 7.5 包装错误

用fmt.Errorf函数包装，占位符用w%

用errors.Unwrap函数解包

```go
func fileChecker(name string) error {
	f,err := os.Open(name)
	if err != nil {
		return fmt.Errorf("in fileChecker %w",err)
	}
	f.Close()
	return nil
}

func main() {
	err := fileChecker("not_here.txt")
	if err != nil {
		fmt.Println(err)
		if wrappedErr := errors.Unwrap(err); wrappedErr != nil {
			fmt.Println(wrappedErr)
		}
	}
}
```

### 7.6 Is和As

**errors.Is：**判断被包装的error是否包含指定错误。

```go
var BaseErr = errors.New("the underlying base error") //定义哨兵错误

func main() {
   err1 := fmt.Errorf("wrap base: %w", BaseErr) //包装哨兵错误
   err2 := fmt.Errorf("wrap err1: %w", err1) //再包装一次
   println(err2 == BaseErr) // false
   if !errors.Is(err2, BaseErr) { 
      panic("err2 is not BaseErr")
   }
   println("err2 is BaseErr")
}
/*
  打印结果：
  false
  err2 is BaseErr
*/
```

**errors.As：**提取指定类型的错误，判断包装的 error 链中，某一个 error 的类型是否与 target 相同，并提取第一个符合目标类型的错误的值，将其赋值给 target。

```go
type TypicalErr struct {
   e string
}

func (t TypicalErr) Error() string {
   return t.e
}

func main() {
   err := TypicalErr{"typical error"}
   err1 := fmt.Errorf("wrap err: %w", err)
   err2 := fmt.Errorf("wrap err1: %w", err1)
   var e TypicalErr
   if !errors.As(err2, &e) {
      panic("TypicalErr is not on the chain of err2")
   }
   println("TypicalErr is on the chain of err2")
   println(err == e)
}
/*
  打印结果：
  TypicalErr is on the chain of err2
  true
*/
```

### 7.7 使用defer包装错误

没使用defer之前

```go
func DoSomeThings(val1 int, val2 string)(string,error){
	val3,err := doThing1(val1)
    if err != nil {
        return "",fmt.Errorf("in DoSomeThings: %w",err)
	}
    val4,err := doThing2(val2)
    if err != nil {
        return "",fmt.Errorf("in DoSomeThings: %w",err)
	}
    result,err := doThing3(val3,val4)
    if err != nil {
        return "",fmt.Errorf("in DoSomeThings: %w",err)
	}
    return result,nil
}
```

用了defer之后

```go
func DoSomeThings(val1 int, val2 string)(_ string,error){
    defer func(){
         if err != nil {
        	return "",fmt.Errorf("in DoSomeThings: %w",err)
		}
    }()
	val3,err := doThing1(val1)
    if err != nil {
        return "",err
    }
    val4,err := doThing2(val2)
    if err != nil {
        return "",err
	}
    return doThing3(val3,val4)
}
```

### 7.8 panic和recover

引起panic的原因可能是程序错误（比如读取切片的时候会越界），也可能是环境问题（比如内存不足）。一旦发生panic，当前运行的函数就会立即退出，并且函数中所包含的所有defer函数都会执行。当前defer都执行结束后，该函数的外层调用函数中的defer也会执行，知道达到主函数。接着程序会完全退出，打印错误以及响应的堆栈跟踪信息。

可以主动触发panic

```go
func doPanic(msg string){
    panic(msg)
}
func main(){
    doPanic(os.Args[0])
}
```

可以在defer中调用recover函数来判断panic是否发生，从而阻止程序退出

```go
func div(i int){
    derfer func(){
        if v := recover();v!=nil{
            fmt.Println(v)
        }
    }()
    fmt.Println(60/i)
}

func main(){
    for _,val := range []int{1,2,0,6}{
        div(val)
    }
}

/*
    60
    30
    runtime error:integer divide by zero
    10
*/
```

> recover必须在defer闭包中调用，因为当panic发生时，只有对应的defer函数才会执行



## 8.模块、包和导入

### 8.1 仓库、模块和包



