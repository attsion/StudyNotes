# 学习笔记

- [kotlin 学习笔记](#kotlin-学习笔记)
- [Android MVP](#android-mvp)
- [md 常用格式](#md-常用格式)

---
# kotlin 学习笔记 

kotlin是基于jvm的语言，由jetbrains开发，完全兼容java。

## 特征用法
1. `!!`操作符转换为非空
2. `?:`操作符为空否则的意思 如：val l = b?.length ?: -1
3. `inline` 函数牺牲了闭包特性但是能增加性能。
4. 多重声明能拆分data对象和map `for ((k, v) in map)`
5. byte和int的值做==比较为false,必须进行显式转换，数值类型有toXX()的函数
6. `is` 和 `!is`操作符
7. `typealias` 类型映射 `typealias Action<T> = (T)->Unit;`
8. `open`相当于c#中的`virual` ,`val`相当于c#中的 `readonly`
9. 注意使用`IntArray`代替`Array`,注意`MutableList`可变和`List`为不可变
10. `field`关键字可以访问该属性的字段
11. 编译时常量 `const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"`
12. 字符串模板 `print("First argument: ${args[0]}")`
13. 注意`let` `apply` `with`的用法
14. `data`对象有copy方法，有componentN方法
15. 对象表达式代表一个匿名类或者对象
```kotlin
object:A(){...}
object{...}
```
16. 匿名对象代表全局单例对象 它总是在 object 关键字后跟一个名称,伴生对象可以省略名字;  
注意：对象声明不能在局部作用域（即直接嵌套在函数内部），但是它们可以嵌套到其他对象声明或非内部类中。
```kotlin
object className{...}
object className:farther(){...}
companion object  {...}
```
17. as 和 as?操作符
```kotlin
val x: String? = y as String?
//失败时返回null
val x: String? = y as? String
```
18. `infix`中缀表示法(用于DLS)
```kotlin
// 给 Int 定义扩展
infix fun Int.shl(x: Int): Int {
……
}
// 用中缀表示法调用扩展函数
1 shl 2
// 等同于这样
1.shl(2)
```
19. *前缀操作符
```kotlin
val a = array(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```
20. sealed类为枚举的扩展
```kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```
21. 要访问来自外部作用域的this（一个类 或者扩展函数， 或者带标签的带接收者的函数字面值）我们使用this@label，其中 @label 是一个 代指 this 来源的标签：
```kotlin
class A { // 隐式标签 @A
    inner class B { // 隐式标签 @B
        fun Int.foo() { // 隐式标签 @foo
            val a = this@A // A 的 this
            val b = this@B // B 的 this

            val c = this // foo() 的接收者，一个 Int
            val c1 = this@foo // foo() 的接收者，一个 Int

            val funLit = lambda@ fun String.() {
                val d = this // funLit 的接收者
            }


            val funLit2 = { s: String ->
                // foo() 的接收者，因为它包含的 lambda 表达式
                // 没有任何接收者
                val d1 = this
            }
        }
    }
}
```

## Lambda表达式和高阶函数
Lambda表达式=匿名函数
1. 最后一个Lambda可以移出小括号
2. 只有一个Lambda，小括号可省略
3. Lambda 只有一个参数可默认为 it
4. 入参、返回值与形参一致的函数可以用函数引用的方式作为实参传入

```kotlin
val lambda = { 
        left: Int, right: Int 
        -> 
        left + right 
    } 
    //调用
    println(lambda(2, 3)) 
    //等价
    println(lambda.invoke(2,3))


args.forEach({ 
    element -> println(element) 
})
//Kotlin 允许我们把函数的最后一个Lambda表达式参数移除括号外
args.forEach(){ 
    element -> println(element) 
}
//如果函数只有这么一个 Lambda 表达式参数()可以省略
args.forEach{ 
    element -> println(element) 
}
//如果传入的这个Lambda表达式只有一个参数,默认叫it
args.forEach{ 
     println(it) 
}
//如果这个 Lambda 表达式里面只有一个函数调用，并且这个函数的参数也是这个Lambda表达式的参数，那么你还可以用函数引用的方式简化上面的代码：
args.forEach(::println)


//lambda的返回
fun main(args: Array<String>) { 
    args.forEach forEachBlock@{ 
     	if(it == "q") return@forEachBlock 
     	println(it) 
    } 
    println("The End") 
}
//map=select;
//filter=where;
//takeWhile=takeWhile;
//flatmap
val list = listOf( 
        1..20, 
        2..5, 
        100..232 
) 
val flatList = list.flatMap { it }println(flatList)
//fold / reduce
val r1 = ints.fold(StringBuilder()){ 
    str, element-> 
    str.append(element).append(",") 
} 
println(r1)


val r2 = ints.reduce { sum, element -> sum + element } 
println(r2)

//let 注意it
person?.let{ 
	it.name = "张三" 
	it.age = 18 
	... 
}
//apply 和let很像只是改变了上下文this 并返回上下文
class Options{ 
    var scale: Float = 1f 
    var offsetX: Double = 0.0 
    var offsetY: Double = 0.0 
    var rotationX: Float = 0f 
    var rotationY: Float = 0f 
}
mapView.animateChange(Options().apply {  
	//Options 的作用域 
    scale = 2f 
    rotationX = 180f 
})

//with 只是作用上下文不会返回上下文
with(br){ 
    var line: String? 
    while (true){ 
        line = readLine()?: break 
    } 
    close() 
}
```
## 函数
注意inline函数可以增加性能但会牺牲作用域
```kotlin

//正常函数声明
fun funName(x: Int): Int {

};
//无返回值可以省略Unit
fun print(x:Int){
    println(x.ToSting());
}
//单表达式参数可以省略返回类型和大括号
fun funName(x: Int) = x * 2

//默认值
fun funName(x: Int=5): Int {
}
//匿名函数 只是名字省略
fun(x: Int): Int {

};
//恶心的匿名函数+单表达式函数(在单表达式函数基础上省略了函数名)
fun(x: Int) = x * 2


//覆盖方法总是使用与基类型方法相同的默认参数值。 当覆盖一个带有默认参数值的方法时，必须从签名中省略默认参数值
open class A {
    open fun foo(i: Int = 10) { …… }
}
class B : A() {
    override fun foo(i: Int) { …… }  // 不能有默认值
}
//命名调用
reformat(str,
    normalizeCase = true,
    upperCaseFirstLetter = true,
    divideByCamelHumps = false,
    wordSeparator = '_'
)
//可变参数
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
val list = asList(1, 2, 3);

//泛型函数*注意<T>位置*
fun <T> singletonList(item: T): List<T> {
    // ……
}
//局部函数
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v)
    }

    dfs(graph.vertices[0])
}

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

## 类相关

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

//嵌套类
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() //==2
//内部类  标记为 inner 这样就可以访问外部类的成员。内部类拥有外部类的一个对象引用
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}
val demo = Outer().Inner().foo() //==1


//匿名内部类 这叫java匿名对象吧
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ……
    }

    override fun mouseEntered(e: MouseEvent) {
        // ……
    }
})

```

## 属性

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

## 对象表达式

```kotlin

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
    fun public Foo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x        // 没问题
        val x2 = publicFoo().x  // 错误：未能解析的引用“x”
    }
}

//匿名类的作用域
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
## 对象声明
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
## 伴生对象(对象声明的一种特殊用法)
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



## 表达式区间
```kotlin
if (i in 1..10) { // 等同于 1 <= i && i <= 10
    println(i)
}
for (i in 1..4) print(i) // 输出“1234”
for (i in 4 downTo 1 step 2) print(i)

var ls=(1..5).toList();

```

## 多重声明
```kotlin
//对data对象
data People(name:string,age:int);
var people=People("jon",5);
val (name, age) = person
//对map
for ((key, value) in map) {

}
```

## 泛型
``` kotlin
//泛型类
class Box<T>(t: T) {
    var value = t
}
//泛型函数
fun <T> singletonList(item: T): List<T> {
    // ……
}
//泛型约束 两种形式皆可
fun <T : Comparable<T>> sort(list: List<T>) {
    // ……
}

fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
    where T : Comparable,
          T : Cloneable {
  return list.filter { it > threshold }.map { it.clone() }
}
```
Kotlin 为此提供了所谓的星投影语法：

- 对于 Foo <out T>，其中 T 是一个具有上界 TUpper 的协变类型参数，Foo <\*> 等价于 Foo <out TUpper>。 这意味着当 T 未知时，你可以安全地从 Foo <\*> 读取 TUpper 的值。
- 对于 Foo <in T>，其中 T 是一个逆变类型参数，Foo <\*> 等价于 Foo <in Nothing>。 这意味着当 T 未知时，没有什么可以以安全的方式写入 Foo <\*>。
- 对于 Foo <T>，其中 T 是一个具有上界 TUpper 的不型变类型参数，Foo<\*> 对于读取值时等价于 Foo<out TUpper> 而对于写值时等价于 Foo<in Nothing>。  

如果泛型类型具有多个类型参数，则每个类型参数都可以单独投影。 例如，如果类型被声明为 interface Function <in T, out U>，我们可以想象以下星投影：

- Function<*, String> 表示 Function<in Nothing, String>；
- Function<Int, *> 表示 Function<Int, out Any?>；
- Function<*, *> 表示 Function<in Nothing, out Any?>。  

注意：星投影非常像 Java 的原始类型，但是安全。


## 代理类
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
## 延迟属性 Lazy

lazy() 是接受一个 lambda 并返回一个 Lazy <T> 实例的函数，返回的实例可以作为实现延迟属性的委托： 第一次调用 get() 会执行已传递给 lazy() 的 lamda 表达式并记录结果， 后续调用 get() 只是返回记录的结果。
``` kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}
```
## 把属性储存在映射中

一个常见的用例是在一个映射（map）里存储属性的值。 这经常出现在像解析 JSON 或者做其他“动态”事情的应用中。 在这种情况下，你可以使用映射实例自身作为委托来实现委托属性。
``` kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
//在这个例子中，构造函数接受一个映射参数：

val user = User(mapOf(
    "name" to "John Doe",
    "age"  to 25
))
//委托属性会从这个映射中取值（通过字符串键——属性的名称）：

println(user.name) // Prints "John Doe"
println(user.age)  // Prints 25
//这也适用于 var 属性，如果把只读的 Map 换成 MutableMap 的话：

class MutableUser(val map: MutableMap<String, Any?>) {
    var name: String by map
    var age: Int     by map
}
```


---

# Android MVP
## 1.View的职责

- 该操作需要什么？（getUserName, getPassword）
- 该操作的结果，对应的反馈？(toMainActivity, showFailedError)
- 该操作过程中对应的友好的交互？(showLoading, hideLoading)



---

# md 常用格式
1. #号加空格代表标题,6级
2. 段内换行为`两个空格加换行`
3. `包裹为强调,*包裹为加粗，还有**包裹,***包裹
4. 列表 `数字.`后面加空格有序列表,*或者-后加空格无序列表
5. 三个`加语言名字为code段
6. 引用为>,可多个代表级别，也可嵌套
7. 分割线用三个·- - -·
8. 超链接是方括号文字加小括号链接
9. 图片是叹号方括弧提示加小括号地址
10. 锚点是链接地址前加#，目的地是标题，注意用-代替空格
11. 表格见下图 
 

| 左对齐 | 居中  | 右对齐 |
| :-- |:---------------:| -----:|
| col 3 is      | some wordy text | $1600 |
| col 2 is      | centered        |   $12 |
| zebra stripes | are neat        |    $1 |



