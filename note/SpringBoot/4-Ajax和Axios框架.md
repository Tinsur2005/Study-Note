# SpringBoot：Ajax和Axios框架

## 一、Ajax 核心概述

### 1.1 什么是 Ajax

Ajax 全称 **Asynchronous JavaScript and XML（异步 JavaScript 和 XML）**，并非全新编程语言，而是一套**基于 JavaScript 实现前后端异步数据交互的技术方案**。

Ajax 核心作用：在**不刷新整个网页**的前提下，实现浏览器与服务器的数据通信，局部更新页面内容，解决传统表单提交整页刷新、页面卡顿、用户体验差的问题。

基础特性补充：

- 技术载体：依托原生 JavaScript 实现，无需依赖其他框架
- 交互模式：同时支持**同步、异步**两种请求方式
- 数据载体：早期以 XML 为主，现代开发已全面替换为 JSON 格式
- 核心优势：局部刷新、非阻塞交互、按需请求数据，减少服务器带宽消耗

### 1.2 Ajax 适用场景

日常开发中所有**无需整页跳转、需要局部更新数据**的场景，均适合使用 Ajax：

- 表单无刷新提交（登录、注册、修改信息）
- 输入框实时校验（用户名、手机号查重）
- 分页、下拉加载更多、模糊搜索
- 动态刷新页面数据（列表、统计数据实时更新）

## 二、同步请求与异步请求

同步、异步是 Ajax 最核心的两种交互模式，核心区别在于**请求发送后是否阻塞浏览器后续操作**，也是前后端交互的基础核心知识点。

### 2.1 同步请求（Synchronized）

**核心定义**：浏览器发送请求后，会**阻塞当前页面所有操作**，暂停一切交互行为，必须等待服务器处理完成并返回响应后，才能继续执行后续代码和页面操作。

通俗理解：排队执行，上一个任务（请求）没做完，下一个任务只能等待。

**优缺点**：

- 优点：执行顺序可控，不会出现数据错乱、请求覆盖问题
- 缺点：页面阻塞卡顿，用户体验极差，现代开发极少使用

### 2.2 异步请求（Asynchronous）

**核心定义**：浏览器发送请求后，**不会阻塞页面**，无需等待服务器响应，可直接继续执行后续操作；服务器处理完成后，通过回调机制通知前端，完成局部数据更新。

通俗理解：异步并行，发送请求后浏览器自主做其他事，服务器响应后再单独处理结果。

**优缺点**：

- 优点：非阻塞、页面流畅、用户体验好、效率高
- 缺点：请求执行顺序不可控，多请求并发时需处理数据覆盖问题

### 2.3 总结

Ajax 主流使用**异步请求**，这也是现代前端页面流畅交互的核心基础；同步请求仅用于极少数需要强制顺序执行的特殊场景。

## 三、Axios 框架入门

### 3.1 Axios 简介

原生 Ajax 语法繁琐、冗余代码多、需要手动处理兼容、回调嵌套混乱，不适合实际项目开发。因此行业内普遍使用**Axios** 替代原生 Ajax。

**Axios** 是一款**基于 Promise 的轻量级网络请求库**，是对原生 Ajax 的高度封装，完美适配浏览器和 Node.js 环境，具备语法简洁、兼容性强、可扩展性高的特点，是 SpringBoot 前后端分离项目的主流请求工具。

官方地址：https://www.axios-http.cn

### 3.2 Axios 核心特性

- 基于 Promise 语法，支持链式调用，告别回调地狱
- 支持 GET、POST、PUT、DELETE 等所有 HTTP 请求方式
- 自动转换 JSON 数据，无需手动解析
- 自带请求、响应拦截器，便于统一处理请求头、异常
- 支持请求取消、超时配置、跨域处理

### 3.3 Axios 引入方式

前端页面可直接通过 CDN 引入，无需下载本地文件，开箱即用：

```html
<!-- Axios 在线CDN引入 -->
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

### 3.4 基础语法格式

Axios 常用请求语法，基于 Promise 的 then/catch 回调处理结果：

```javascript
// GET请求（查询数据）
axios.get('请求地址')
.then(res => {
    // 请求成功逻辑
})
.catch(err => {
    // 请求失败逻辑
})

// POST请求（提交数据）
axios.post('请求地址', 请求参数)
.then(res => {
    // 请求成功逻辑
})
.catch(err => {
    // 请求失败逻辑
})
```

## 四、登录操作

实现登录表单**无刷新异步提交**，通过 Axios 发送 POST 请求与 SpringBoot 后端交互，根据后端返回结果，动态实现页面跳转或弹窗提示，全程不刷新整页。

> 在 [第一节：SpringBoot入门](https://note.tinsur.cn/#/note/SpringBoot/1-SpringBoot入门) 中提到了 `student_list.html` 页面和前后端交互实现在前端展示学生列表，接下来我们在原有学生列表的基础上进行修改。自本节开始逐步将先前的学生列表逐步改造成一个完善的学生管理系统Demo。

### 4.1 前端

```html
<!-- 登录表单页面 -->
<form id="loginForm">
    <label for="name">用户名</label>
    <input type="text" id="name" name="name"
           placeholder="请输入用户名" required autofocus/>
    <br>
    <label for="password">密码</label>
    <input type="password" id="password" name="password"
           placeholder="请输入密码" required/>
    <br>
    <input type="submit" value="登录"/>
</form>

<!-- 引入Axios -->
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>

<script>
    // 监听表单提交事件
    document.getElementById('loginForm').addEventListener('submit', function (e) {
        // 1. 阻止表单默认同步刷新提交（核心关键）
        e.preventDefault();

        // 2. 获取页面表单输入数据
        var name = document.getElementById('name').value;
        var password = document.getElementById('password').value;

        // 3. 组装请求参数：适配SpringBoot后端普通参数接收
        // URLSearchParams 自动设置请求头为 application/x-www-form-urlencoded
        var params = new URLSearchParams();
        params.append('name', name);
        params.append('password', password);

        // 4. 发送Axios异步POST请求
        axios.post('/user/login', params)
            .then(function (resp) {
                // 获取后端返回的JSON数据
                var res = resp.data;
                // 业务逻辑判断
                if (res.code === 1) {
                    // 登录成功：跳转学生列表页面
                    location.href = '/student/selectAll';
                } else {
                    // 登录失败：弹窗提示错误信息
                    alert(res.msg);
                }
            }).catch(function (err) {
                // 捕获网络异常、服务器报错等问题
                alert('请求失败，请稍后重试');
            });
    });
</script>
```

### 4.2 后端

```java
@RequestMapping(value = "/login", method = {RequestMethod.POST})
@ResponseBody
public Result login(String name, String password,HttpSession session){
    Result result = new Result();
    User user = userService.login(name,password);
    if(user == null){
        //result.setCode(Result.ERROR);
        //result.setMsg("用户名或密码错误");
        //return result;
        return Result.error("用户名或密码错误");
    }else {
        session.setAttribute("user",user);
        //result.setCode(Result.OK);
        //result.setMsg("登录成功");
        //return result;
        return Result.ok("登录成功");
    }
}
```

### 4.3 后端工具类

新建一个包 `util` ，写入 `Result` 工具类：

```java
package cn.tinsur.springboot.util;
// 请求是成功还是失败：ok error
// 后台希望前台弹出消息：msg
// 后台返回前台数据：Data
// JSON格式的通用响应对象，封装的就是后台返回给前台的所有信息
public class Result<T> {
    public static final int ERROR = 0;
    public static final int OK = 1;

    // 当前状态（程序员判断状态）:成功、失败、未登录、没有权限
    // 当前登录是成功还是失败要告诉前台，前台才能知道弹出的提示框用errorMsg、okMsg
    private Integer code;
    // 描述信息（主要是给用户看的提示信息）
    private String msg;
    // 后台返回给前端的数据 Object， User、List<User>、Map
    private T data;


    public Result() {
    }
    
    public Result(Integer code) {
       this.code = code;
    }

    public Result(Integer code, String msg) {
       this.code = code;
       this.msg = msg;
    }
    
    public Result(Integer code, T data) {
       this.code = code;
       this.data = data;
    }

    public Result(Integer code, String msg, T data) {
       this.code = code;
       this.msg = msg;
       this.data = data;
    }
    
    // 告诉前台成功：code
    public static Result ok() {
       return new Result(OK);
    }
    
    // 告诉前台成功：code、msg
    public static Result ok(String msg) {
       return new Result(OK, msg);
    }
    
    // 告诉前台成功：code、data
    public static Result ok(Object data) {
       return new Result(OK, data);
    }
    
    // 告诉前台成功：code、msg、data
    public static Result ok(String msg, Object data) {
       return new Result(OK, msg, data);
    }
    

    // 告诉前台成功：code
    public static Result error() {
       return new Result(ERROR);
    }
    
    // 告诉前台成功：code、msg
    public static Result error(String msg) {
       return new Result(ERROR, msg);
    }

    public Integer getCode() {
       return code;
    }

    public void setCode(Integer code) {
       this.code = code;
    }

    public String getMsg() {
       return msg;
    }

    public void setMsg(String msg) {
       this.msg = msg;
    }

    public T getData() {
       return data;
    }

    public void setData(T data) {
       this.data = data;
    }
    
}
```

## 五、Axios四大请求方式（RESTful 规范）

### 4.1 基础语法对照表

| 请求方法                              | 用途   | 完整语法     | 参数说明                        |
| :-------------------------------- | :--- | :------- | :-------------------------- |
| axios.get(url[, config])          | 查询数据 | 查列表 / 单条 | 无请求体 data，参数放 config.params |
| axios.delete(url[, config])       | 删除数据 | 删除记录     | 无请求体 data，参数放 config.params |
| axios.post(url[, data[, config]]) | 新增数据 | 添加记录     | data 为请求体 body，传递表单 / JSON  |
| axios.put(url[, data[, config]])  | 更新数据 | 修改记录     | data 为请求体 body，传递完整实体 JSON  |

重点区分：

1. `get/delete` **没有第二个 data 参数**，参数只能通过`config.params`拼接在 URL；
2. `post/put` 第二个参数是`data`，存请求体 body 数据。

### 4.2 Axios 的 config

**Axios 的 config 理解成请求配置对象，里面放的是请求控制信息：**

```json
{
    params: {},       // URL参数
    headers: {},      // 请求头
    timeout: 5000,    // 超时
}
```

### 4.3 请求体 data 两种主流格式

请求体格式由前端传递的数据类型决定，SpringBoot 接收方式不同：

#### 1. 表单格式

即`application/x-www-form-urlencoded`，**上文中的登录功能即使用表单格式进行请求**。

- 来源：原生 HTML form 表单默认格式；
- 前端构造：`new URLSearchParams()` 拼接键值对；
- 数据格式：`name=张三&age=23&gender=男`；
- 后端接收：直接定义普通参数 `String name, Integer age`。

#### 2. JSON

即`application/json`，前后端分离主流方式

- 来源：直接传递 JS 普通对象，Axios 自动执行`JSON.stringify`；
- 数据格式：`{"name":"张三","age":23,"gender":"男"}`；
- 后端接收：必须添加注解 `@RequestBody 实体类`。

下面使用第二种方式来实现上面的登录功能：

##### 前端实现

前端相对于上文中的使用表单格式进行请求的前端HTML，:warning:**仅有对params进行赋值有区别**：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div th:if="${session.errorMsg}" th:text="${session.errorMsg}" style="color:red;"></div>
    <form id="loginForm" >
        <label for="name">用户名</label>
        <input type="text" id="name" name="name"
               placeholder="请输入用户名" required autofocus/>
        <br>
        <label for="password">密码</label>
        <input type="password" id="password" name="password"
               placeholder="请输入密码" required/>
        <br>
        <input type="submit" value="登录"/>
    </form>
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    <script>
        document.getElementById("loginForm").addEventListener("submit", function (e) {
            e.preventDefault();
            let name = document.getElementById("name").value;
            let password = document.getElementById("password").value;

            let params = {//仅仅在这里有区别
                name: name,
                password: password
            }
            axios.post("/user/login", params).then(function (resp){
                let response = resp.data;
                if(response.code === 1){
                    alert(response.msg)
                    window.location.href = "/student/selectAll";
                } else {
                    alert(response.msg)
                }
            }).catch(function (error) {
                console.log(error);
                alert("出现错误")
            })

        })
    </script>
</body>
</html>
```

##### 后端实现

:warning:**后端实现要注意login方法的参数变化**

```java
@RequestMapping(value = "/login", method = {RequestMethod.POST})
@ResponseBody
public Result login(@RequestBody User loginUser, HttpSession session){
    User user = userService.login(loginUser.getName(),loginUser.getPassword());
    if(user == null){
        return Result.error("用户名或密码错误");
    }else {
        session.setAttribute("user",user);
        return Result.ok("登录成功");
    }
}
```
