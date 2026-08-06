# SpringBoot - Ajax

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

### 2.3 核心对比总结

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

## 四、Axios 实战案例（SpringBoot 登录交互）

### 4.1 案例功能说明

实现登录表单**无刷新异步提交**，通过 Axios 发送 POST 请求与 SpringBoot 后端交互，根据后端返回结果，动态实现页面跳转或弹窗提示，全程不刷新整页。

### 4.2 完整前端代码

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

### 4.3 核心代码逐行解析

- **e.preventDefault()**：阻止表单默认的整页刷新提交行为，是实现 Ajax 局部无刷新交互的核心，不添加该代码会导致异步请求失效。
- **URLSearchParams**：参数格式化工具，自动将键值对参数转为后端可识别的 `application/x-www-form-urlencoded` 格式，适配 SpringBoot 普通参数、实体类参数接收。
- **axios.post(地址, 参数)**：发送 POST 异步请求，第一个参数为后端接口地址，第二个参数为请求参数。
- **then() 成功回调**：服务器正常响应后执行，通过 `resp.data` 获取后端返回的 JSON 数据，执行业务逻辑。
- **catch() 异常回调**：捕获网络中断、接口404、服务器500等异常，统一做错误提示，提升容错性。

## 五、补充开发注意事项

- Axios 所有请求均为**异步执行**，后续依赖接口返回数据的逻辑，必须写在 then 回调内部，否则会出现数据未获取到就执行代码的问题。
- SpringBoot 后端接口无需跳转页面，只需返回 JSON 格式结果（code 状态码、msg 提示信息、data 数据），由前端控制页面跳转和提示。
- CDN 引入 Axios 依赖网络，离线开发可下载 `axios.min.js` 本地引入，避免资源加载失败。
- 异步请求不会携带页面默认缓存，登录等场景需注意后端 Session、Token 校验配置。