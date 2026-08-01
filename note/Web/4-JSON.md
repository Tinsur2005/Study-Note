# JSON

## 一、JSON

### 1.1 JSON定义

**定义**：是一种轻量级的数据交换格式

JSON是 **JavaScript Object Notation** 的缩写

**特点**：

- 易于程序员阅读和编写
- 易于计算机解析和生成
- 是 JavaScript 的子集，原生 JavaScript 直接支持 JSON

原生JS对象示例代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        var student = {
            name: 'zhansgan',
            age: 23,
            study: function() {
                console.log(this.name + '的年龄是' + this.age + '岁');
            }
        };
        console.log(student.name);
        student.study();
    </script>
</head>
<body>
</body>
</html>
```

JSON是一种**与语言无关**的数据交换格式，不受编程语言限制，主流开发语言均支持解析与生成JSON数据。

**核心作用**：

1. 用于 AJAX 前后端异步数据交换
2. 移动端（App、小程序等）与服务端的数据交互

### 1.2 JSON数据格式

JSON语法规则简洁规范，核心包含两种基础结构，语法严谨、可读性强。

**两种核心结构：**

1. **对象格式**：`{"key1":obj1, "key2":obj2, "key3":obj3...}`
2. **数组/集合格式**：`[obj1,obj2,obj3...]`

**核心语法规则**：

- JSON 的 key（键）必须是**字符串类型**，需用双引号包裹
- JSON 的 value（值）支持任意合法数据类型
- 元素值合法类型：string、number、object、array、true、false、null

#### 1. 对象格式（Object）

使用大括号 `{}` 包裹，以键值对形式存储数据

```Plain
{
    "id" : 12,
    "name" : "java1711",
    "age" : 20,
    "gender" : "男"
}
```

#### 2. 数组格式（Array）

使用方括号 `[]` 包裹，可存储多个对象或基础数据，常用格式：`[{},{},{}]`

```Plain
[
    {
        "id" : 12,
        "name" : "java1711",
        "age" : 20,
        "gender" : "男"
    },
    {
        "id" : 13,
        "name" : "java1712",
        "age" : 21,
        "gender" : "女"
    }
]
```

## 二、JSON浏览器调试插件（FeHelper）

### 2.1 插件介绍

FeHelper 是一款开源、长期维护的浏览器开发者超级工具箱，适配 Chrome、Edge、Firefox 主流浏览器，内置专业的 JSON 处理工具，是前端开发调试 JSON 数据的核心工具。

### 2.2 插件地址

官方地址：https://www.baidufe.com/fehelper

测试JSON接口地址：https://layui.itze.cn/demo/table/user.json?page=2&limit=10（该接口偶现系统内部异常，可作为调试测试地址）

### 2.3 核心JSON相关功能

- **JSON自动美化**：自动识别网页JSON数据，格式化排版，解决压缩JSON、乱码、嵌套转义问题，支持BigInt无损解析
- **JSON内容比对**：支持两段JSON数据差异对比，高亮展示不同字段，快速排查数据变更
- **JSON纠错与生成**：自动修复错误JSON格式，可基于JSON样例生成TypeScript、JSON Schema规范代码
- **辅助功能**：支持JSON数据排序、手动编辑、文件下载、自定义皮肤，适配开发调试全场景

### 2.4 插件基础使用技巧

- 默认快捷键：`Alt+Shift+J`，可快速唤起工具面板，通过方向键选择功能、回车确认使用
- 支持自定义工具排序、右键快捷菜单、夜间模式，适配个人使用习惯
- 所有JSON处理操作默认本地完成，无需联网，高效安全