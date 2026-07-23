# Java介绍

## 一、Java介绍

![image-20260721204438224](./../assets/1-Java%E4%BB%8B%E7%BB%8D/image-20260721204438224.png)

### JVM、JDK、JRE

Java的程序跑在**JVM（Java虚拟机）**上，Java通过JVM实现跨平台能力，各系统的JVM关系如下图所示：

![image-20260721204441399](./../assets/1-Java%E4%BB%8B%E7%BB%8D/image-20260721204441399.png)

**JDK，**即Java Delelopment Kit，**Java开发工具包**

**JRE，**即Java Runtime Environment，**Java运行时环境**

```JVM、JDK、JRE```三者的关系如下图所示：

![image-20260721204444739](./../assets/1-Java%E4%BB%8B%E7%BB%8D/image-20260721204444739.png)

## 二、关键字、标识符（变量）

### 1、关键字

**关键字**，即Java保留有特殊用途个关键字，如 `public、static、void` 等

### 2、标识符（变量）

变量，英文Variable

标识符（变量）的命名规范：

1. 不能用关键字

2. 由 **字母、数字、下划线、$** 组成，但是**不能以数字****开头**

命名变量要做到 **见名知意 **，Java中一般采用 **小驼峰命名** 

定义变量时，在前方加上final标识符，则相当于定义了一个**常量**

```
final flout PI = 3.1415926535 ;
```

## 三、基本数据类型

![image-20260721204447689](./../assets/1-Java%E4%BB%8B%E7%BB%8D/image-20260721204447689.png)

### 1、基本数据类型

Java中有**八个**基本数据类型，包括**byte、short、int、long、float、double、char、boolean**，其中**byte、short、int、long**属于数值型，**float和double**属于浮点类型，**char**是字符型，**boolean**是布尔型。

在计算机中，一个字节是**8个比特位（8bit）**，每个比特位都有0和1两种状态

#### （1）数值型

**byte：**1个字节，每一个字节不考虑负数能表示的范围是**0~255**，考虑正负的表示范围：**-128~127**

**short：**2个字节，16位，能表示的范围**0~65535**

**int：**4个字节，考虑正负可以表示**-21亿~21亿**

**long：**8个字节

#### （2）浮点类型

**float：**4个字节

**double：**8个字节

#### （3）boolean

**boolean：** 布尔类型，只有true和false两种情况

#### （4）char

**char：**2个字节，采用的是Unicode编码，可以存储汉字

每个字符在**ASCII**编码表里面都对应一个**int**整数

### 2、数据类型转换

数据类型转换原则：

1. 从**小到大**自动类型转换

2. 从**大到小**需要强制类型转换，可能会**丢失精度**
3. 从小到大的顺序为：`byte -> short(char) -> int -> long -> float -> double`
4. **char类型**可以当做一个**整数**来使用

```java
	char ch = 'a';
	System.out.println(ch);//a
	System.out.println(ch+1);//98
	System.out.println((char)(ch+1));//b
	System.out.println((char)(ch+2));//c
```

### 3、运算符

#### （1）基本运算符

加\+	减\-	乘\*	除/	取余%

#### （2）单目运算符

单目运算符包括**自增（++）**和**自减（--）**

如：i++，i--，++i，--i

单独使用的时候**++**和**--**放在前面和后面**没有**本质的区别，

被使用的时候，++和--放在前面，则**先自增（或自减）再使用**，++和--放在后面，则**先使用，再自增（或自减）**。

```java
    public void test1(){
        int i = 1;
        i++;//2
        i--;//3
        System.out.println(i++);//3
        System.out.println(++i);//5
    }
```

#### （3）比较运算符

**\>	<	>=	<=	!=**

比较之后的结果只有两种情况：**true、false**，比较运算的结果是boolean类型。

#### （4）逻辑运算符

**与&&	或||	非！**

逻辑运算符运算结果也是**boolean类型**。

逻辑运算**与运算&&和或运算||**过程中会出现**短路**的情况，只要通过**前半段表达式**就能确**定整个式子最终结果**，就**不会**再执行后半段代码。

与 & 和 | 不同, & 和 | 不会出现短路，即使**前半段表达式**就能确**定整个式子最终结果**，后半段代码**仍然执行**。单个的 **&** 和 **|** 还可以进行**位运算**，这是后面的内容。

```java
    public void test2(){//短路演示
        int num1 = 3;
        int num2 = 5;
        //&&短路
        System.out.println(num1 < 0 && num1++ < num2);//false
        System.out.println(num1);//3
        System.out.println(num2);//5

        //||短路
        System.out.println(num1 > 0 || num1++ < num2);//true
        System.out.println(num1);//3
        System.out.println(num2);//5
    }
```



