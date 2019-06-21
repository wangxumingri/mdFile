运算：



Math:









***

## Math



### 平方根：

`static  double  sqrt(double num)`  

```java
/*
  1.参数和返回值都是double
  2.传入负数结果为NaN
/
double num1 = Math.sqrt(9); // 3.0
double num2 = Math.sqrt(-9); // NaN 
```

### 次方：

`static double pow(double a, double b)`

```java
/** 
	* @param   a   the base 		---运算对象（正负都行）
	* @param   b   the exponent  	 ---n次方
	* @return  the value {@code a}<sup>{@code b}</sup>.
 */
public static double pow(double a, double b)

Math.pow(-3, 3) // -27.0
Math.pow(3, 3); // 次方：27.0 参数和返回值都是double
Math.pow(9, 1/2);// 次方：1.0 会用整数部分计算 参数和返回值都是double
```

### 随机数

`public static double random()`

值的范围==[0.0,1.0)==



### 四舍五入

`public static long round(double a)`

`public static int round(float a) `

### 向下取整

`public static double floor(double a)`

### 向上取整

`public static double ceil(double a)`

***

## 位移运算

<https://blog.csdn.net/zxc_user/article/details/78183097> 

```
左移运算符: num << bit 右边空出的位置补0，相当于 num * （bit的2次方）
右移运算符: num >> bit 左边空出的位，如果是正数则补0，若为负数则补0或1,相当于 num / (bit的2次方 )
```



```java

System.out.println(3 << 2); // 12
System.out.println(27 >> 3); // 3
System.out.println(10 << 4); // 160
```

`Tipes:`

**3(10) = 11（2）  将其左移2位：左移1 ---> 110 --->左移1 ---> 1100 = 12(10)**

```java
@Test
    public void test6(){
        int a = 12;
        int b = -12;
        int c = 0;
        // 返回整数的二进制字符串
        String binaryStr1 = Integer.toBinaryString(a);// 1100
        String binaryStr2 = Integer.toBinaryString(b);// 11111111111111111111111111110100
        String binaryStr3 = Integer.toBinaryString(c);// 0
        // 将n进制的字符串数字转换为整数
        int d = Integer.parseUnsignedInt(binaryStr1, 2);
        Assert.assertEquals(a, d); // true
}
```

