# 学习笔记

* [kotlin 学习笔记](#kotlin-学习笔记)
* [md 常用格式](#md-常用格式)

---
## kotlin 学习笔记 

kotlin是基于jvm的语言，由jetbrains开发，完全兼容java。
### 类相关

```kotlin
//类和属性默认是public
//注意下面是不同的
class Person(first:String){
    var a;
    init{
        //只能在init块里看到a，注意init前面没有fun
        a=first;
    }
}
//first为属性，可以任意位置读取；constructor 可省略
open class Person constructor (val first:String)
{
    //类和方法必须同时open才能复写
    open fun Call()
    {
        println("...");
    }
}
//接口方法可以有默认实现，接口不用open
interface IPeople{
    fun Call()
    {
        println("...");
    }
}
//子类继承必须初始化主构造函数
class Student(b:string):Person(b),IPeople
{
    //如果有主构造函数,次构造函数必须调用主构造函数
    constructor(b:int):this(b.toString())
    {
        
    }
    //必须显式的标注为override,final为禁止再次覆盖
    final override fun Call()
    {
        //用super<..>标识父类，加强版的base
        super<Person>.Call();
        super<IPeople>.Call();
    }
}

```

### 属性

```kotlin
//比c#重少了许多大括号
open var stringRepresentation: String
    get() = this.toString()
    private  set(value) {
        filed=5;//可以用filed关键字访问字段
    }
//只读属性可以自动推倒类型
val isEmpty get() = this.size == 0  // 具有类型 Boolean

val Foo=3
private set;//改变set可见性但是不改变默认实现
```

### 对象表达式

```kotlin
//这叫java匿名对象吧
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ……
    }

    override fun mouseEntered(e: MouseEvent) {
        // ……
    }
})
//c# 的感觉
val adHoc = object {
        var x: Int = 0
        var y: Int = 0
    }


class C {
    // 私有函数，所以其返回类型是匿名对象类型
    private fun foo() = object {
        val x: String = "x"
    }

    // 公有函数，所以其返回类型是 Any
    fun publicFoo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x        // 没问题
        val x2 = publicFoo().x  // 错误：未能解析的引用“x”
    }
}

//作用域
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ……
}
```
### 对象声明
*注意：对象声明不能在局部作用域（即直接嵌套在函数内部），但是它们可以嵌套到其他对象声明或非内部类中。*
```kotlin
//单例 有点像c#的纯静态类
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ……
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ……
}
//访问
DataProviderManager.registerDataProvider(...)
```
### 伴生对象(对象声明的一种特殊用法)
*注意：使用伴生对象就像静态方法。*
```kotlin
class MyClass {
    companion object {
        fun Foo();
    }
}
//使用时下面情况一样
MyClass.Foo();
MyClass.Companion.Foo();

```

### 代理类
``` kotlin

interface IOp
{
    fun print();
}
class BaseImp(val x:Int) : IOp
{
    override fun print(){
        println(x);
    }
}
//那么
class Derived(b:IOp) : IOp by b
{

}
//相当于
class Derived(b:IOp) : IOp 
{
    override fun print(){
        b.println(x);
    }
}

```

---

### 特性
``` kotlin
//open == c#中的 virual
//val == c#中的 readonly
//'field'关键字可以访问该属性的字段
// 编译时常量 注意有val 
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"
val a: Int = 1//不可变 会实现get,set访问器
val b = 1 //推导出Int型
val c: Int //当没有初始化值时必须声明 类型
c = 1 // 赋值
//使用字符串模板
print("First argument: ${args[0]}")
//if可作为表达式
fun max(a: Int,  b: Int) = if (a > b) a else b
//in操作符
if (text in names) //将会调用nemes.contains(text)方法
    print("Yes")

for (name in names)
    println(name)
//Range 
if (x in 1..y-1)
    print("OK")
if (x !in 0..array.lastIndex)
    print("Out")
//Lambda it可以作为简写形式
names
     .filter { x-> x.startsWith("A") }
     .sortedBy { it }
     .map { it.toUpperCase() }
     .forEach { print(it) }
//遍历 map/list
for ((k, v) in map) {
    print("$k -> $v")
}
//如果不为空则... 的简写
println(files?.size)
//如果不为空...否则... 的简写
println(files?.size ?: "empty")
//如果声明为空执行某操作
val email = data["email"] ?: throw IllegalStateException("Email is missing!")
//如果不为空执行某操作
val date = ...
data?.let{
    ...//如果不为空执行该语句块
}

//使用 when 表达式
fun cases(obj: Any) {
    when (obj) {
        1 -> print("one")
        "hello" -> print("Greeting")
        is Long -> print("Long")
        !is Long -> print("Not a string")
        else -> print("Unknown")
    }
}
//创建 DTO
data class Customer(val name:int =5,val email: String)
//利用 with 调用一个对象实例的多个方法
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double) 
    fun forward(pixels: Double)
}
val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0) 
    }
    penUp() 
}
```

---

### byte和int的值做==比较为false,必须进行显式转换，数值类型有toXX()的函数

---

### 'typealis' 可以定义类型映射

``` kotlin
typealias int = Int;
typealias Action<T> = (T)->Unit;
```

---

### 函数支持命名参数和默认参数
``` kotlin
//正常函数声明
fun double(x: Int): Int {

};
//无返回值可以省略Unit
fun print(x:Int){
    println(x.ToSting());
}
//单表达式参数可以省略返回类型和大括号
fun double(x: Int) = x * 2
//尾递归函数
tailrec fun findFixPoint(x: Double = 1.0): Double 
    = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))
//相当于
fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if ( x == y ) return y
        x = y
    }
}
```



---

## md 常用格式
1. #号加空格代表标题,6级
2. 段内换行为`两个空格加换行`
3. `包裹为强调,*包裹为加粗，还有**包裹,***包裹
4. 列表 `数字.`后面加空格有序列表,*或者-后加空格无序列表
5. ```加语言名字为code段
6. 引用为>,可多个代表级别，也可嵌套
7. 分割线用三个·- - -·
8. 超链接是方括号文字加小括号链接
9. 图片是叹号放括弧提示加小括号地址
10. 锚点是链接地址前加#，目的地是标题，注意用-代替空格
11. 表格见下图 
 

| 左对齐 | 居中  | 右对齐 |
| :-- |:---------------:| -----:|
| col 3 is      | some wordy text | $1600 |
| col 2 is      | centered        |   $12 |
| zebra stripes | are neat        |    $1 |



