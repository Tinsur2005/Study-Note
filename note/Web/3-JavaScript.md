# JavaScript

## 一、JS 简介

### 1.1 JS 运行特点

- 可直接嵌入 HTML 页面中运行
- 由浏览器解释执行代码，**无预编译过程**

### 1.2 语言类型对比

**JS**：解释型语言、弱类型语言

**Java**：编译型语言、强类型语言

核心区别示例：

- Java（强类型）：`int num = 100;`（声明时指定数据类型）
- JS（弱类型）：`var num = 100;`（声明不指定类型，赋值决定类型）

### 1.3 变量命名规范

- 变量名**区分大小写**
- 合法字符：字母、数字、美元符号(`$`)、下划线(`_`)
- 命名禁忌：首字符不能为数字，不能包含空格、其他标点符号
- 命名原则：见名知意，使用语义化名称

### 1.4 字符串规则

JS 中单引号、双引号作用完全一致，无使用区别。

```javascript
<script type="text/javascript">
    var num1 = 3;
    var num2 = 5;
    var result = num1 + num2;
    console.log(result);//8

    // 数字与字符串拼接
    var str = '10';
    console.log(num1 + str);//310（字符串拼接）

    // 全局函数 parseInt 字符串转数字
    var result2 = num1 + parseInt(str);
    console.log(result2);//13（数值运算）
    alert(result2);
</script>
```

## 二、JS 数据类型

### 2.1 介绍

- **undefined：**变量 age 没有初始值，将被赋予 undefined

```javascript
var age;
```

- **null：**表示一个空值，与 undefined 值相等

- **number：**整数和浮点数

```javascript
var score=90;  //整数
var score=96.5; //浮点数
```

- **boolean：**布尔型：true 和 false

- **string：**被引号（单引号或双引号）括起来的文本

```javascript
var string1="This is a string";
```

> - 统一使用 `let`或者`let`声明变量，声明阶段变量无类型
> - **变量的类型由赋值的值决定**，赋值后确定数据类型

### 2.2 null 与 undefined 区别

- **undefined**：变量已声明，但**未初始化、未赋值**，系统默认值
- **null**：变量已手动赋值，赋值内容为空值，代表空对象

```javascript
<script type="text/javascript">
    // 仅声明未赋值，默认值 undefined
    var v;
    console.log(v);//undefined
    console.log(typeof(v));//undefined

    // 赋值数字类型
    v = 3.14;
    console.log(v);//3.14
    console.log(typeof(v));//number

    // 赋值字符串类型
    v = 'js';
    console.log(v);//js
    console.log(typeof(v));//string

    // 赋值布尔类型
    v = true;
    console.log(v); //true
    console.log(typeof(v));//boolean

    // 赋值 null（特殊，typeof 检测为 object）
    v = null;
    console.log(v); //null
    console.log(typeof(v));//object
</script>
```

### 2.4 使用 let 替换 var

早期 JS 仅支持 **var** 声明变量，存在**变量提升、无块级作用域、可重复声明**的缺陷，容易引发代码污染、逻辑bug。ES6 新增 **let** 声明方式，完美解决上述问题：

- **块级作用域**：let 仅在当前代码块（{}）内生效，避免全局变量污染
- **禁止重复声明**：同一作用域内不可重复声明同名变量，规避变量覆盖问题
- **无变量提升**：必须先声明后使用，代码逻辑更严谨规范

因此现代 JS 开发统一使用 let/const 替代 var，下文第三节及后续内容全部采用 `let` 声明变量。

## 三、关系运算符

- `==`（相等）：仅判断**数值/内容**是否一致，忽略类型
- `===`（全等）：同时判断**内容 + 数据类型**完全一致
- `!==`（不全等）：全等取反，内容或类型不一致即为 true

```javascript
<script type="text/javascript">
    let m = '3'; // 字符串类型
    let n = 3;   // 数字类型

    if (m == n) {
       console.log('m==n') // 执行：内容相等，忽略类型
    }
    if (m === n) {
       console.log('m===n') // 不执行：类型不同
    }
</script>
```

## 四、String 字符串

### 4.1 字符串创建方式

- 字面量创建（常用）：`let str1 = 'hello js!';`
- 对象创建：`let str2 = new String('hello js!');`

### 4.2 核心属性

`length`：获取字符串长度

示例：`'hello js!'.length; // 9`

### 4.3 常用方法

#### （1）大小写转换

- `toLowerCase()`：全部转为小写
- `toUpperCase()`：全部转为大写

```javascript
let str = 'Hello jS!';
console.log(str.toLowerCase());//hello js!
console.log(str.toUpperCase());//HELLO JS！
```

#### （2）获取指定位置字符

`charAt(index)`：根据索引获取对应字符，索引从 0 开始

示例：`console.log(str.charAt(6));//j`

#### （3）索引查询

- `indexOf(findstr, [index])`：从前往后查找，返回首个匹配索引，无匹配返回 -1
- `lastIndexOf(findstr, [index])`：从后往前查找，返回首个匹配索引，无匹配返回 -1
- 参数：findstr=查找字符串，index=可选，指定起始查找位置

#### （4）字符串分割

`split(separator, howmany)`：将字符串分割为数组

- separator：分割依据（字符串/正则）
- howmany：可选，限制返回数组最大长度

```javascript
<script type="text/javascript">
    let str1 = 'Hello JS and Java';
    console.log(str1.indexOf('J'));//6
    console.log(str1.indexOf('JS'));//6
    console.log(str1.indexOf('JS', 9));//-1

    let str2 = 'how are you doing today';
    let array = str2.split(' ');
    console.log(array);// 按空格分割的数组
    console.log(array[0]);//how
    console.log('1:2:3:4'.split(':'));//["1","2","3","4"]
</script>
```

## 五、Boolean 布尔值

### 5.1 核心特性

- Java 中条件语句（if/while）必须传入布尔表达式
- **JS 中所有表达式均可作为条件**，自动隐式转换布尔值

### 5.2 隐式转换规则

- **false 值（空值）**：0、空字符串`''`、null、undefined、NaN
- **true 值（非空值）**：非0数字、非空字符串、任意有效对象

```javascript
<script type="text/javascript">
    console.log('-----------------')
    if (true) {
       console.log('true'); // 执行
    }
    if (0) {
       console.log('0'); // 不执行（0为false）
    }
    if (!0) {
       console.log('!0'); // 执行（!0为true）
    }
    if ('') {
       console.log('空字符串'); // 不执行（空串为false）
    }
    if ('abc') {
       console.log('abc'); // 执行（非空串为true）
    }
</script>
```

## 六、Array 数组

### 6.1 数组创建方式

```javascript
// 1. 创建空数组
let array1 = new Array();
// 2. 创建指定长度空数组
let array2 = new Array(7);
// 3. 创建带初始值的数组
let array3 = new Array(100, 'a', true);
// 4. 字面量创建（最常用）
let array4 = [100, 200, 300];
```

### 6.2 核心属性

`length`：获取数组元素个数，可动态变化

### 6.3 常用操作方法

- `push()`：数组**末尾添加**元素，返回新长度
- `pop()`：数组**末尾删除**元素，返回删除的元素

```javascript
<script type="text/javascript">
    let array1 = new Array(7);
    console.log(array1.length);//7

    let array2 = new Array(100, 'abc', true);
    console.log(array2.length);//3

    // 下标赋值
    let array = new Array();
    array[0] = '234';
    console.log(array);//['234']
    console.log(array.length);//1
    array[1] = false;
    console.log(array);//['234', false]
    console.log(array.length);//2

    // push、pop 操作
    let arr = new Array();
    arr.push('zhangsan');
    console.log(arr);//['zhangsan']
    arr.push(false);
    arr.push(45);
    console.log(arr);//['zhangsan', false, 45]

    console.log(arr.pop());//45
    console.log(arr.pop());//false
    console.log(arr.pop());//zhangsan
</script>
```

## 七、Math 数学工具

Math 是 JS 内置数学对象，无需手动创建，直接调用属性/方法

- `parseInt()`：全局方法，字符串转整数
- `Math.PI`：圆周率常量
- `Math.round(num)`：四舍五入取整

示例：`Math.round(3.14); // 3`

## 八、eval 动态执行函数

`eval()`：可解析并执行**字符串格式的 JS 代码**

```javascript
console.log(eval('2+3')) //5
eval('let x=10;let y=20;console.log(x*y);'); //200
```

## 九、JS 自定义对象

JS 支持自定义对象，以键值对形式存储属性和方法

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>自定义对象</title>
    <script>
        // 自定义学生对象
        let student = {
            name: 'zhansgan',
            age: 23,
            // 对象方法，this 指向当前对象
            study: function() {
                console.log(this.name + '的年龄是' + this.age + '岁');
            }
        };
        // 调用对象属性
        console.log(student.name);
        // 调用对象方法
        student.study();
    </script>
</head>
<body>
</body>
</html>
```

## 十、DOM 文档对象模型

### 10.1 DOM 概念

DOM（Document Object Model）：文档对象模型，是 **JS 与 HTML 标签交互的桥梁**，可实现对 HTML 元素的增、删、改、查。

### 10.2 HTML 节点分类

- 文档节点：document（整个页面）
- 元素节点：HTML 标签（核心操作对象）
- 文本节点：标签内的文本内容
- 属性节点：标签的属性（id、class、value 等）
- 注释节点：页面注释内容

### 10.3 常用元素获取方法

- `document.getElementById('id值')`：根据 id 获取单个元素
- `document.getElementsByTagName('标签名')`：根据标签名获取元素集合

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>DOM 基础操作</title>
</head>
<body>
    <input type="text" id="inputId" value="Hello"> <br>
    <input type="button" value="点我" onclick="clickMe()">

    <script>
        function clickMe() {
            // 根据标签名获取元素集合
            let array = document.getElementsByTagName('input');
            console.log(array);
            console.log(array[0]);
            console.log(array[0].value);

            // 根据 id 获取单个元素
            let inputObj = document.getElementById('inputId');
            console.log(inputObj.value);
        }
    </script>
</body>
</html>
```

### 10.5 示例：简易购物计算器

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>简易计算器</title>
</head>
<body>
<table border="1" cellpadding="5" cellspacing="0">
    <tr>
        <td><img src=""/></td>
        <td colSpan="3">购物简易计算器</td>
    </tr>
    <tr>
        <td>第一个数</td>
        <td colSpan="3">
            <input type="text" id="num1Id"/>
        </td>
    </tr>
    <tr>
        <td>第二个数</td>
        <td colSpan="3">
            <input type="text" id="num2Id"/>
        </td>
    </tr>
    <tr>
        <td>
            <input type="button" value=" + " onclick="cal('+')"/>
        </td>
        <td>
            <input type="button" value=" - " onclick="cal('-')"/>
        </td>
        <td>
            <input type="button" value=" * " onclick="cal('*')"/>
        </td>
        <td>
            <input type="button" value=" / " onclick="cal('/')"/>
        </td>
    </tr>
    <tr>
        <td>计算结果</td>
        <td colSpan="3">
            <input type="text" id="resultId"/>
        </td>
    </tr>
</table>

<script type="text/javascript">
    // 方式1：eval 动态计算
    function cal(type) {
        let num1 = document.getElementById('num1Id').value;
        let num2 = document.getElementById('num2Id').value;
        let result = eval(parseInt(num1) + type + parseInt(num2));
        document.getElementById('resultId').value = result;
    }

    // 方式2：switch 分支计算
    function cal1(type) {
        let num1 = parseInt(document.getElementById('num1Id').value);
        let num2 = parseInt(document.getElementById('num2Id').value);
        let result = 0;
        switch (type) {
            case '+': result = num1 + num2; break;
            case '-': result = num1 - num2; break;
            case '*': result = num1 * num2; break;
            case '/': result = num1 / num2; break;
        }
        document.getElementById('resultId').value = result;
    }

    // 方式3：单独函数计算
    function add() {
        let num1 = parseInt(document.getElementById('num1Id').value);
        let num2 = parseInt(document.getElementById('num2Id').value);
        document.getElementById('resultId').value = num1 + num2;
    }
    function sub() {
        let num1 = parseInt(document.getElementById('num1Id').value);
        let num2 = parseInt(document.getElementById('num2Id').value);
        document.getElementById('resultId').value = num1 - num2;
    }
    function mul() {
        let num1 = parseInt(document.getElementById('num1Id').value);
        let num2 = parseInt(document.getElementById('num2Id').value);
        document.getElementById('resultId').value = num1 * num2;
    }
    function div() {
        let num1 = parseInt(document.getElementById('num1Id').value);
        let num2 = parseInt(document.getElementById('num2Id').value);
        document.getElementById('resultId').value = num1 / num2;
    }
</script>
</body>
</html>
```

## 十一、innerText 与 innerHTML

### 11.1 核心区别

- **innerText**：纯文本解析，内容仅作为字符串展示，HTML 标签不生效
- **innerHTML**：HTML 解析，内容中的标签会被渲染生效

### 11.2 示例代码

```html
<!DOCTYPE html>
<html>
    <head>
       <meta charset="utf-8">
       <title>innerText 与 innerHTML</title>
    </head>
    <body>
       <span id="spanId">Hello Span</span><br/>
       <button onclick="clickMe()">点击</button>
       
       <script type="text/javascript">
          function clickMe() {
             let spanObj = document.getElementById('spanId');
             // innerText：直接显示标签文本
             // spanObj.innerText = '<h1 style="color: red;">innerText</h1>';
             // innerHTML：解析并渲染 h1 标签样式
             spanObj.innerHTML = '<h1 style="color: red;">innerHTML 生效</h1>';
          }
       </script>
    </body>
</html>
```

## 十二、onload 页面加载事件

### 12.1 作用

JS 代码依赖 HTML、CSS 节点，若代码提前执行会出现节点未加载、获取不到的问题。`onload` 可保证 **页面所有内容加载完成后再执行 JS 代码**。

### 12.2 语法

```javascript
window.onload = function() { 
    // 页面加载完成后执行的代码
}
```

## 十三、节点创建与追加

### 13.1 常用节点创建方法

- `document.createElement(标签名)`：创建元素节点
- `document.createTextNode(文本内容)`：创建文本节点
- `node.setAttribute(属性名, 属性值)`：给节点设置属性

### 13.2 常用节点操作方法

- `父节点.appendChild(子节点)`：末尾追加子节点
- `父节点.insertBefore(新节点, 旧节点)`：在旧节点前插入新节点
- `父节点.replaceChild(新节点, 旧节点)`：新节点替换旧节点

```html
<!DOCTYPE html>
<html>
    <head>
       <meta charset="utf-8">
       <title>节点创建与追加</title>
    </head>
    <body>
       <script type="text/javascript">
          window.onload = function() {
             let colors = ['red', 'green', 'blue'];
             let colorMeans = ['热情', '冷静', '生机'];
             // 创建 ul 父节点
             let ul = document.createElement('ul');

             // 循环创建 li 子节点
             for (let i = 0; i < colors.length; i++) {
                let li = document.createElement('li');
                li.setAttribute('mean', colorMeans[i]);
                let textNode = document.createTextNode(colors[i]);
                li.appendChild(textNode);
                ul.appendChild(li);
             }
             // 将 ul 追加到页面 body 中
             document.body.appendChild(ul);
          }
       </script>
    </body>
</html>
```

## 十四、页面点击事件

### 14.1 事件概念

事件：用户对页面的鼠标、键盘操作（点击、悬浮、输入等）；事件驱动：事件触发后执行的自定义函数。

### 14.2 常用事件

`onclick`（点击）、`onmouseover`（鼠标悬浮）、`onmouseout`（鼠标离开）、`onkeyup`（按键抬起）、`onkeydown`（按键按下）、`onblur`（失去焦点）、`onfocus`（获取焦点）、`onsubmit`（表单提交）

### 14.3 两种事件绑定方式

#### 方式1：标签内直接绑定

```html
<input id="btnId" type="button" value="Press Me" onclick="clickMe()" >
<script>
    function clickMe(){
        alert('标签绑定事件触发');
    }
</script>
```

#### **方式2：JS 动态绑定**

```javascript
<script>
let btn = document.getElementById('btnId');
btn.onclick = function () {
    alert('JS 动态绑定事件触发');
}
</script>
```