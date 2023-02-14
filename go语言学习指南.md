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
x := make([]int,5,10)
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

Employee成为一个内嵌字段，**在内嵌字段上声明的任何字段或方法都会被提升到所包含的结构体上，并可以直接对其进行调用**，以下代码有效

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

仓库是一个通用概念，项目的源代码通过版本控制系统存储在仓库之中。模块是Go库或者应用程序的根目录，存储在仓库之中。模块包含一个或多个包，用来组织模块结构。

在使用第三方库之前，确保项目已经被声明成模块。每个模块都有一个全局的标识符。

### 8.2 go.mod

通过在项目的根目录定义一个有效的go.mod文件可以将Go代码组织为一个模块。执行以下命令创建go.mod文件

```go
go mod init MODULE_PATH
```

MODULE_PATH是用来标识这个模块的唯一每次。模块的路径是区分大小写的，为了减少混淆，不要在模块路径中使用大写字母

```go
go mod init tyy

//go.mod
module tyy

go 1.19

require(
	github.com/shopspring/decimal v1.2.0
)
```

如果没有依赖其他模块，require会被省略。

这个文件有两个可选部分：replace部分用来覆盖依赖模块的路径，exclude部分指定避免使用某些特定版本的模块

### 8.3 创建包

#### 8.3.1 import和export

**Go的import声明可以让我们访问其他包所导出的常量、变量、函数以及类型。包的导出标识符（标识符时变量、常量、类型、函数、方法或者结构体中字段等的名称）只有通过import才能被其他包访问**

> 标识符大写就标识是导出。相应的，如果一个标识符的名字以小写或者下划线开头，那么它只能在当前包中使用

#### 8.3.2 包的创建与访问

pakage创建包，import导入包

```go
pakage math

import(
	"fmt"
)
```

> 如果导入了一个包但并不使用任何它所导出的标识符，就会产生编译时错误。

通常情况下，最好让包名和包含这个包的目录名一致，如果两者不一致，就不容易找到对应的包名

#### 8.3.3 包的命名

包名应该有意义。

不应该使用util作为包名。比如，有两个函数ExtractNames用来从字符串中提取名字，FormatNames用来格式化名字。不要在util包中创建这两个函数，引用的时候会是：

```go
util.ExtractNames
util.FormatNames
```

更好的方式是，在extract包中创建名为Names的函数，在format包中也创建名为Names的函数，调用就是

```go
extrct.Names
format.Names
```

同时也要避免在包中创建函数时，重复使用包名相同或者相似的名字。例如，不要在 extract包中定义叫做ExtractNames函数。也有例外，例如标准库sort包中的Sort函数，context包中的Context接口。

#### 8.3.4 覆盖包名

有时候会出现导入的包命名冲突的情况。比如标准库生成随机数的包，一个是加密安全的（crypto/rand），另一个不是（math/rand）。此时需要对一个包重命名

```go
import(
	crand "crypto/rand"
    "math/rand"
)
//使用
crand.Rrand
rand.New()
```

使用.作为包名会将包中所有可导出的标识符放入当前包的命名空间，此时不再需要使用前缀方式应用他们。

#### 8.3.5 包注释和godoc

Go有专门的注释格式，使用该格式可以自动将注释转换为文档。这称为godoc格式，非常简单易用。只要遵循一下约定即可：

- 直接在代码前写注释，中间不要有任何空行
- 注释以//开头，后跟项目名
- 如果注释有多段，每个段落由空注释隔开
- 对有格式的注释内容需要使用缩进

包级注释

```go
//Pakage  content...
pakage money
```

结构体注释，以结构体名开头

```go
//Money content...
//and content...
type Money struct {
    ...
}
```

函数注释

```go
//Convert content...
//
//content...
//content...
func convert()int{
    ...
}
```

使用go doc或go doc PAKAGE_NAME查看godocs

#### 8.3.6 内部包

有时候我们希望模块内部的包之间能共享函数、类型、常量，但是不希望让他们成为公共API的一部分，可以使用internal包名。

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230209095508100.png" alt="image-20230209095508100" style="zoom:25%;" />

#### 8.3.7 尽量避免使用init函数

init函数能让包在不调用任何方法的情况下设置初始状态。

有一些包例如数据库驱动程序用init函数注册数据库驱动程序，但我们并不需要使用这个包的其他标识符，只用init函数。Go不允许导入包却不使用，为了解决这个问题，Go允许使用下划线组委导入包的名称。这样的包会触发相应的init函数，同时也保证了不能访问导入包中的任意标识符。

```go
import(
	_ "github.com/lib/mysql"
)
```

Go允许第一多个init函数，但是在一个包中应该只声明一个init函数。

#### 8.3.8 循环依赖

Go不允许包与包之间存在循环依赖。这意味着如果包A直接或简介导入了包B，那么包B就不能直接或间接导入包A。

解决方法：

1. 如果两个包互相依赖，那么它们可以合并为一个包
2. 如果希望两个包保持独立，那么可以将导致循环依赖的部分移到其中一个包中，或者直接移道一个新包中

#### 8.3.9 优雅地重命名和重组API

使用一个模块一段时间后，你可能觉得它提供的API并不完美，并且想将一些导出的标识符重命名，这时为了避免破坏向后的兼容性，不要移除原有的标识符而是应该使用别名。

对于函数或者方法，很简单，只需要声明一个新的函数或方法来调用原来的函数即可。

重命名或者移动一个导出的类型必须使用别名

```go
type Foo struct {
	x int
    S string
}

func (f Foo) Hello() string {
    ...
}
func (f Foo) goodbye() string {
    ...
}

type Bar Foo //别名
```

以上别名由type关键字、别名、等号以及原类型别名构成。别名具有原类型的字段和方法。

这个别名不经过类型转换就可以分配给用原类型的变量：

```go
func MakeBar() Bar{
    bar := Bar{
        a:20
        B:"hello"
    }
    var f Foo = bar
    return var
}
```

> 别名只是类型的另外一个名字。如果想为这个类型添加新字段或者方法，则需要在原类型上添加

不管原类型是否在一个包里，都可以添加别名。不过对于其他包中的变量，使用别名有一个限制：不能通过别名使用原类型未导出的方法和字段。

有两种类型的可导出标识符不能有别名。第一种是包级变量，第二种是结构体中的字段。一旦确定了导出的结构体字段名，就无法为其创建别名。

### 8.4 模块

#### 8.4.1 导入第三方代码

```go
import(
	"github/shopspring/decimal"
)

//运行go build 
//go.mod
module tyy

go 1.19

require(
    github.com/learning-g-gbook/formatter v0.0.0-20230320230342453496-5sjdasidn898asojd //这里没有版本标记，Go生成了一个伪版本号
	github.com/shopspring/decimal v1.2.0
)
```

与此同时，创建了一个go.sum文件

<img src="C:\Users\TYY\AppData\Roaming\Typora\typora-user-images\image-20230209102751374.png" alt="image-20230209102751374" style="zoom:25%;" />

#### 8.4.2 版本

定义版本的好处是可以给一个模块指定使用早期版本

```go
//go list 查看这个包有哪些版本可用
$ go list -versions github/.....

//go get 命令可以操作模块 更新依赖的版本
$ go get github/...
```

#### 8.4.3 选择最小版本

Go中的模块系统遵循选择最小版本（最新版本）原则。

例如，一个模块依赖模块A、B、C，这三个模块又依赖模块D。模块A的go.mod显示它的依赖版本v1.1.0，模块B依赖v1.2.0，模块C则依赖v1.2.3。Go智慧到如模块D一次并选择版本v1.2.3。

那么如果模块A在v1.1.0可以工作，v1.2.3不能工作，咋办？Go建议联系模块的作者修复兼容性问题。

#### 8.4.4 更新到兼容版本

如何显式地更新一个依赖项呢？

假设用的是simpletax模块的v1.1.0版本，现在它又有三个新版本

- v1.1.1修复了v1.1.0的问题，不增加新功能，属于补丁
- v1.2.0添加了新功能
- v1.2.1修复了1.2.0版本的问题，属于补丁

```go
go get github/learning-go-book/simpletax@v1.1.0 更新到版本v1.1.0

go get -u=patch github/learning-go-book/simpletax 更新到版本v1.1.1，这个命令更新到当前版本的补丁版本

go get -u patch github/learning-go-book/simpletax 版本升级到v1.2.1
```

#### 8.4.5 更新到不兼容版本

假设simpletax已经有版本v2.0.0，这个版本和之前的版本API不一样

为了处理不兼容行，有两个规则

1. 模块的主版本号必须是递增的
2. 除了1和0这两个主版本号，模块的路径必须以vN结束，这里N是主版本号

```go
github/learning-go-book/simpletax/v2

//go.mod
module tyy

go 1.19

require(
	github/learning-go-book/simpletax v1.0.0
    github/learning-go-book/simpletax/v2 v2.0.0
)
```

可以使用go mid tidy移除不再使用的版本

### 8.5 模块发布

将自己的模块放到版本管理系统中就能供他人使用，无论是github还是私有的版本控制系统。尽量使用宽松的协议，例如BSD、MIT、Apache。



## 9.Go的并发编程

### 9.1 何时使用并发编程

一般来说，素有程序都遵行三个步骤：获取数据、转换数据、输出结果。是否使用并发取决于数据在程序周的流动方式。假设有两个步骤，如果其中一个步骤执行时不依赖于另外一个步骤的输出结果，你去么这两个步骤可以并发；而如果一个步骤依赖于另一个步骤的输出结果，则两个步骤必须以顺序的方式进行。

如果并发运行的进程时间很短，就没有必要使用并发，因为并发也会消耗时间。并发编程常用于IO操作，因为向磁盘或网络读写的速度比除了最复杂的内存操作之外的所有操作都慢几千倍。

### 9.2 goroutine

goroutine是由Go运行时管理的轻量级线程。当Go程序启动时，Go运行时会创建一些线程并启动一个goroutine拉运行程序。你的程序创建的所有goroutine（包括程序入口部分）都由Go运行时调度器自动分配这些线程，就像操作系统在CPU内核间调度线程一样。

goroutine有几个好处：

- 创建goroutine比创建线程更快，因为你不是在操作操作系统级的资源
- goroutine的初始栈比线程栈更小，并且可以根据需要增长。这使得goroutine的内存效率更高
- 在goroutine之间切换比在线程之间切换更快，因为goroutine之间的切换完全发生在进程内部，避免了操作系统相对缓慢的调用
- 调度器作为Go进程的一部分能够进行优化。当调度器与网络轮询器一起工作时，可以检测goroutine合适因为IO阻塞而无法调度。它还与垃圾回收器继承，确保工作在所有操作系统线程之间可以较为平均地分配给Go进程。



在一个函数调用前放置go关键字可以启动一个goroutine。与其它函数一样，我们可以向他传递参数以初始化其状态。不过任何函数返回的值都会被忽略。

```go
go func(){
   ... 
}()
```

### 9.3 通道

goroutine使用通道进行通信，与使用make函数创建切片和映射一样，通道也是一个使用make函数创建的内置类型

```go
ch := make(chan int)
```

通道也是引用类型。即，当把通道传递给函数时，传递的实际上是在传递指向该通道的指针。通道的零值是nil

#### 9.3.1 读写和缓冲

```go
a := <- ch	//读通道
ch <- b		//写通道
```

> 每个写入通道的值只能被读取一次。如果多个goroutine从同一个通道中读取数据，写到通道中的一个值也只能被其中一个goroutine读取

默认情况下，通道无缓冲。

- 对一个已打开、无缓冲的通道写入都会导致goroutine写暂停，直到另一个goroutine从同一个通道读取。
- 对一个已打开、无缓冲的通道读取都会导致goroutine读暂停，直到另一个goroutine从同一个通道写入。

创建有缓冲的通道

```go
ch := make(chan int, 10)
```

内置函数len可以获得当前在缓冲区内有多少个值，使用cap可以获得最大的缓冲区大小。缓冲区容量不能改。

#### 9.3.2 通道的for-range

使用for-range循环从通道中读取数据

```go
for v := range ch{
    fmt.Println(v)
}
```

与其他for-range循环不同的是，改通道只声明了一个变量，也就是值。循环将一直持续到通道被关闭，遇到break或者return语句也会停止。

#### 9.3.3 关闭通道

当完成对一个通道的**写入**时，可以使用内置的close函数关闭通道

```go
close(ch)
```

**一旦通道被关闭**，任何试图写入通道或再次关闭该通道的操作都会发生panic，但可以读取被关闭的通道。

如果是缓冲通道，并且还有一些值没被读取，这些值任然可以读取，按序依次返回。

```go
ch1 := make(chan int, 5)
ch1 <- 1
ch1 <- 2
ch1 <- 3
ch1 <- 4
close(ch1)
for v := range ch1 {
    fmt.Println(v)
}
```

如果是无缓冲的，过着尽管是有缓冲但是通道没有任何值，则返回通道类型的零值

这导致了一个问题：当我们从通道中读取到零值时，如何区分这是被写入的零值，还是因为通道关闭而返回的零值呢？

```go
v,ok := <-ch
```

ok为true，那么通道是打开的，如果是false，则通道是关闭的。

#### 9.3.4 通道的行为

通道有序到不同的状态，每个状态在读取、写入或关闭时都有不同的行为

|      | 无缓冲、打开         | 无缓冲、关闭                                 | 有缓冲、打开               | 有缓冲、关闭                                                 | nil      |
| ---- | -------------------- | -------------------------------------------- | -------------------------- | ------------------------------------------------------------ | -------- |
| 读取 | 阻塞直到有数据写入   | 返回零值，需要使用逗号ok模式判断通道是否关闭 | 如果缓冲区无数据就会阻塞   | 剩余的值仍然在缓冲区中，当缓冲区数据为空时返回零值。需要使用逗号和ok模式判断通道是否关闭 | 永远挂起 |
| 写入 | 阻塞直到有数据被读出 | panic                                        | 如果缓冲区已满则会阻塞     | panic                                                        | 永远挂起 |
| 关闭 | 正常工作             | panic                                        | 正常工作，剩余的值仍然存在 | panic                                                        | panic    |

必须避免产生panic的情况

### 9.4 select语句

如果可以执行两个并发操作，有限执行哪一个？我们不能偏好某个操作，否则就医院无法处理某些情况。这就是所谓的饥饿。

使用select关键字允许一个goroutine从一组，通道中读取或者写入。他看起来很像一个，空switch语句。

```go
select {
    case v := <-ch:
    	fmt.Println(v)
	case v := <-ch2
    	fmt.Println(v)
	case ch3 <- x:
    	fmt.Println(v)
    case <-ch4:
    	fmt.Println("got value on ch4,but ignored it")
}
```

select中的每个case语句都是对一个通道的读或写操作。

如果多个case语句都对通道有读或写，会如何？select会从任何一个可执行的case语句中随机挑选，顺序并不重要解决了饥饿问题。

select语句随机选择的另一个好处是可以防止以不一致的顺序获取锁，从而避免死锁。

```go
//产生死锁
func main() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go func() {
		ch1 <- 1
		v := <-ch2
		fmt.Println(v)
	}()
	ch2 <- 2
	v := <-ch1
	fmt.Println(v)
}
```

原因是：func函数的goroutine等待ch1被读，而main的goroutine等待ch2被读，相互等待，产生死锁。

```go
//select避免死锁
func main() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	go func() {
		ch1 <- 1
		v := <-ch2
		fmt.Println(v)
	}()
    var v2 int
    select{
    case ch2 <-2:
    case v2 = <-ch1:
    }
	fmt.Println(v2)
}
```

与switch一样，select语句有一个default从句。如果想在一个通道上实现非阻塞的读或写操作，那么可以使用带有default的select。

下面的代码在ch中没有值可读时不会等待，它立即执行default从句：

```go
select{
case v:= <-ch:
    fmt.Println("read from ch:",v)
default:
    fmt.Println("no value written to ch")
}
```

在for-select循环中，如果只有一条default语句，肯定是错误的。当没有任何容易需要读或写时，每次循环都会执行default 语句。这使得for循环不断运行，从而占用大量的CPU资源。

### 9.5 并发实践与模式

#### 9.5.1 goroutine、for循环与变量

大多数情况下，我们依赖启动goroutine的闭包没有参数。它从上下文环境中获取变量的值，这样使用会造成一种常见的错误：当试图捕获for循环的索引或值时，这种方法不起作用

下面代码有一个难以察觉的错误

```go
func main() {
	a := []int{2, 4, 6, 8, 10}
	ch := make(chan int, len(a))
	for _, v := range a {
		go func() {
			ch <- v * 2
		}()
	}
	for i := 0; i < len(a); i++ {
		fmt.Println(<-ch)
	}
}

//结果
20
20
20
20
20
```

原因是每个goroutine的闭包都捕获了同一个变量。for循环中的索引和值变量在每次迭代都被重复使用，并且最后分配给v的值时10.当goroutine运行时，他们看到的就是这个值。

解决方法

```go
//v1
for _, v := range a {
    v := v
    go func() {
        ch <- v * 2
    }()
}

//v2
for _, v := range a {
    go func(val int) {
        ch <- val * 2
    }(v)
}
```

#### 9.5.2 防止goroutine泄露

每次当你启动goroutine函数时，必须确保它最终会退出。与变量不同，Go运行时无法监测goroutine函数将不再被使用。如果goroutine不退出，即使啥也不做，调度器仍然会定期给它分配执行时间，这就拖慢了程序。这就是所谓的goroutine泄露。

```go
func countTo(max int) <-chan int{
	ch := make(chan int)
	go func() {
		for i :=0;i<max;i++ {
			ch <- i
		}
		close(ch)
	}()
	return ch
}

func main() {
	for i := range countTo(10){
		fmt.Println(i)
	}
}

//结果
0
1
2
3
4
5

```

在常见情况下，当遍历完所有的值，goroutine就会自动退出，然而如果提前退出循环，goroutine就会永远阻塞，等待从通道中读出一个值：

```go
func main() {
	for i := range countTo(10){
        if i > 5 {
            break
        }
		fmt.Println(i)
	}
}
//
0
1
2
3
4
5

```

#### 9.5.3 通道结束模式

通道结束模式提供了一种方法来通知goroutine停止执行。它使用通道来发出信号，表明goroutine现在可以退出了

看个例子，把相同数据传递给多个函数，但只想得到最快的函数的结果

```go
func searchData(s string,searchers []func(string) []string) []string{
	done := make(chan struct{})
	result := make(chan []string)
	for _,searcher := range searchers {
		go func(searcher func(string) []string) {
			select {
			case result <- searcher(s):
			case <-done:
			}
		}(searcher)
	}
	r := <-result
	close(done)
	return r
}
```

启动的goroutine中的select语句要么等待在result通道上的写操作（当searcher函数返回时），要么等待读取done通道上的数据

记住，在打开的通道上读取数据会阻塞，知道有可用的数据；而在关闭的通道上读取数据总是返回该通道的零值。

这意味着从done读取的case会保持阻塞，知道done被关闭。在searchData中，读取到写入result的第一个值，然后关闭done通道。

通道关闭后，goroutine收到信号，然后退出，防止他们泄露。

#### 9.5.4 使用取消函数终止goroutine

看看之前coutTo的例子

```go
func countTo(max int) <-chan int {
	ch := make(chan int)
    done := make(chan struct{})
    cancel := func(){
        close(done)
    }
	go func() {
		for i := 0; i < max; i++ {
            select{
                case <- done:
                	return
                case ch <- i:
            }
		}
		close(ch)
	}()
	return ch,cancel
}

func main() {
    ch,cancel := countTo(10)
	for i := range ch{
        if i > 5 {
            break
        }
		fmt.Println(i)
	}
    cancel()
}
```

cancel必须在for循环调用之后用，这个闭包函数关闭done通道，goroutine就会case接受到nil，返回，从而关闭ch通道

#### 9.5.6 缓存通道和无缓存通道

当我们知道已经启用了多少个goroutine，而想限制要启动的goroutine的数量，或者想限制队列中排队的阻塞时，缓存通道很有用。

#### 9.5.7 背压

另一个可以用缓冲通道实现的技术是背压，可以使用缓冲通道和select语句来限制系统中并发请求的数量。

被压是指在异步场景中，被观察者发送事件的速度远快于观察者的处理速度的情况下的一种策略，此策略告诉上游的被的被观察者降低发送速度。简而言之，背压是一种限流策略。

```go
type PressureGuage struct{
    ch chan struct{}
}

func New(limit int) *PressureGuage{
    ch := make(chan struct{},limit)
    for i:=0;i<limit;i++ {
        ch <- struct{}{}
    }
    return &PressureGuage{
        ch:ch,
    }
}

func (pg *PressureGuage) Process(f func()) error{
    select{
        case <- pg.ch:
        	f()
        	pg.ch <- struct{}{}
        	return nil
        default:
        	return errors.New("no more capacity")
    }
}
```

这段代码中，我们创建了一个结构体其拥有一个包含若干“令牌”的缓冲通道和一个待运行的函数。每当一个goroutine想使用这个函数时，它就会调用Process。select试图从通道中读取一个令牌。如果它获得令牌函数就会运行，并返回令牌。如果不能获取令牌，就执行default语句，返回一个错误。

```go
func doTingThatShouldBeLimited() string{
    time.Sleep(2 * time.Second)
    return "done"
}

func main(){
    pg := New(10)
    http.HandleFunc("/request",func(w http.ResponseWriter,r *http.Request){
        err := pg.Process(func(){
            w.Write([]byte(doTingThatShouldBeLimited()))
        })
        if err != nil {
            w.WriteHeader(http.StatusTooManyRequests)
            w.Write([]byte("Too many requests"))
        }
    })
    http.ListenAndServe(":8080",nil)
}
```

#### 9.5.8 在select中跳过无效分支

如果select中的一个case语句正在读取一个关闭的通道，将总可以读取成功，但是得到的是零值。每次select中case触发时，都需要以确保读取的值是有效的，如果无效就跳过当前的case代码。否则，程序就会浪费许多时间去读取没有意义的垃圾值。

我们可以使用nil通道使得case无效。当检测到一个通道被关闭时，将该通道设置为nil。相关的case语句就不会再运行，因为nil通道读取医院没有返回值

```go
for{
    select{
        case v,ok :=<-in:
            if !ok{
                in = nil
                continue
            }
        case v,ok := <-in2:
            if !ok{
				in2 = nil
                continue
            }
        case <-done:
        	return
    }
}
```

#### 9.5.9 如何处理超时

Go中有很多关于超时的模式。这里介绍一种，使用select语句在两个case语句中进行选择。

```go
func timeLimit() (int,error){
    var result int
    var err error
    done := make(chan struct{})
    go dunc(){
        result,err = doSomeWork()
        close(done)
    }()
    select{
        case <-done:
         	return result,err
        case <-time.After(2 * time.Second):
        	return 0,errors.New("work timed out")
    }
}
```

如果done通道先关闭，那么结束通道的读取就会成功，并返回值。

第二个通道是由time包的After函数返回的，在传入指定的time.Duration后，它有一个值被写入其中。在doSomeWork完成之前读取该值，timeLimit返回超时错误。

#### 9.5.10 使用WaitGroup

有时一个goroutine需要等待多个goroutine完成他们的工作，可以使用WaitGroup。

```go
func main(){
    var wg sync.WaitGroup
    wg.Add(3)
    go func(){
        defer wg.Done()
        doThing1()
    }()
     go func(){
        defer wg.Done()
        doThing2()
    }()
     go func(){
        defer wg.Done()
        doThing3()
    }()
    wg.wait()
}
```

sync.WaitGroup不用初始化，只需要声明，默认为零值，因为它的零值很有用。sync.WaitGroup有三个方法

- Add，用于增加要等待goroutine的计数器
- Done，用于递减计数器，当一个goroutine完成时被调用
- Wait，暂停当前的goroutine直到计数器归零

Add通常使用启动的goroutine的数量作为参数调用一次。Done在goroutine内部调用。为了确保Done始终被调用，我们使用defer调用它，这样即使goroutine中发生panic也始终可以调用Done

> 注意不要显示传递sync.WaitGroup，原因有二
>
> 1.如果把sync.WaitGroup传递给goroutine而不使用指针，那么函数中使用的就是一个副本，调用Done不会递减原来的sync.WaitGroup
>
> 2.设计。我们应当把并发处理隐藏在API内部。

当有多个goroutine向同一个通道写入时，我们需要确保被写入的通道只被关闭一次，适合用sync.WaitGroup

```go
func processAndGather(in <-chan int,processor func(int) int,num int) []int {
    out := make(chan int,num)
    var wg sync.WaitGroup
    wg.Add(num)
    for i:=0;i<num;i++{
        go func(){
            defer wg.Done()
            for v := range in{
                out <- processor(v)
            }
        }()
    }
    go func(){
        wg.Wait()
        close(out)
    }()
    var result []int
    for v:=range out{
        result append(result,v)
    }
    return result
}
```

该例子中，启动了一个监控的goroutine，有等待所有的执行goroutine退出。当他们退出是，监控的goroutine在输出通道是close。当out被关闭**且**者缓冲区为空时，for-range通道循环退出。最后，该函数返回处理后的值。

#### 9.5.11 只执行一次代码

有时候需要懒加载，或者在程序启动后仅仅调用一次初始化代码。这通常因为初始化速度较慢，而且可能不需要在每次程序运行时进行初始化。可以使用sync.Once

```go
type SlowComplicatedParser interface{
    Parse(string) string
}

var parser SlowComplicatedParser
var once sync.Once

func Parse(dataTopParse string) string{
    once.Do(func(){
        parser = initParser()
    })
    return parser = initParser()
}

func initParser() SlowComplicatedParser{
    //do something
}
```

例子中，想确保parser只被初始化一次，就在闭包中处理。Parse被调用一次之后，once.Do就不会再执行这个闭包。

sync.Once也不能被传递，否则就不是原来的实例。

### 9.6 何时使用互斥锁

常见场景是goroutine仅对一个共享值进行读取或写入。

用一个多人游戏的内存记分牌作为例子，先用通道实现

```go
func scoreboardManager(in <-chan func(map[string]int), done <-chan struct{})){
    scoreboard := map[string]int{}
    for{
        select {
            case <-done:
            	return
            case f := <-in:
            	f(scoreboard)
        }
    }
}
```

该函数声明了一个映射，然后在一个通道上监听或者修改此映射的函数，并在第二个通道上监听以了解何时关闭通道。

再创建一个类型，并使用一个方法将值写入映射

```go
type ChannelScoreboardManager chan func(map[string]int)

func NewChannelScoreboardManager() (ChannelScoreboardManager,func()) {
    ch := make(ChannelScoreboardManager)
    done := make(chan struct{})
    
    go scoreboardManager(ch,done)
    return ch,func(){
        close(done)
    }
}

func (csm ChannelScoreboardManager) Update(name string,val int){
    csm <- func(m map[string]int){
        m[name] = val
    }
}

func (csm ChannelScoreboardManager) Read(name string) (int,bool){
    var out int
    var ok bool
    done := make(chan struct{})
    csm <- func(m map[string]int){
        out,ok = m[name]
        close(done)
    }
    <-done
    return out,ok
}

func main(){
    ch,cancel = NewChannelScoreboardManager()
    ch.Update("a",1)
    ch.Update("b",2)
    ch.Update("c",3)
    val,ok = ch.Read("b")
}
```

使用通道很麻烦，而且一次只允许从ch中读取一次。更好的办法是互斥锁。

- 第一个名为Mutex,有两个方法：Lock和Unlock。调用Lock时，只要一个goroutine当前处于临界区，就会导致其他goroutine暂停阻塞。当临界区被清除时，锁被其他的goroutine获得，临界区的代码被执行。调用Unlock方法标志着临界区的结束。

- 第二个互斥锁为RWMutex，它允许你同时拥有读锁和写锁。虽然在临界区一次只能有一个写入，但是读锁是共享的，多个读取可以同时在临界区发生。读写锁互斥，读的时候不能写。写锁是用Lock和Unlock方法管理的，读锁是Rlock和RUnlock方法管理的。

任何时候获得了一个互斥锁，必须确保释放这个锁。在调用Lock或者RLock后，立即使用defer语句调用Unlock

```go
type MutexScoreboardManager struct{
    l	sync.RWMutex
    scoreboard map[string]int
}

func NewMutexScoreboardManager() *MutexScoreboardManager {
    return &MutexScoreboardManager{
        scoreboard: map[string]int{},
    }
}

func(msm *MutexScoreboardManager) Update(name string,val int){
    msm.l.Lock()
    defer msm.l.Ulock()
    msm.scoreboard[name] = val
}

func (msm *MutexScoreboardManager) Read(name string)(int,bool){
    msm.l.RLock()
    defer msm.l.RUnlock()
    val,ok := msm.scoreboard[name]
    return val,ok
}
```

与sync.Once和sync.WaitGroup一样，互斥锁不能被复制。如果将它们传递给函数或作为结构体上的字段被访问，必须通过指针来实现。一旦互斥锁有副本，他的锁就不会被共享。

简单点的例子

```go
package main

import (
    "fmt"
    "sync"
)

var (
    // 逻辑中使用的某个变量
    count int

    // 与变量对应的使用互斥锁
    countGuard sync.Mutex
)

func GetCount() int {

    // 锁定
    countGuard.Lock()

    // 在函数退出时解除锁定
    defer countGuard.Unlock()

    return count
}

func SetCount(c int) {
    countGuard.Lock()
    count = c
    countGuard.Unlock()
}

func main() {

    // 可以进行并发安全的设置
    SetCount(1)

    // 可以进行并发安全的获取
    fmt.Println(GetCount())

}
```

或者改变成读写锁

```go
var (
    // 逻辑中使用的某个变量
    count int
    // 与变量对应的使用互斥锁
    countGuard sync.RWMutex
)
func GetCount() int {
    // 锁定
    countGuard.RLock()
    // 在函数退出时解除锁定
    defer countGuard.RUnlock()
    return count
}
```

### 9.7 atomic

Go还有另一种方法来保持数据在多个线程之的一致性。sync/atomic包提供了对现代CPU中内置的原子变量操作的访问，以add、swap、load、store或compare以及swap(CAS)方式操作一个适用于单个寄存器的值。通常来说，goroutine和互斥锁处理并发编程已经足够。



## 10.Go语言标准库

### 10.1 标准io库

io.Reader和io.Writer都只定义了一个方法

```go
type Reader interface {
    Read(p []byte) (n int,err error)
}

type Writer interface {
    Write(p []byte) (n int.err error)
}
```

io.Writer接口的Writer方法接收一个字节切片，将其写入接口实现。它返回写入的字节数，如果出错则返回错误。

io.Reader上的Read方法将切片作为参数并修改，调用时将最多len(p)的字节写入切片。返回成功的字节数。

可能有人认为io.Reader接口应该这样定义

```go
type Reader interface {
    Read() (p []byte, err error)
}
```

关于io.Reader有三点要注意

1. 我们创建一个一个缓冲区，并在每次调用r.Read时重用它。这使得我们可以使用一次内存分配，如果返回一个[]byte，那么每次调用都需要调用一个新的分配，每次分配都在堆上结束，这将给垃圾回收器带来相当多的工作。
2. 我们根据r.Read返回的n值判断有多少字节被写入缓冲区，并从buf切片派生的子切片上循环读取数据。
3. 当从r.Read返回的错误是io.EOF时，我们便可以指代已经完成了从r的读取。它表明io.Reader中已经没有数据可以读取。当返回io.EOF时，我们就完成了处理并返回结果。

因为io.Reader和io.Writer都是十分简单的接口，所哟有许多不同方式实现它。

例如，使用strings.NewReader函数从字符串创建io.Reader

```go
s := "one two three"
sr := strings.NewReader(s)
```

io.Reader和io.Writer一般都结合装饰器使用，即在NewReader的基础上返回的实例，再用包装器包装一层

```go
func buildZipReader(fileName string)(*gzip.Reader,func(),error){
    r,err := os.Open(fileName)
    if err != nil {
        return nil,nil,err
    }
    gr,err := gzip.NewReader(r)
     if err != nil {
        return nil,nil,err
    }
    return gr,func(){
        gr.Close()
        r.Close()
    },nil
}
```

还有其他 标准函数用于向现有的io.Reader和io.Writer实例添加新功能

- io.MultiReader 它返回一个io.Reader，从多个io.Reader实例中逐个读取
- io.LimitReader 它返回一个io.Reader，从提供的io.Reader中读取指定数量的字节
- io.MultiWriter 同时写入多个io.Writer实例中

io包中还定义了其他的但方法接口，如io.Closer和io.Seeker：

```go
type Closer interface {
    Close() error
}

type Seeker interface {
    Seek(offset int64, whence int)(int64, error)
}
```

os.file等类型实现了io.Closer接口，因为它们需要在读或写操作完成后执行清理工作。通常情况下，Close是通过defer调用的。

io.Seeker接口用于随机访问一个资源。参数whence的有效值是常数io.SeekStart、io.SeekCurrent、io.SeekEnd。这里whence是int类型

io包中定义了以不同的方式组合上述的4个接口。分别是io.ReadCloser、io.ReadSeeker、io.ReadWriterCloser、io.ReadWriteSeeker、io.ReadWriter、io.WriteCloser和io.WriteSeeker。

ioutil包提供了一些简单的实用工具函数来执行特定功能，比如一次性将整个io.Reader而没有实现io.Reader实现读入的字节切片、读写文件，以及处理临时文件。ioutil.ReadAll、ioutil.ReadFile和ioutil.WriteFile函数对小型数据源来说是不错的选择，但当处理大型数据时最好使用bufio包中的Reader、Writer和Scanner。

### 10.2 time包

有两类主要类型用于表示时间：time.Duration和time.Time。

一个时间段用time.Duration来表示，它是一个基于int64的类型。Go可以表示的最小时间是1ns，但time包定义了time.Duration类型的常量来表示纳秒、微秒、毫秒、秒、分钟和小时。例如，使用以下方式表示2小时30分钟的持续时间

```go
d := 2*time.Hour + 30 * time.Minute
```

time.Duration里定义了几个满足fmt.stringer接口的方法，并通过String方法返回格式化的时间间隔字符串。它还有一些方法来获得小时、分钟、秒、毫秒、微秒或纳秒的数值。Truncate和Round方法将time.Duration截取或四舍五人到指定的time.Duration 的单位中。

某个时间则用time.Time类型表示，并带有一个时区。可以通过函数time.Now获得对当前时间的引用。这将返回一个设置为当前本地时间的time.Time实例。

> time.Time包含时区信息，这意味着不能直接使用==来检查两个time.Time实例是否指向同一个时间点。可以使用Equal方法，该方法会对时区进行修正。

time.Parse函数将string转换为time.time，而Format方法可以将time.Time转换为string。

Go使用了独有的日期和时间格式语言。他使用**January 2,2006 at 3:04:05PM MST**(美国山地时间)的格式进行格式化

```go
t,err := time.Parse("2006-02-01 15:04:05  -0700","2016-13-03 00:00:00 +0000")
if err != nil{
    return err
}
fmt.Println(t.Format("January 2,2006 at 3:0405PM MST"))		//March 13,2016 at 12:0000AM +0000
```

time.Time也有一些方法可以提取它的部分内容，包括Day、Month、Year、Hour、Minute、Second、Weekday、Clock（它将time.Time时间中的小时、分钟和秒的int值作为一个整体返回），已经Date（它将time.Time时间中的年、月、日的int值作为一个整体返回）。还可以用After、Before和Equal方法将一个time.Time实例与另一个进行比较。

SUb方法返回一个time.Duration(两个time.Time实例之间的时间间隔)，Add方法这回一个 tine.Time（晚于时间间隔(time.Duration）之后的时间)，而AddDate方法会返回一个新的time.Time实例，该实例按指定的年数、月数和日数递增。tine.Time同样定义了Truncate和Round 方法。所有这些方法都是在值接收者上定义的，所以它们不会修改time.Time实例。

#### 10.2.1 单调时钟时间

大多数操作系统跟踪两种不同的时间：墙上时间和单调时间，前者与当前时间对应，后者从计算机启动的时间开始计算。追踪两种不同时间的原因是墙上时间不能单调递增。在夏令时、闰秒和NTP（Network Time Protocol，网络时间协议）更新墙上时间，可能会意外导致偏差。这在设置计时器或查找已过的时间量时可能会造成问题。

为了解决这个潜在的问题，每当设置定时器或用time.Now 创建 time.Time 实例时，Go都使用单调时间来计算逝去的时间。这个实现是内置的，无须单独调用，计时器会自动使用它。如果两个time.Time实例都设置了单调时间，则sub方法使用单调时间来计算time.Duration，如果没有（因为其中一个或两个实例不是用time.Now创建的），则Sub方法使用实例中指定的时间来计算time.Duration。

#### 10.2.2 计时器和超时

time包中包含一些函数，这些函数都返回一个通道，在指定时间后返回数据。time.After 函数返回仅输出一次的通道，而time.Tick返回的通道在每隔指定的time.Duration后都会返回一次。这些都在Go的并发编程情况下使用，以实现超时或循环任务。我们也可以用time.AfterFunc函数来触发一个函数在指定的time.Duration后运行。不要在复杂的程序外部使用time.Tick，因为底层的time.Ticker不能被关闭（因此不能被垃圾回收）。可以使用time.NewTicker函数代替，它返回*time.Ticker（它包含被监听的通道，以及重置和停止ticker的方法。

### 10.3 encoding/json

#### 10.3.1 使用结构体标签添加元数据

```go
{
    "id":"12345",
    "date_ordered":"2020-05-01T13:01:02Z",
    "customer_id":"3",
    "items":[{"id":"xyz123","name":"Thing 1"},{"id":"abc789","name":"Thing 2"}]
}

//定义数据类型来映射JSON数据
type Order struct {
    ID			string		`json:"id"`
    DateOrdered time.Time	`date_ordered`
    CustomerID	string		`json:"customer_id"`
    Items		[]Item		`json:"items"`
}
type Item struct {
    ID		string `json:"id"`
    Name	string	`json:"name"`
}
```

我们用结构体标签指定处理JSON的规则，结构体标签是写在结构中的字段之后的字符电。尽管结构体标签是带有反斜线的字符串，但它们不能超过一行。结构体标签由一个或多个标签和值成对组成，写成 tagName：'tagvalue” 并以空格分隔。因为它们只是字符串，编译器不能验证它们的格式是否正确，所以我们需要使用go vet执行格式检查。另外，请注意，所有这些字段都应该是导出字段。与其他包一样，encoding/json 包中的代码不能访问其他包中结构体上未导出的字段。

对于JSON数据的处理，我们使用标签名json来指定与结构体字段对应JSON字段名。如果没有提供json标签，则默认假定JSON对象字段的名称与Go结构体宇段的名称一致。尽管有这种默认行为，但即使JSON字段名与结构体字段名相同，最好还是使用结构体标签来显式指定字段名。

当从JSON反序列化到没有json标签的结构体字段时，名称匹配是不区分大小写的：而当把没有json标签的结构体字段序列化到JSON时，JSON字段的单词首字母总是大写的，因为该字段是导出的。

如果一个字段在序列化或反序列化时需要忽略，请使用-作为名称。如果字段是空的，该字段应该被排除在结果之外，在json字段名后面加上，omitempty。

#### 10.3.2 序列化与反序列化

```go
//反序列化 将json字符串转换为数据结构
var o Order
err := json.Unmarshal([]byte(data),&o) //data是JSON字符串
if err!=nil{
    return err
}

//序列化 将数据结构转换为json字符串
var o = Order{...}
out,err = json.Marshal(o) 
```

#### 10.3.3 JSON读取与写入

json.Decoder和josn.Encoder类型分别对实现了io.Reader和io.Writer接口的任意类型执行读和写操作

```go
type Person struct {
    Name string 	`json:"name"`
    Age int			`json:"age"`
}
toFile := Person{
    Name:"Fred",
    Age:40,
}
```

os.File类型同时实现了io.Reader和io.Writer接口，所以我们可以用它来演示json.Decoder和json.Encoder。首先，我们通过将临时文件传递给json.NewEncoder来获取一个 json.Encoder，然后将toFile作为参数传人json.Encoder的Encode方法，这样就可以将toFile写入临时文件：

```go
tempFile, err := ioutil.TempFile(os.TempDir(), "sample-")
if err != nil {
    panic(err)
}
defer os.Remove(tmpFile.Name())
err = json.NewEncoder(tmpFile).Encode(toFile)
if err != nil {
	panic(err)
}
err = tmpFile.Close()
if err != nil {
	panic(err)
}
```

一旦toFile被成功写人，我们就可以将JSON读取出来，通过将临时文件的引用传递给json.NewDecoder，然后在返回的json.Decoder变量上使用Person类型变量的引用作为调用参数，从而调用 Decode 方法，这样 Person 类型变量就可以从 JSON 读取到对应的值：

```go
tmpFile2, err := os.Open(tmpFile.Name())
if err != nil {
    panic(err)
}
var fromFile Person
err = json.NewDecoder(tmpFile2).Decode(&fromFile)
if err != nil {
    panic(err)
}
err = tmpFile2.Close()
if err != nil {
    panic(err)
}
fmt.Printf("%+v\n",fromFile)
```

#### 10.3.4 JSON流的编码和解码

假设有如下数据需要处理：
```go
{"name": "Fred","age": 40}
{"name":"Mary","age": 21}
{"name":"Pat","age":30}
```

我们假设它存储在一个名为data的字符串中，当然它也可以是一个文件，甚至是一个传人的 HTTP 请求(我们稍后会在 HTTP 服务端的工作原理中看到）。

我们把这些数据存储到变量t中，一次处理一个JSON对象。

与之前一样，我们用数据源初始化 json.Decoder，不同的是，这次我们使用 json.Decoder 的 More 方法与for循环对 JSON 对象的数据进行循环读取，它一次读一个JSON对象的数据

```go
dec := json.NewDecoder(strings.NewReader(data))
for dec.More() {
	err := dec.Decode(&t)
	if err != nil {
		panic(err)
	}
	// process t
}
```

用json.Encoder 写入多个值与用它写入一个值一样。在这个例子中，我们要写人bytes.Buffer，但任何满足 io.Writer 接口的类型都可以使用：

```go
var b bytes.Buffer
enc := json.NewEncoder(&b)
for _,input := range allInputs {
	t := process (input)
	err = enc.Encode (t)
    if err != nil {
        panic(err)
    }
}
out := b.String()
```

在这个例子中，数据流包含多个未包装在数组中的 JSON 对象，但你可以使用 Json.Decoder 从数组中读取单个对象，而无须一次性将整个数组加载到內存中。这可以极大地提高性能并减少内存的使用。

#### 10.3.5 自定义JSON解析

在实际开发中，经常会遇到需要定制json编解码的情况。比如，按照指定的格式输出json字符串，又比如，根据条件决定是否在最后的json字符串中显示或者不显示某些字段。如果希望自己定义对象的编码和解码方式，需要实现以下两个接口：

```go
type Marshaler interface {
    MarshalJSON() ([]byte, error)
}
type Unmarshaler interface {
    UnmarshalJSON([]byte) error
}
```

对象实现接口后，编解码时自动调用自定义的方法进行编解码。

下面例子中，自定义编解码方法。编码时，将map转化为字符串数组。解码时，将字符串数组转化为map。

```go
package main
import (
	"encoding/json"
	"fmt"
)
type Bird struct {
	A	map[string]string	`json:"a"`
}
func (bd *Bird) MarshalJSON() ([]byte, error) {
	l := []string{} //空字符串切片
	for _,v := range bd.A {
		l = append(l,v)
	}
	return json.Marshal(l)
}
func (bd *Bird) UnmarshalJSON(b []byte) error {
	l := []string{}
	err := json.Unmarshal(b, &l)
	if err != nil {
		return err
	}
	for i,v := range l {
		k := fmt.Sprintf("%d", i)
		bd.A[k] = v
	}	
	return nil
}
func main() {
	m := map[string]string{"1": "110", "2":"120", "3":"119"}
	xiQue := &Bird{A:m}
	xJson, err := json.Marshal(xiQue)
	if err != nil {
		fmt.Println("json.Marshal failed:", err)
	}
	fmt.Println("xJson:", string(xJson))	//自定义序列化 xJson: ["119","110","120"] 
	b := `["apple", "orange", "banana"]`	
	baoXiNiao := &Bird{A:map[string]string{}} //这里大括号代表类型为map[string]string，值为空
	err = json.Unmarshal([]byte(b), baoXiNiao)
	if err != nil {
		fmt.Println("json.Unmarshal failed:", err)
	}
	fmt.Println("baoXiNiao:", baoXiNiao)	//自定义反序列化 baoXiNiao: &{map[0:apple 1:orange 2:banana]}
}
```

### 10.4 net/http

#### 10.4.1 客户端 

net/http包定义了一个Client类型，用于发出HTTP请求和接收HTTP响应。net/http包中有一个默认的客户端实例（命名为DefaultClient），但我们应该避免在生产环境中使用它，因为它默认没有超时设置，我们应该实例化单独的客户端。我们只需要为整个程序创建一个http.Client，因为它可以正确地处理跨goroutine的多个同步请求：

```go
client := &http.Client{
    Timeout: 30 * time.Second
}
```

构建一个请求时，可以用http.NewRequestWithContext函数创建一个新的*http.Request实例，把一个上下文、方法和你要请求的URL传给它。如果你正在构建一个PUT、POST或PATCH请求，则需要将请求的内容设置成io.Reader类型，并作为最后一个参数传入。如果请求没有内容，则使用nil。

```go
req,err := http.NewRequestWithContext(context.Background(),http.MethodGet,"https://jsonplaceholder.typicode.com/todos/1",nil)
if err != nil {
    panic(err)
}

req.Header.Add("X-My-Client","Learning Go") //添加消息头
res,err := client.Do(req)	//发送请求，接收数据
if err != nil {
    panic(err)
}

defer res.Body.Close()
if res.StatusCode != http.StatusOK{
    panic(fmt.Sprintf("unexpected status:got %v",res.Status))
}
fmt.Println(res.Header.Get("Content-Type"))
var data struct{
    UserID int 		`json:"userId"`
    ID	   int 		`json:"id"`
    Title string	`json:"title"`
    Completed bool	`json:"completed"`
}
err = json.newDecoder(res.Body).Decode(&data)
if err != nil {
    panic(err)
}
fmt.Printf("%+v\n",data)
```

#### 10.4.2 服务端

HTTP服务端基于http.server和http.Handler接口的基础构建。http.Client发送HTTP请求，而http.server负责监听HTTP请求。它是一个高性能的HTTP/2服务端，支持TLS。

对服务端的请求由分配给Handler字段的http.Handler接口处理。这个接口只定义了一个方法：

```go
type Handler interface {
	ServeHTTP(http.ResponseWriter, *http.Request)
}
```

*http.Request定义了三个方法：http.Request与向HTTP服务端发送请求的类型相同。http.ResponseWriter接口定义了三个方法：

```go
type ResponseWriter interface {
    Header() http.Header
    Write([]byte) (int, error)
    WriteHeader (statusCode int)
}
```

这些方法必须按照特定的顺序调用。首先，调用Header来获得http.Header的实例，并设置你需要的任何响应头消息。如果你不需要设置响应头信息，则不用调用它。接下来，用HTTP状态码调用 WriteHeader，为请求的响应设置状态码（所有的状态码都在net/http包中定义为常量。这本来是一个定义自定义类型的好地方，但并没有实现。所有的状态码常量都是无类型的整数)。如果你正在发送一个状态码是200的响应，则可以跳过WriteReader。最后，调用Write方法来设置响应的内容。下面是一个处理程序

```go
type HelloHandler struct{}

//实现http.Handler接口
func (hh HelloHandler) ServeHTTP(w http.ResponseWriter,r *http.Request){
    w.Write([]byte("Hello!\n"))	//设置响应内容
}

//实例化一个新的http.Server实例
s := http.Server{
    Addr: ":8080",
    ReadTimeout: 	30*time.Second,
    WriteTimeout:	90*time.Second,
    IdleTimeout:	120*time.Second,
    Handler:		HelloHandler{},		//使用自定义的handler
}
err := s.ListenAndServe()
if err != nil{
    if err != http.ErrServerClosed{
        panic(err)
    }
}
```

Addr字段用于指定服务器启动后监听的主机和端口。如果不指定它们，服务器就默认在所有主机上监听标准的HTTP端口(80)。默认情況下是没有超时处理的，不过可以使用time.Duration为服务器的读、写和空闲指定超时的值。要确保设置这些值以正确处理恶意或破坏性的HTTP客户端请求。最后，将http.Handler指定给服务器实例的Handler宇段（这里的HelloHandler实现了http.Handler接口）。

仅处理单个请求的服务器作用不大，所以Go标淮库包含一个请求路由器：*http.ServeMux。我们用http.NewServeMux函数创建一个实例。它满足http.Handler接口，所以可以被分配到http.server中的Handler字段。它还包括两个方法来调度请求。第一个方法直接调用Handle函数，它接收两个参数：路径和http.Handler。如果路径匹配，就会调用http.Handler。

虽然我们可以创建http.Handler的实现，但更常见的模式是使用*http.ServeMux的HandleFunc方法：

```go
mux := http.NewServeMux()
handler := HelloHandler{}
//第一个方法
mux.Handle("/", handler) //handler是实现了http.Handler的结构体实例
//第二个方法
mux.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello!\n"))
})
```

这个方法接收一个函数或闭包，并将其转换为http.HandlerFunc。对于简单的处理程序，一个闭包就足够了。对于依赖其他业务逻辑的复杂处理程序，可以使用结构体上的方法。

因为http.ServeMux将请求分派给http.Handler实例，并且由于\*http.ServeMux实现了http.Handler，所以可以用多个相关的请求创建一个\*http.ServeMux实例，并将其注册到父http.ServeMux中

```go
person := http.NewServeMux()
person.HandleFunc("/greet", func(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("greetings!\n"))
})
dog := http.NewServeMux()
dog.HandleFunc("/greet", func(w http. Responsewriter, r *http. Request) {
	w.Write([]byte("good puppy! \n"))
})
mux := http.NewServeMux()
mux.Handle("/person/", http.StripPrefix("/person", person)) 
mux.Handle("/dog/", http.StripPrefix("/dog", dog))
```

在上面的例子中，访问/person/greet的请求由属于person的处理程序进行处理，而访问/dog/greet的请求则由属于dog的处理程序进行处理。当我们用mux注册person和dog时，需要使用http.StripPrefix辅助函数来移除路径中mux已经处理过的部分。

#### 10.4.3 中间件

HTTP服务端最常见的要求之一是在多个处理程序中执行一组动作，比如检查用户是否登录、为请求计时或检查请求头。Go通过中间件模式处理这些跨领域的问题。中间件模式没有使用特殊的类型，而是使用函数来接收http.handler实例并返回http.handler。通常，返回的http.Handler是一个闭包，它被转换为http.HandlerFunc。这里有两个中间件生成器，一个用于请求的计时，另一个则用于简单的访问控制：

```go
func RequestTimer (h http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		h.ServeHTTP(w, r)
		end := time.Now()
		log.Printf("request time for %5: %", r.URL.Path, end.Sub(start))
    })
}

var securityMsg = []byte("You didn't give the secret password\n"),

func TerribleSecurityProvider (password string) func(http.Handler) http.Handler {
    return func(h http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request){
            if r.Header. Get ("X-Secret-Password") != password {
                w.WriteHeader(http.StatusUnauthorized)
				w.Write(securityMsg)
				return
            }
            h.ServeHTTP (w, r)
        })
    }
}
```

这两个中间件的实现演示不了中间件是如何工作的。首先，我们进行设置操作或检查。如果检查没有通过，则在中间件中写入输出（通常有一个错误代码）并返回。如果一切正常，则调用处理程序的 ServeHTTP 方法。当它返回时，我们需要执行相应的清理操作。

TerribleSecurityProvider展示了如何创建可配置的中间件。我们传人配置信息（在上面的示例中，是密码检查的配置)，然后该函数返回使用该配置信息的中间件。这里返回的的是一个闭包，其闭包中又会返回一个闭包，尽管这让人不容易理解。

我们通过链式调用将中间件添加到请求处理程序中：

```go
terribleSecurity := TerribleSecurityProvider("GOPHER")

mux.Handle("/hello", terribleSecurity(RequestTimer(
    http.HandlerFunc(func(w http. ResponseWriter, r *http.Request){
        w.Write([]byte("Hello!\n"))
    })
)))
```

我们将从 TerribleSecurityProvider 处得到中间件，然后用一系列函数调用来包装处理程序。这会首先调用 terribleSecurity 闭包，然后调用 RequestTimer，再调用实际处理请求逻辑的处理程序。

因为*http.ServeMux实现了http.Handler接口，所以可以将一组中间件应用于所有已经注册的单个请求的路由器：

```go
terribleSecurity := TerribleSecurityProvider("GOPHER")
wrappedMux := terribleSecurity(RequestTimer(mux))
s := http.Server{
    Addr: ":8080",
    Handler:	wrappedMux,		//使用自定义的handler
}
```



## 11.上下文

### 11.1 什么是上下文
