# 基础
## 基础类型
在kotlin中，所有东西都是对象，在这个意义上讲，我们可以在任何变量上调用成员函数与属性。一些类型可以有特殊的内部展示－－例如，数字、字符以及布尔值可以在运行时表示为原生类型值，但是对于用户来说，它们看起来像普通的类。
### 数字

kotlin处理数字在某种程度接近java，但是并不完全相同。例如，对于数字没有隐式扩展转换（如java中int可以隐式转换为long）.


type|Bit width
---|---
double|64
float|32
long|64
int|32
short|16
byte|8

#### 字面常量
数值常量字面值有以下几种：

- 十进制：123 Long类型用大写的L标记：123L
- 十六进制：0x0F
- 二进制：0b00001011

注意：不支持八进制

kotlin同样支持浮点数的常规表示方法
- 默认double：123.5 123.5e10
- float 用f 或者 F 标记：123.5f

#### 数字字面值中的下划线
你可以使用下划线使数字常量更易读
```
  val oneMillion = 1_000_000
  val creditCardNumber=1234_5678_0987
  val socialSecurityNumber = 999_99_9999L
  val hexBytes = 0xFF_EC_DE_5E
  val bytes = 0b11010010_01101001_10010100_10010010
```

#### 表示方式

在java平台数字时物理存储为jvm的原生类型，除非我们需要一个可空的引用（如int?）或泛型。后者情况下会把数字装箱。

注意数字装箱不一定保留同一性
```
        val a:Int = 10000
        println(a === a)//输出true
        val b: Int? = a
        val c: Int? = a
        println(b === c)//输出false
 ```
另一方面，它保留了相等性：
```
        val a:Int = 10000
        println(a == a)//输出true
        val b: Int? = a
        val c: Int? = a
        println(b == c)//输出true
```
#### 显示转换
由于不同的表示方式，较小类型并不是较大类型的字类型。如果它们是的话，就会出现下述问题
```
// 假想的代码,实际上并不能编译:
val a: Int? = 1 // 一个装箱的 Int (java.lang.Integer)
val b: Long? = a // 隐式转换产生一个装箱的 Long (java.lang.Long)
print(b == a) // 惊!这将输出“false”鉴于 Long 的 equals() 会检测另一个是否也为 Long
```

所以相等性会在所有地方悄无声息地失去，更别说同一性啦。
因此较小的类型不呢呢个隐式转换为较大的类型。这意味着在不进行显示转换的情况下我们不能把byte赋值给一个int变量。

```
fun main() {
//sampleStart
val b: Byte = 1 // OK, 字面值是静态检测的
vali:Int=b// 错误 //sampleEnd
}
```

我们可以显示转换来拓展数字
```
fun main() {
    val b: Byte = 1
//sampleStart
val i: Int = b.toInt() // OK:显式拓宽 print(i)
//sampleEnd
}
```

每个数字类型支持如下的转换：
- toByte(): Byte
- toShort(): Short
- toInt(): Int
- toLong(): Long
- toFloat(): Float
- toDouble(): Double
- toChar(): Char

缺乏隐式类型转换很少会引起注意，因为类型会从上下文推断出来，而算术运算会有重载做适当转换，如：
```
 val l = 1L + 3 // Long + Int => Long
```

#### 运算
kotlin 支持数字运算的标准集，运算被定义为相应的类成员
这是完整的位运算列表
- shl(bits) - 有符号左移（java的<<)
- shr(bits) – 有符号右移 (Java 的 >> )
- ushr(bits) – 无符号右移 (Java 的 >>> )
- and(bits) – 位与
- or(bits) – 位或 
- xor(bits) – 位异或 
- inv() – 位非

#### 浮点数比较
本节讨论的浮点数操作如下：
- 相等性检测：a==b 与 a!=b
- 比较操作符：a<b a>b a<=b
- 区间实例以及区间检测：a..b x in a..b  x !in a..b
当其中的操作数a与b 都是静态已知的float 或double 或者他们对应的可空类型（声明为该类型，或者推断为该类型，或者智能类型转换的结果是该类型），两数字形成的操作或者区间遵循ieee754浮点运算标准。

然而，为了支持泛型场景并提供全序支持，当这些操作数并非静态类型为浮点数（例如是any、comparable<....>、类型参数）时，这些操作使用为float 与double实现的不符合标准的equals与compareto,这会出现：
- 认为nan与自身相等
- 认为nan比包括正大穷大在内的任何其它元素都大
- 认为－0.0小于0.0

### 字符
字符用char类型表示。它们不能直接当作数字
字符字面值用单引号括起来：‘1’。特殊字符可以用反斜杠转义。支持这几个转义序列:\t \b \n \r \' \" \\ 与\$.编码其它字符要用unicode转义序列语法‘\uff00’。
我们可以显示把字符转换为int 数字：
```
 fun decimalDigitValue(c: Char): Int {
        if (c !in '0'..'9') {
            throw IllegalArgumentException("out of range")  
        }
        return c.toInt() - '0'.toInt()
    }
```
当需要可空引用时，像数字、字符会被装箱，装箱操作不会保留同一性。
### 布尔
布尔用boolean类型表示，它有两个值：true和false
若需要可空引用布尔会被装箱。
内置的布尔运算有：
- || 短路逻辑或
- && 短路逻辑与
- ！ 逻辑非

### 数组
数组在kotlin中使用array类来表示，它定义了get与set函数以及size属性，以及一些其它有用的成员函数：
```
class Array<T> private constructor() {
    val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit
    operator fun iterator(): Iterator<T>
// ......
}
```
我们可以使用库函数arrayof()来创建一个数值并传递元素值给它，这样arrayof(1,2,3)创建了array[1,2,3]。或者库函数arrayofnulls()可以用于创建一个指定大小的，所有元素都为空的数组。
另一个选项是用接受数组大小以及一个函数参数的array构造函数，用作参数的函数能够返回给定索引的每个元素初始值：
```
fun main() {
//sampleStart
// 创建一个 Array<String> 初始化为 ["0", "1", "4", "9", "16"] 
val asc = Array(5, { i -> (i * i).toString() }) asc.forEach { println(it) }
//sampleEnd
}
```
如上所述, [] 运算符代表调用成员函数 get() 与 set() 。
注意: 与 Java 不同的是,Kotlin 中数组是不型变的(invariant)。这意味着 Kotlin 不让我们把 Array<String> 赋值给 Array<Any> ,以防止可能的运行时失败(但是你可以使用 Array<outAny> , 参见类型投影)。
Kotlin 也有无装箱开销的专门的类来表示原生类型数组: ByteArray 、
ShortArray 、 IntArray 等等。这些类与 Array 并没有继承关系,但是它们有同样的方法属
性集。它们也都有相应的工厂方法:
```
val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```
### 无符号整型
无符号类型自kotlin1.3起才可用，并且目前是实验性。
kotlin为无符号整数引入了以下类型：
- kotlin.ubyte:无符号8比特整数，范围0-255
- kotlin.ushort:无符号16比特整数，范围0-65535
- kotlin.uint:无符号32比特整数，范围0-2｀32-1
- kotlin.ulong:无符号64比特整数，范围0-2｀64-1

无符号类型支持其对应有符号类型的大多数操作。
请注意，将类型从无符号类型更改为对应的有符号类型是二进制不兼容变更｛:.note｝
无符号类型是使用另一个实验性特性(即内联类)实现的。

#### 特化的类
与原生类型相同，每个无符号类型都有相应的为该类型特化的表示数组的类型：
- kotlin.UByteArray : 无符号字节数组 
- kotlin.UShortArray : 无符号短整型数组 
- kotlin.UIntArray : 无符号整型数组 
- kotlin.ULongArray : 无符号长整型数组

与有符号整型数组一样，它们提供了类似于array类的api而没有装箱开销。
此外,区间与数列也支持 UInt 与 ULong (通过这些类 kotlin.ranges.UIntRange 、 kotlin.ranges.UIntProgression 、 kotlin.ranges.ULongRange 、 kotlin.ranges.ULongProgression )
#### 字面值
为使无符号整型更易于使用，kotlin提供了用后缀标记整型字面值来表示指定无符号类型(类似于float/long)：
- 后缀u与U将字面值标记为无符号。确切类型会根据预期类型确定。如果没有提供预期的类型，会根据字面值大小选择uint或者ulong

```
val b: UByte = 1u // UByte,已提供预期类型 
val s: UShort = 1u // UShort,已提供预期类型 
val l: ULong = 1u // ULong,已提供预期类型
val a1 = 42u // UInt:未提供预期类型,常量适于 UInt
val a2 = 0xFFFF_FFFF_FFFFu // ULong:未提供预期类型,常量不适于 UInt
```
- 后缀ul显式将字面值标记为无符号长整型。
```
val a = 1UL // ULong,即使未提供预期类型并且常量适于 UInt
```
#### 无符号整型的实验性状态
无符号类型的设计是实验性的,这意味着这个特性改进很快并且没有给出兼容性保证。当在 Kotlin 1.3+ 中使用无符号算术时,会报出警告表明这个特性是实验性的。如需移除警告,必 须选择加入(opt-in)无符号类型的实验性使用。
选择加入无符号整型有两种可行的方式:将 API 标记为实验性的,或者无需标记。
如需传播实验性,要么使用 @ExperimentalUnsignedTypes 标注使用了无符号整型的声 明,要么将 -Xexperimental=kotlin.ExperimentalUnsignedTypes 传给编译器(请注意,后 者会使所编译的模块内的所有声明都具实验性) 如需选择加入而不传播实验性,要么使用
@UseExperimental(ExperimentalUnsignedTypes::class) 注解标注声明,要么将 -Xuse- experimental=kotlin.ExperimentalUnsignedTypes 传给编译器
你的客户是否必须选择使用你的 API 取决于你,不过请记住,无符号整型是一个实验性特 性,因此使用它们的 API 可能会因语言的变更而发生突然破坏

### 字符串
字符串用string 类型表示。字符串式不可变的。字符串的元素－－字符可以使用索引运算符访问：s[i]。可以用for循环迭代字符串：
```
fun main() {
val str = "abcd"
//sampleStart
for (c in str) {
println(c) }
//sampleEnd
}
```
可以用＋操作符连接字符串，这也适用于连接字符串与其他类型的值，只要表达式中的第一个元素是字符串：
```
fun main() {
//sampleStart
val s = "abc" + 1
println(s + "def")
//sampleEnd
}
```
请注意，在大多数情况下，优先使用字符串模板或原始字符串而不是字符串连接。

#### 字符串字面值

kotlin有两种类型的字符串字面值：转义字符串可以有转义字符，以及原始字符串可以包含换行以及任意文本。转义字符串很像java字符串：
```
 val s = "Hello, world!\n"
```
转义采用传统的反斜杠方式。参见上面的字符查看支持的转义序列。
原始字符串使用三个引号(""")分界符括起来，内部没有转义并且可以包含换行以及任何其他字符：
```
val text = """
    for (c in "foo")
    print(c) 
"""
```
你可以通过trimmargin()函数去除前导空格：
```
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```
默认 | 用作边界前缀,但你可以选择其他字符并作为参数传入,比如 trimMargin(">") 。

#### 字符串模板
字符串可以包含模板表达式，即一些小段代码，会求值并把结果合并到字符串中。模板表达式以美元符($)开头，由一个简单的名字构成：
```
fun main() {
//sampleStart
val i = 10
println("i = $i") // 输出“i = 10” 
//sampleEnd
}
```
或者用花括号括起来的任意表达式：
```
fun main() {
//sampleStart
val s = "abc"
println("$s.length is ${s.length}") // “abc.length is 3”
 //sampleEnd
}
```
原始字符串与转义字符串内部都支持模板。如果你需要在原始字符串中表示字面值$字符(它不支持反斜杠转义，你可以用下列语法：
```
val price = """
${'$'}9.99
"""
```
## 包
源文件通常以包声明开头：
```
package foo.bar
fun baz() { ... }
class Goo { ... }
```
源文件所有内容(无论是类还是函数)都包含在声明的包内。 所以上例中 baz() 的全名是 foo.bar.baz 、 Goo 的全名是 foo.bar.Goo 。
如果没有指明包,该文件的内容属于无名字的默认包。
## 控制流：if when for while

### if表达式
在kotlin中，if是一个表达式，即它会返回一个值。因此就不需要三元运算符(条件？然后：否则)，因为普通的if就能胜任这个角色。
```
   var max = a
        if (a < b) max = b

        var max1: Int
        if (a > b) {
            max1 = a
        } else {
            max1 = b
        }

        val max2 = if (a > b) a else b
```
if 的分支可以是代码块,最后的表达式作为该块的值:
```
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```
如果你使用 if 作为表达式而不是语句(例如:返回它的值或者把它赋给变量),该表达式 需要有 else 分支。
### when表达式
when取代了类c语音的switch操作符。最简单的形式如下：
```
 when (a) {
            1 -> print("x==1")
            2 -> print("x==2")
            else ->{
                print("")
            }
        }
```
when将它的参数与所有分支条件顺序比较，直到某个分支满足条件。when既可以被当作表达式使用也可以被当做语句使用。如果它被当作表达式，符合条件的分支的值就是整个表达式的值，如果当做语句使用，则忽略个别分支的值。(像if一样，每一个分支可以式一个代码块，它的值是块中最后的表达式的值)

如果其他分支都不满足条件将会求值else分支。如果when作为一个表达式使用，则必须有else分支，除非编译器能够检测出所有可能情况都已经覆盖了。

如果很多分支需要用相同的方式处理，则可以把多个分支条件放在一起，用逗号隔开：
```
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```
我们可以用任意表达式(而不只是常量)作为分支条件
```
when (x) {
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
}
```
我们也可以检测一个值在(in)或者不在(!in)一个区间或者集合中：
```
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```
另一种可能性是检测一个值是(is)或者不是(!is)一个特定类型的值。注意：由于智能转换，你可以访问该类型的方法与属性而无需任何额外的检测。
```
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```
when也可以用来取代if－else if链。如果不提供参数，所有的分支条件都是简单的布尔表达式，而当一个分支的条件为真时，则执行该分支：
```
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
```
since kotlin1.3,it is possible to capture when subject in a variable using following syntax:
```
fun Request.getBody() =
        when (val response = executeRequest()) {
            is Success -> response.body
            is HttpError -> throw HttpException(response.status)
        }
```
Scope of variable,introduced in when subject,is restricted to when body.

## for循环
for循环可以对任何提供迭代器(iterator)的对象进行遍历，这相当于像c#这样的语言中foreach循环。语法如下：
```
for(item in collection) print(item)
```
循环体可以是一个代码块
```
for(item:int in ints){

}
```
如上所述，for可以循环遍历任何提供了迭代器的对象。即：
- 有一个成员函数或者扩展函数iterator(),它的返回类型
有一个函数或者扩展函数next()，并且有一个成员函数或者扩展函数hasnext()返回boolean。

这三个寒暑都需要标记为operator。
如需在数字区间上迭代，请使用区间表达式：
```
fun main() {
//sampleStart
for (i in 1..3) {
println(i) }
for (i in 6 downTo 0 step 2) {
    println(i)
}
//sampleEnd
}
```
对区间或者数组的for循环会被编译为并不创建迭代器的基于索引的循环。
如果你想要通过索引遍历一个数组或者一个list，你可以这么做：
```
fun main() {
val array = arrayOf("a", "b", "c")
//sampleStart
for (i in array.indices) {
    println(array[i])
}
//sampleEnd
}
```
或者你可以用库函数withindex：
```
fun main() {
val array = arrayOf("a", "b", "c")
//sampleStart
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
//sampleEnd
}
```





























