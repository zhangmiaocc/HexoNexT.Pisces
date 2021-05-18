---
title: 代码实例展示Java和Kotlin有哪些区别
tags:
  - Android
  - Kotlin
categories:
  - Android
  - Kotlin
abbrlink: 24470fc
date: 2021-05-17 18:06:47
---

自从谷歌 I/O 2017宣布将支持 Kotlin 作为 Android 开发的 First-Class 语言，相信各位程序员的朋友圈都被Kotlin的消息轰炸了吧，支持Java的“守旧派”认为Java将稳坐霸主地位不动摇，支持Kotlin的“维新派”认为Kotlin很可能会把Java拉下马。其实，笔者认为新语言的出现对于程序员来说并不是一件新鲜事儿，程序员始终践行着“活到老，学到老”，真正能够对程序员产生影响的新语言的使用和性能。

## 易用性。

1. 在语法糖的加持下，kotlin能够用更短的代码实现更多的功能。这是java 无法比拟的。所谓代码量越少，出bug的可能性就越低。
2. kotlin特有的扩展属性，不再需要java工具类，对开发更加友好。
    比如我们想实现字符串判空操作，在java中需要写一个StringUtil类，这样其他开发同学想实现该功能的时候，可能并不知道已经有人实现了该功能，存在重复造轮子的可能。通过扩展属性，我们可以很方便的看出String类都存在哪些方法。
3. java中的bean类总是需要使用插件生成setter getter方法。kotlin中的data关键字可以解决这类问题，data类在编译期自动生成getter setter方法。

## 规范性。

工程项目都是需要指定开发规范的。比如变量命名等。在java中，方法重载的时候会生成@Override注解，但是这并不是强制约束的。kotlin的方法采用override关键字进行了强制约束。
 再比如TODO。java中的TODO是以注释的形式存在，即使没有实现TODO处的代码，也没什么运行时问题。kotlin的TODO形式如下：

```kotlin
fun main(args: Array<String>) {
    TODO()
}
```

kotlin TODO的实现

```kotlin
@kotlin.internal.InlineOnly
public inline fun TODO(): Nothing = throw NotImplementedError()
```

可以看到运行时会抛出异常。
 思考：这里为什么不在编译期抛出异常呢？

## 安全性。

java中虽然有@Nullable @NotNull等注解。但是不会在编译期起作用，而是在运行期抛出异常。kotlin在声明变量的时候，可以指定变量是否为空，调用可为空对象时，需要进行空判断。

## 跨平台。

java在jvm的协助下虽然也是跨平台语言，但是不能像kotlin一样可以既可以编译成class字节码，又可以编译成js。

## 编译速度。

只有全量编译下，kotlin比java慢。增量编译下，两者几乎没有差距。

## 其他kotlin特性。

懒加载、高阶函数、协程、inline操作符、运算符重载、默认参数等。

GitHub 用户amitshekhariitbhu在GitHub上贴图[分享了](https://github.com/MindorksOpenSource/from-java-to-kotlin/blob/master/README.md)Java和Kotlin的语法区别，下面我们就一起来看一下吧!

<!--more-->


## 打印日志


```java
// java
System.out.print("Hello Word");
System.out.println("Hello Word");
```

```kotlin
// Kotlin
print("Hello Word")
println("Hello Word")
```


## 定义变量和常量

```java
// java
String name = "Hello Word";
final String name = "Hello Word";
```

```kotlin
// Kotlin
var name = "Hello Word"
val name = "Hello Word"
```

## null声明

```java
// java
String otherName;
otherName = null;
```

```kotlin
// kotlin 
var otherName : String?
otherName = null
```

## 空判断

```java
// java
if (text != null) {
  int length = text.length();
}
```


```kotlin
// kotlin 
text?.let {
    val length = text.length
}
```


## 字符串拼接

```java
// java
String firstName = "Android";
String lastName = "ZM";
String message = "My name is: " + firstName + " " + lastName;
```

```kotlin
// kotlin 
val firstName = "Android"
val lastName = "ZM"
val message = "My name is: $firstName $lastName"
```


## 换行

```java
// java
String text = "First Line\n" +
              "Second Line\n" +
              "Third Line";
```

```kotlin
// kotlin 
val text = """
        |First Line
        |Second Line
        |Third Line
        """.trimMargin()
```


## 三元表达式

```java
// java
String text = x > 5 ? "x > 5" : "x <= 5";
```

```kotlin
// kotlin 
val text = if (x > 5)
              "x > 5"
           else "x <= 5"
```

## 操作符

```java
// java
final int andResult = a & b;
final int orResult = a | b;
final int xorResult a ^ b;
final int rightShift = a >> 2;
final int leftShift = a << 2;
final int unsignedRightShift = a >>> 2;
```

```kotlin
val andResult = a and b;
val orResult = a or b;
val xorResult a xor b;
val rightShift = a shr 2;
val leftShift = a shl 2;
val unsignedRightShift = a ushr 2;
```

## 类型判断和转换(声明式)

```java
// java
Car car = (Car) object;
```

```kotlin
// kotlin 
var car = object as Car
```


## 类型判断和转换(隐式)

```java
// java
if (object instanceof Car) {
   Car car = (Car) object;
}
```

```kotlin
// kotlin 
if (object is Car) {
   var car = object // smart casting
}
```


## 多重条件

```java
// java
if (score >= 0 && score <= 300) { }
```


```kotlin
// kotlin 
if (score in 0..300) { }
```

## 更灵活的case语句

```java
// java
int score = // some score;
String grade;
switch (score) {
	case 10:
	case 9:
		grade = "Excellent";
		break;
	case 8:
	case 7:
	case 6:
		grade = "Good";
		break;
	case 5:
	case 4:
		grade = "Ok";
		break;
	case 3:
	case 2:
	case 1:
		grade = "Fail";
		break;
	default:
	    grade = "Fail";				
}
```


```kotlin
// kotlin 
var score = // some score
var grade = when (score) {
	9, 10 -> "Excellent" 
	in 6..8 -> "Good"
	4, 5 -> "Ok"
	in 1..3 -> "Fail"
	else -> "Fail"
}
```


## for循环

```java
// java
for (int i = 1; i <= 10 ; i++) { }

for (int i = 1; i < 10 ; i++) { }

for (int i = 10; i >= 0 ; i--) { }

for (int i = 1; i <= 10 ; i+=2) { }

for (int i = 10; i >= 0 ; i-=2) { }

for (String item : collection) { }

for (Map.Entry<String, String> entry: map.entrySet()) { }
```

```kotlin
// kotlin 
for (i in 1..10) { }

for (i in 1 until 10) { }

for (i in 10 downTo 0) { }

for (i in 1..10 step 2) { }

for (i in 10 downTo 1 step 2) { }

for (item in collection) { }

for ((key, value) in map) { }
```


## 更方便的集合操作

```java
// java
final List<Integer> listOfNumber = Arrays.asList(1, 2, 3, 4);

final Map<Integer, String> keyValue = new HashMap<Integer, String>();
map.put(1, "Amit");
map.put(2, "Ali");
map.put(3, "Mindorks");

// Java 9
final List<Integer> listOfNumber = List.of(1, 2, 3, 4);

final Map<Integer, String> keyValue = Map.of(1, "Amit",
                                             2, "Ali",
                                             3, "Mindorks");
```


```kotlin
// kotlin 
val listOfNumber = listOf(1, 2, 3, 4)
val keyValue = mapOf(1 to "Amit",
                     2 to "Ali",
                     3 to "Mindorks")
```


## 遍历

```java
// java
// Java 7 and below
for (Car car : cars) {
  System.out.println(car.speed);
}

// Java 8+
cars.forEach(car -> System.out.println(car.speed));

// Java 7 and below
for (Car car : cars) {
  if (car.speed > 100) {
    System.out.println(car.speed);
  }
}

// Java 8+
cars.stream().filter(car -> car.speed > 100).forEach(car -> System.out.println(car.speed));
```

```kotlin
// kotlin
cars.forEach {
    println(it.speed)
}

cars.filter { it.speed > 100 }
      .forEach { println(it.speed)}
```


## 方法定义

```java
// java
void doSomething() {
   // logic here
}
void doSomething(int... numbers) {
   // logic here
}
```


```kotlin
fun doSomething() {
   // logic here
}
fun doSomething(vararg numbers: Int) {
   // logic here
}
```


## 带返回值的方法

```java
// java
int getScore() {
   // logic here
   return score;
}
```


```kotlin
fun getScore(): Int {
   // logic here
   return score
}

// as a single-expression function

fun getScore(): Int = score
```

## 无结束符号

```java
// java
int getScore(int value) {
    // logic here
    return 2 * value;
}
```

```kotlin
fun getScore(value: Int): Int {
   // logic here
   return 2 * value
}

// as a single-expression function

fun getScore(value: Int): Int = 2 * value
```

## constructor构造器

```java
// java
public class Utils {

    private Utils() { 
      // This utility class is not publicly instantiable 
    }
    
    public static int getScore(int value) {
        return 2 * value;
    }
    
}
```

```kotlin
// kotlin
class Utils private constructor() {

    companion object {
    
        fun getScore(value: Int): Int {
            return 2 * value
        }
        
    }
}

// other way is also there

object Utils {

    fun getScore(value: Int): Int {
        return 2 * value
    }

}
```


## Get Set构造器

```java
// java
public class Developer {

    private String name;
    private int age;

    public Developer(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Developer developer = (Developer) o;

        if (age != developer.age) return false;
        return name != null ? name.equals(developer.name) : developer.name == null;

    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }

    @Override
    public String toString() {
        return "Developer{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```


```kotlin
// kotlin
data class Developer(var name: String, var age: Int)
```


## 类方法

```java
// java
public class Utils {

    private Utils() { 
      // This utility class is not publicly instantiable 
    }
    
    public static int triple(int value) {
        return 3 * value;
    }
    
}

int result = Utils.triple(3);
```

```kotlin
// kotlin
fun Int.triple(): Int {
  return this * 3
}

var result = 3.triple()
```

## 枚举
```java
// java
public enum Direction {
        NORTH(1),
        SOUTH(2),
        WEST(3),
        EAST(4);

        int direction;

        Direction(int direction) {
            this.direction = direction;
        }

        public int getDirection() {
            return direction;
        }
    }
```

```kotlin
// kotlin
enum class Direction(val direction: Int) {
    NORTH(1),
    SOUTH(2),
    WEST(3),
    EAST(4);
}
```

## 定义未初始化的对象
```java
// java
Person person;
```

```kotlin
// kotlin
internal lateinit var person: Person
```

## 排序
```java
// java
List<Profile> profiles = loadProfiles(context);
Collections.sort(profiles, new Comparator<Profile>() {
    @Override
    public int compare(Profile profile1, Profile profile2) {
        if (profile1.getAge() > profile2.getAge()) return 1;
        if (profile1.getAge() < profile2.getAge()) return -1;
        return 0;
    }
});
```

```kotlin
// kotlin
val profile = loadProfiles(context)
profile.sortedWith(Comparator({ profile1, profile2 ->
    if (profile1.age > profile2.age) return@Comparator 1
    if (profile1.age < profile2.age) return@Comparator -1
    return@Comparator 0
}))
```

## 匿名类
```java
// java
 AsyncTask<Void, Void, Profile> task = new AsyncTask<Void, Void, Profile>() {
    @Override
    protected Profile doInBackground(Void... voids) {
        // fetch profile from API or DB
        return null;
    }

    @Override
    protected void onPreExecute() {
        super.onPreExecute();
        // do something
    }
};
```

```kotlin
// kotlin
val task = object : AsyncTask<Void, Void, Profile>() {
    override fun doInBackground(vararg voids: Void): Profile? {
        // fetch profile from API or DB
        return null
    }

    override fun onPreExecute() {
        super.onPreExecute()
        // do something
    }
}
```

## 初始化块
```java
// java
public class User {
    {  //Initialization block
        System.out.println("Init block");
    }
}
```

```kotlin
// kotlin
   class User {
        init { // Initialization block
            println("Init block")
        }
    }
```
