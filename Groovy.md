# Groovy

参考：

<https://github.com/ZhangQinglian/Cradle/tree/master/groovy-syntax> 

<https://blog.csdn.net/jeasonlzy/article/details/74273953>  ==正在看==

>Groovy 是一个基于 JVM 的语言，代码最终编译成字节码（bytecode）,并在 JVM 上运行。
>
>它具有类似于 Java 的语法风格，但是语法又比 Java 要灵活和方便，同时具有动态语言（如 ruby 和 Python）的一些特性



>使用Groovy可以使你的应用具备脚本，DSL定义，运行时和编译时元编程，函数式编程等功能。 
>
>1.可选类型（弱类型）：	groovy的弱类型让你不用再面对各种类型转换问题。 
>
>2.动态性 
>	单纯的java语言是不具有动态性的，groovy恰恰弥补了这一缺憾，有了groovy你可以在程序运行时任意	  
>
>​	修改代码逻辑，不需要重新发布.
>
>3.语法糖 
>
>​	groovy在语法上兼具java 语言和脚本语言特点，大大简化了语法。



> DSL 是一个面向特定小领域的语言，如常见的 HTML、CSS 都是 DSL，它通常是以配置的方式进行编程，与之相对的是通用语言（General Purpose Language），如 Java 等。



**在控制台可以使用return 方法执行的结果，返回null时，不显示**

**也可使用print/println在IDEA中打印**

## 语法：
### 1.基础：

Groovy的语法，大致上与Java类似：



区别：

​	弱类型

​	语句末尾可以不加分号

​	打印语句：println 



#### 注释：

Groovy的单行注释（`//`）、多行注释（`/*...*/`）、文档注释（`/**...*/`）基本都和Java一样 。但是groovy还有一种特殊的注释  `#!`,用来在UNIX系统声明允许脚本运行的类型的，一般都是固定写法， 

```groovy
#!/usr/bin/env groovy
println "Hello from the shebang line"
```

这类脚本注释主要用于表明脚本的路径。

#### 关键词：

groovy中的关键词基本上与java类似

```
as、assert、break、case、catch、class、const、continue、def、default、do、else、enum、extends、false、finally、for、goto、if、implements、import、in、instanceof、interface、new、null、package、return、super、switch、this、throw、throws、trait、true、try、while
```

其中：

​	`as` : 声明数组时使用：

```

```

​	`def`： 用来定义变量，方法

```
run();

def run(){
    //业务代码
}
```

#### 标识符：

命名规则和java相同，由字母，数字，下划线及$组成，但首字母不能是数字





### 2.数据类型:

* 区分大小写
* 在定义变量时可以通过 `def`关键字，也可以直接指明类型,也可以强转

```groovy
def a = 'A';
def a = 'A' as Character;  
LinkedList list = ['A','B','C'];
```

#### 强制类型转换：

```groovy
def c =  (char)'A' // 
def a = 1 as String

println c instanceof String // false
println c instanceof Character // true

println a instanceof String // true
println a instanceof Integer // false
```

`tips:`

​	**instanceof 右侧必须是引用类型**

#### 整型

​	相同

#### 浮点型

​	相同

####布尔型

​	相同

#### 字符

* **单引号下的字符是  `java.lang.String`类型**
* **单引号不能使用占位符，可以使用转义字符**

```groovy
// 占位符：
def name = 'jay'
def age = "18"
def man1 = "$name $age"  // 双引号
def man2 = '$name $age' // 单引号
----------
Result:
	 man1:jay 18  // 可使用占位符
	 man2:$name $age  // 不可使用占位符
******************************************
// 转义字符：
def a = 'a\n'
def b = "a\n"

println a
println b
--------------
result:
	a
		//换行
	a
		//换行
```

* Groovy没有明确的   `Characters`。但可通过以下三种方式来将字符串作为字符处理：

```groovy
char c1 = 'A'  				// 1.指明数据类型
println c1 instanceof Character
def c2 = 'B' as char  		// 2.使用as关键字
println c2 instanceof Character
def c3 = (char)'C' 			// 3.强制类型转换
println c3 instanceof Character
--------------
Result:
    true
	true
	true
```

#### 字符串

- Groovy的字符或字符串可以和java一样通过 "+"号连接

==单引号==：

​	groovy中单引号字符串对应 ==java中的String==

* 单引号不能使用占位符，可以使用转义字符

  ```groovy
  
  ```


==双引号==

如果双引号字符串中不包含 `占位符`则是  `java.lang.String` 类型的

如果双引号字符串中包含占位符则是`groovy.lang.GString` 类型的。 

==三引号==

三引号字符串同样对应java中的String，不支持动态插入。三引号字符串支持多行：  

```groovy

```

==**占位符**==

#### 数组

​	类似

```groovy
String[] a = ['A',2]; // 指定类型时，类型确定,该数组内的元素都是String

def b = ['A',2]; // def,类型不确定，数组内的元素类型视情况而定
def numArr = [1, 2, 3] as int[]   // 可通过as指定数组的类型,元素类型需要一致

println a[0] instanceof String // true
println a[1] instanceof Integer // false

println b[0] instanceof String // true
println b[1] instanceof Integer // true
```

#### 列表

​	groovy中的    `list `用法和java类似，允许在运行时改变列表的大小,用 ==[]==表示

​	**区别在于:**

​		1.groovy的list存储数据时**不区分数据类型**；

​		2.可通过   **超出列表范围的数**来索引列表 （`只适用于list[index]；而不适用于list.get(index)`）

​		3.==使用下标进行取值和赋值  ，不能使用get==

```groovy
LinkedList list = ['A',100,false];

println "列表的长度:"+list.size()
println list[-1]
println list[0]
println list[1]
println list[2]
println list.get(-1) //抛出java.lang.IndexOutOfBoundsException: Index: -1, Size: 3
---------------
Result:
	列表的长度:3
	false
	A
	100
	false
```



#### Map

```groovy
def map = ["a":1,b:"2"] // 第一个key是String;第二个是Integer

println map[b] // null 这种方式，不管key的类型如何，都要加 双引号
println map["b"] // 2
println map.a // 1
```



### 3.结构

#### 导包：

groovy默认导入一些常用的包：

```
import java.lang.*
import java.util.*
import java.io.*
import java.net.*
import groovy.lang.*
import groovy.util.*
import java.math.BigInteger
import java.math.BigDecimal
```

除此之外，都需要手动导入包



### 4.方法

==参数优化==

```groovy
groovy中定义的函数：
	- 有参，在调用的时可省略括号()。
	- 无参，不能省，否则会当成一个变量使用。
	
def fun(String a){
    println a
}
fun 'hello'
----------
result:hello
```

### 5.类

==类的property==

>Groovy中的class和java中的Class区别不大，值得我们关注的区别是，如果类的成员变量没有加任何权限访问，则称为Property, 否则是Field，filed和Java中的成员变量相同，但是Property的话，它是一个private field和getter setter的集合，也就是说groovy会自动生成getter setter方法，因此在类外面的代码，都是会透明的调用getter和setter方法
>



## groovy脚本和java类的关系：

==groovy脚本会被转换为一个继承`Script`的类，而且需要运行的代码被放在run方法中了。==