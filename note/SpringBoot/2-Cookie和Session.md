# SpringBoot - Cookie和Session

## 一、会话技术核心概述

### 1.1 什么是会话？

用户打开浏览器访问网站，从**第一次发送请求**开始，到**关闭浏览器结束访问**的全过程，称为一次**会话（Session）**。

HTTP 协议是**无状态协议**，每次请求都是独立的，服务器无法识别多次请求是否来自同一个用户。会话技术的核心作用就是**记录用户状态、存储用户会话数据**，解决 HTTP 无状态问题。

### 1.2 会话技术分类

- **客户端会话技术：Cookie**：数据存储在**浏览器（客户端）**本地
- **服务端会话技术：Session**：数据存储在**服务器**内存中，依赖 Cookie 实现会话关联

## 二、Cookie

### 2.1 概念

Cookie 是服务器发送给浏览器的**少量键值对数据**，浏览器接收后会本地保存，下次访问同一服务器时，会自动携带该 Cookie 发送请求，服务器以此识别用户。

### 2.2 特点

- 存储位置：客户端浏览器本地
- 数据大小：有限制，单个 Cookie 最大 4KB，单个域名最多存储 50 个 Cookie
- 数据类型：仅支持**字符串**，无法存储对象、数组等复杂数据
- 安全性：较低，数据保存在本地，可被查看、篡改、删除
- 生命周期：支持临时（默认）和持久化存储
- 跨域限制：遵循同源策略，仅当前域名可访问

### 2.3 Cookie 核心属性

| 属性     | 作用说明                                                     |
| :------- | :----------------------------------------------------------- |
| name     | Cookie 名称，唯一标识，不可修改                              |
| value    | Cookie 存储的值，字符串类型                                  |
| maxAge   | 生命周期（秒）：正数=持久化、负数=浏览器关闭失效、0=立即删除 |
| path     | Cookie 生效路径，默认当前请求路径，/ 代表全站生效            |
| domain   | Cookie 生效域名，默认当前域名，可实现子域名共享              |
| secure   | 为 true 时，仅 HTTPS 请求可携带该 Cookie                     |
| httpOnly | 为 true 时，禁止 JS 读取 Cookie，防止 XSS 攻击（核心安全配置） |

### 2.4 Cookie应用

#### 1. Cookie的设置和获取

```java
@Controller
@RequestMapping("/cookie")
public class CookieController {
    @RequestMapping("/setCookie")
    @ResponseBody //加上这个如果返回值是String，则只把字符串返回，不再转发页面
    public String setCookie(HttpServletResponse response){
        Cookie cookie = new Cookie("goods","IPhone");
        cookie.setPath("/");
        cookie.setMaxAge(60*60*24);
        response.addCookie(cookie);
        return "ok";
    }

    @RequestMapping("/getCookie")
    @ResponseBody
    public String getCookie(HttpServletRequest request){
        Cookie[] cookies = request.getCookies();
        for (Cookie cookie : cookies) {
            System.out.println(cookie.getName() + ":" + cookie.getValue());
        }

        return "ok";
    }
}
```

#### 2. 获取Cookie的另一种写法

```java
@RequestMapping("/getCookie2")
@ResponseBody
public String getCookie2(@CookieValue("goods") String value){
    return value;
}
```

## 三、Session 详细详解

### 3.1 Session 概念

Session 是**服务端会话技术**，服务器为每个独立用户创建一个专属 Session 对象，用于存储用户会话数据。

**底层原理**：用户第一次请求服务器时，服务器创建 Session 并生成唯一 SessionID，通过 Cookie 发送给浏览器保存；后续用户请求自动携带该 Cookie，服务器通过 SessionID 匹配对应会话，识别用户身份。

### 3.2 Session 核心特点

- 存储位置：服务器内存中
- 数据大小：无严格限制，支持存储对象、集合、字符串等所有数据类型
- 安全性：较高，数据仅存服务端，客户端仅存储 SessionID
- 生命周期：依赖会话超时机制，默认闲置30分钟失效
- 唯一性：每个用户对应唯一 SessionID

### 3.3 Session 的使用

SpringBoot 默认集成 Servlet Session，无需额外引入依赖，直接使用 `HttpSession` 即可。

#### 1. 存入 Session 数据

```java
@RequestMapping("/login")
public String login(String name, String password , Model model, HttpSession httpSession){
    User user = userService.login(name,password);
    if(user == null){
        //model.addAttribute("errorMsg","用户名或密码错误");
        httpSession.setAttribute("errorMsg","用户名或密码错误");
        return "redirect:/user/toLogin";
    }
    return "redirect:/student/selectAll";
}
```

#### 2. 获取 Session 数据

> 这里首先演示一下在**拦截器**中获取Session的用法

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws IOException {
        HttpSession session = request.getSession();
        User user = (User) session.getAttribute("user");
        if(user == null){
            response.sendRedirect("/user/toLogin");
            return false;
        }
        return true;
    }
}
```

> 然后演示一下普通用法

```java
public String getSession(HttpSession session) {
    // 根据key获取数据
    String username = (String) session.getAttribute("username");
    Integer userId = (Integer) session.getAttribute("userId");
    return "Session数据：username=" + username + ", userId=" + userId;
}
```

### 3.4 SpringBoot Session 配置

在 `application.properties` 中自定义 Session 全局配置

```properties
# Session 超时时间（单位：秒），默认1800秒
server.servlet.session.timeout=1800
# 是否开启Cookie，默认开启
server.servlet.session.cookie.enabled=true
# Session Cookie 路径
server.servlet.session.cookie.path=/
# Session Cookie HttpOnly 安全配置
server.servlet.session.cookie.http-only=true
```

## 四、Cookie和Session对比

| 对比项             | Cookie                                  | Session                          |
| ------------------ | --------------------------------------- | -------------------------------- |
| 存储位置           | ✅ **客户端（浏览器）**                  | ✅ **服务器端**                   |
| 保存数据           | 键值对，只能存字符串，小体积（4KB以内） | 键值对，值可以存储对象，容量大   |
| 数据安全性         | ❌ 容易被查看/篡改（保存在用户机器）     | ✅ 更安全，保存在服务器           |
| 生命周期           | 可设置过期时间，默认浏览器关闭失效      | 服务器关闭或超时失效（如30分钟） |
| 是否占用服务器资源 | ❌ 不占用                                | ✅ 占用（每个用户一个 Session）   |
| 典型使用场景       | 记住用户名、自动登录、广告跟踪          | 用户登录状态、权限控制           |
