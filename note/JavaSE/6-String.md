# String

String 是 Java 中最常用的**引用类型**

## 一、定义

1. 定义：**String 代表字符串，由多个字符组成的字符序列**。
2. 所属包：java.lang
3. 类修饰符：**public final class String**

核心特性：

- **final 修饰类**：String 不能被继承
- **不可变性**：字符串一旦创建，内容无法修改，修改操作只会生成新字符串
- 底层存储：JDK 9 之前使用 `char[]` 存储字符，JDK 9 及之后优化为 `byte[]`，节省内存空间

## 二、String 的两种创建方式

### 1.直接赋值

```java
String str = "abc";
```

- JVM 先检查**字符串常量池**中是否存在 "abc"
- 存在：直接返回常量池中的对象地址**（复用常量池对象）**
- 不存在：在常量池中创建该字符串对象，再返回地址

### 2.New关键字创建

```java
String str = new String("abc");
```

执行原理：

- 先检查常量池是否有 "abc"，没有则在常量池创建
- 无论常量池是否存在，都会**在堆内存中新建一个 String 对象**，不存在常量池复用
- 变量 str 指向堆内存的对象，堆对象再指向常量池的字符串

### 3.两种方法的区别

```java
String s1 = "abc";
String s2 = new String("abc");
System.out.println(s1 == s2); // false
System.out.println(s1.equals(s2)); // true
```

- **==**：比较对象地址，s1 指向常量池，s2 指向堆内存，地址不同
- **equals()**：String 重写了该方法，只比较字符串内容

## 三、String 不可变性

### 1. 不可变定义

字符串对象**一旦初始化，内部字符序列无法被修改**，所有修改、拼接、替换操作，都不会改变原字符串，只会生成**新的 String 对象**。

### 2. 不可变原因

- 底层字符数组 `value` 被 **private final** 修饰
- private：外部无法直接访问数组
- final：数组引用地址不可修改，无法重新赋值数组
- String 内部未提供修改字符数组的方法，彻底杜绝修改可能

### 3. 代码验证不可变性

```java
String str = "hello";
str += " world";
System.out.println(str);
```

**原理：**代码看似修改了 str，实则原 "hello" 不变，JVM 新建 "hello world" 字符串，**让 str 指向新对象**，原对象等待垃圾回收。

### 4. 不可变性的优缺点

**优点**：

- 线程安全：不可变对象天然支持多线程，无需加锁
- 支持常量池复用，节省内存
- 哈希值固定，适合作为 HashMap、HashSet 的 key

**缺点**：

- 频繁字符串拼接、修改时，会产生大量临时垃圾对象，效率极低

## 四、String 常用核心方法

所有方法均不会修改原字符串，只会返回新字符串。

### 1. 获取长度与字符

- `length()`：返回字符串长度
- `charAt(int index)`：返回指定下标字符
- `indexOf(String str)`：返回子串首次出现下标，无则返回-1
- `indexOf(String str , int fromIndex)`：返回子串在fromIndex后面首次出现的下标，无则返回-1
- `lastIndexOf(String str)`：返回子串最后出现下标

### 2. 字符串判断

- `equals(Object obj)`：比较内容是否相同（区分大小写）
- `equalsIgnoreCase(String str)`：忽略大小写比较内容
- `isEmpty()`：判断字符串是否为空（长度为0）
- `startsWith(String prefix)`：判断是否以指定前缀开头
- `endsWith(String suffix)`：判断是否以指定后缀结尾
- `contains(CharSequence s)`：判断是否包含指定子串

### 3. 字符串截取与分割

- `substring(int beginIndex)`：从指定下标截取到末尾
- `substring(int begin, int end)`：左闭右开区间截取
- `split(String regex)`：根据正则表达式分割字符串，返回数组

### 4. 转换与替换

- `toLowerCase()`：转小写
- `toUpperCase()`：转大写
- `trim()`：去除首尾空格（中间空格保留）
- `replace(char old, char new)`：替换所有指定字符
- `replaceAll(String regex, String newStr)`：正则替换所有子串

### 5. 类型转换

- `toCharArray()`：字符串转字符数组
- `String.valueOf(任意数据类型)`：任意类型转字符串

