## 一、SpringBoot 简介

### 1.1 传统 Spring 开发的缺点

- **依赖导入繁琐**：开发不同功能需要手动导入大量 Jar 包，版本适配复杂，容易出现版本冲突问题。
- **项目配置繁琐**：需要手动编写大量 XML 配置文件，包含容器配置、Bean 注册、依赖整合、视图解析等，配置冗余且维护成本高。
- **部署流程复杂**：需要额外配置外置 Tomcat 服务器，项目打包、部署步骤繁琐。
- **整合第三方框架困难**：Spring 原生框架无自动整合机制，整合 MyBatis、Thymeleaf 等框架需要手动配置大量参数。

### 1.2 SpringBoot 核心概念

SpringBoot 并非全新框架，而是**Spring 框架的子项目、脚手架工具**，用于快速、极简地构建独立可运行的 Spring 企业级应用。

随着 Spring3.0 迭代升级，Spring 团队彻底摒弃繁琐的 XML 配置，核心遵循**约定优先配置（Convention over Configuration）**思想，通过默认约定、自动配置、起步依赖三大核心特性，简化 Spring 开发流程，让开发者专注业务逻辑开发。

### 1.3 SpringBoot 核心特性

- **起步依赖（Starter）**：内置全套场景依赖整合方案，无需手动导入零散 Jar 包，按需引入对应 starter 即可完成场景依赖配置，自动适配版本。
- **自动配置（AutoConfiguration）**：根据项目导入的依赖，自动完成 Spring 及第三方框架的配置，无需手动编写配置代码和 XML 文件。
- **内置 Web 容器**：内嵌 Tomcat、Jetty 等服务器，项目无需外置容器，直接通过 main 方法启动、打包运行。
- **无冗余代码**：摒弃繁琐配置，简化开发模板，开箱即用。
- **生产级特性**：内置监控、日志、异常处理等企业级功能，适配线上项目部署运行。

## 二、SpringBoot 项目搭建

本文采用 Maven 构建工具搭建 SpringBoot3.x 项目，包含完整依赖、启动类、配置文件、Mapper 文件等核心代码。

### 2.1 核心 pom.xml 依赖文件

统一管理项目版本、场景依赖、打包插件，整合 Web、MyBatis、Thymeleaf、分页、日志等全套开发依赖。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- SpringBoot 父工程：统一管理所有依赖版本，解决版本冲突 -->
    <parent>
       <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
       <version>3.4.5</version>
       <relativePath/> <!-- 从仓库查找父工程，无需本地路径 -->
    </parent>

    <!-- 项目基础坐标 -->
    <groupId>com.situ</groupId>
    <artifactId>springboot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot</name>
    <description>SpringBoot 入门实战项目</description>

    <!-- 全局属性配置 -->
<properties>
       <java.version>17</java.version>
    </properties>

    <!-- 项目核心依赖 -->
    <dependencies>
       <!-- Spring 核心基础依赖 -->
       <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter</artifactId>
       </dependency>

       <!-- SpringMVC Web 场景依赖：处理请求、接口响应 -->
       <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
       </dependency>

       <!-- MySQL 数据库驱动 -->
       <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.31</version>
       </dependency>

       <!-- MyBatis 整合 SpringBoot 依赖 -->
       <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>3.0.3</version>
       </dependency>

       <!-- Thymeleaf 模板引擎：服务端页面渲染 -->
       <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-thymeleaf</artifactId>
       </dependency>

      <!-- Lombok：简化实体类代码，自动生成 get/set/构造/toString 方法 -->
       <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <version>1.18.30</version>
          <scope>provided</scope>
       </dependency>

       <!-- PageHelper 分页插件 -->
       <dependency>
          <groupId>com.github.pagehelper</groupId>
          <artifactId>pagehelper-spring-boot-starter</artifactId>
          <version>2.1.0</version>
       </dependency>

       <!-- 单元测试依赖 -->
       <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-test</artifactId>
          <scope>test</scope>
       </dependency>
    </dependencies>

    <!-- 项目构建插件 -->
    <build>
       <plugins>
          <!-- SpringBoot 打包插件：支持项目打包为 jar 包独立运行 -->
          <plugin>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
       </plugins>
    </build>

</project>
```

### 2.2 项目启动类

SpringBoot 项目唯一启动入口，`@SpringBootApplication` 是核心注解，整合了自动配置、包扫描等功能。

```java
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// SpringBoot 项目启动核心注解：开启自动配置、组件扫描
@SpringBootApplication
// 扫描 Mapper 接口包，无需每个 Mapper 加 @Mapper 注解
@MapperScan("com.situ.springboot.mapper")
public class SpringbootApplication {
    public static void main(String[] args) {
        // 启动 SpringBoot 项目，加载容器、自动配置
        SpringApplication.run(SpringbootApplication.class, args);
    }
}
```

### 2.3 基础 Controller 测试类

模拟前端请求接收、数据响应，实现简单的查询接口测试。

```java
import com.situ.springboot.pojo.Student;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/student")
public class StudentController {

    // 学生列表查询接口
    @RequestMapping("/selectAll")
    @ResponseBody // 返回 JSON 格式数据，而非页面跳转
    public List<Student> selectAll() {
        System.out.println("StudentController.selectAll 接口执行");
        List<Student> list = new ArrayList<>();

        // 模拟数据
        Student student1 = new Student(1, "zhangsan", 18, "男");
        Student student2 = new Student(2, "lisi", 19, "女");
        Student student3 = new Student(3, "wangwu", 20, "男");

        list.add(student1);
        list.add(student2);
        list.add(student3);
        return list;
    }
}
```

### 2.4 全局配置文件 application.properties

SpringBoot 核心配置文件，优先级最高，可配置端口、数据库、MyBatis、静态资源等所有项目参数。

```properties
# 1. 服务器端口配置
server.port=8080

# 2. 数据库连接配置
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8&zeroDateTimeBehavior=CONVERT_TO_NULL
spring.datasource.username=root
spring.datasource.password=1234

# 3. 静态资源访问路径配置
spring.mvc.static-path-pattern=/static/**

# 4. MyBatis 全局配置
# 实体类包别名扫描
mybatis.type-aliases-package=com.situ.springboot.pojo
# Mapper XML 文件路径扫描
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
# 开启驼峰命名自动转换（数据库下划线 → 实体类驼峰）
mybatis.configuration.map-underscore-to-camel-case=true
# 开启 SQL 日志打印
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

### 2.5 Mapper 映射文件 StudentMapper.xml

MyBatis 数据库 SQL 映射文件，存放数据查询、增删改查语句。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 绑定对应 Mapper 接口 -->
<mapper namespace="com.situ.springboot.mapper.StudentMapper">
    <!-- 查询所有学生数据，resultType 对应实体类别名 -->
    </mapper>
```

## 三、项目三层架构

### 3.1 三层架构核心分层

SpringBoot 项目标准开发架构，职责分离、解耦代码，便于维护和迭代。

- **Controller 控制层**：接收前端 HTTP 请求，参数校验、请求分发，响应数据/页面给前端，不处理业务逻辑和数据操作。
- **Service 业务逻辑层**：处理核心业务逻辑、事务控制、数据校验，调用 Dao 层操作数据，是项目核心业务载体。
- **Dao（Mapper）数据访问层**：持久层，专门负责数据库的增删改查操作，仅做数据交互，不处理业务逻辑。

### 3.2 完整执行流程

前端页面/客户端发起请求 → **Controller 接收请求** → 调用 Service 层方法 → **Service 处理业务逻辑** → 调用 Dao 层接口 → **Dao 层操作数据库** → 数据逐层返回 → Controller 响应前端。

### 3.3 架构核心优势

- 职责单一、代码解耦，各层各司其职
- 便于团队分工开发、后期维护迭代
- 方便单元测试、功能扩展、代码复用

## 四、IOC 与 DI 核心原理

### 4.1 IOC 控制反转

**1. 概念**

IOC（Inversion of Control，控制反转）：**将对象的创建、管理、销毁权限，从开发者代码中转移到 Spring 容器**，彻底摒弃手动 new 对象的方式。

**2. 正转和反转**

- **控制正转（传统开发）**：开发者手动 new 对象，主动控制对象创建，代码耦合度极高。
- **控制反转（Spring）**：开发者只定义接口，对象由 Spring 容器统一创建、管理、赋值，开发者直接从容器获取对象。

示例：传统开发耦合代码

```java
class StudentController{
    // 手动 new 实现类，代码高度耦合
    private IStudentService studentService = new StudentServiceImpl();
}
```

### 4.2 DI 依赖注入

**1. 概念**

DI（Dependency Injection，依赖注入）：**Spring 容器创建好对象后，自动将依赖的对象赋值给当前类的属性**，无需手动赋值。

**2. 核心逻辑**

IOC 解决 **对象创建问题**，DI 解决 **对象依赖关系赋值问题**，**先有 IOC，再有 DI**。

### 4.3 IOC+DI 最终效果

三层架构彻底解耦：

- Controller 层只依赖 Service 接口，不依赖具体实现类
- Service 层只依赖 Dao 接口，不依赖具体实现类
- 所有对象由 Spring 容器统一管理、自动注入

## 五、MVC 模式与 Thymeleaf 模板引擎

### 5.1 MVC 核心模式

MVC（Model-View-Controller）分层开发模式，是 Web 项目标准架构：

- **Model（模型）**：POJO 实体类、业务数据、业务逻辑，用于封装和存储数据。
- **View（视图）**：页面展示层（HTML/Thymeleaf），负责数据渲染、页面展示。
- **Controller（控制器）**：请求调度中心，接收请求、调用模型、跳转视图。

### 5.2 POJO 与 JavaBean 规范

- **POJO**：简单 Java 对象，无任何框架依赖，仅用于封装数据。
- **JavaBean 规范**：        
  - 所有成员属性私有化（private）
  - 提供默认无参构造方法
  - 提供 getter/setter 方法
  - 实现 Serializable 序列化接口

### 5.3 Thymeleaf 模板引擎

**1. 简介**

Thymeleaf 是 SpringBoot 官方推荐的服务端模板引擎，替代传统 JSP，支持 HTML 原型开发，可直接在浏览器打开预览，无需服务器。

**2. 核心依赖**

```xml
<!-- Thymeleaf 模板引擎依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

**3. 控制器视图跳转代码**

```java
// 返回视图页面，携带数据到前端
@RequestMapping("/selectAll")
public String selectAll(Model model) {
    List<Student> list = studentService.selectAll();
    // 将数据存入 Model，传递到前端页面
    model.addAttribute("list", list);
    // 跳转 templates 下的 student_list.html 页面
    return "student_list";
}
```

**4.HTML页面源码**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" type="text/css" href="/static/bootstrap-3.4.1-dist/css/bootstrap.css"/>
</head>
<body>
    <table class="table table-bordered table-condensed table-hover table-striped">
        <tr>
            <td>编号</td>
            <td>姓名</td>
            <td>年龄</td>
            <td>性别</td>
        </tr>
        <tr th:each="student:${list}">
            <td th:text="${student.id}"></td>
            <td th:text="${student.name}"></td>
            <td th:text="${student.age}"></td>
            <td th:text="${student.gender}"></td>
        </tr>
    </table>

</body>
</html>
```

## 六、请求方式、转发与重定向

### 6.1 登录页面HTML

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<body>

<div th:if="${errorMsg}" th:text="${errorMsg}" style="color:red;"></div>
<form id="loginForm" method="get" action="/user/login">
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
</body>
</html>
```

### 6.2 登录控制器逻辑

```java
import org.springframework.util.ObjectUtils;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/user")
public class UserController {

    // 跳转登录页面
    @RequestMapping("/toLogin")
    public String toLogin() {
        return "login";
    }

    // 登录校验逻辑
    @RequestMapping("/login")
    public String login(String name, String password, Model model) {
        // 调用业务层校验用户名密码
        User user = userService.login(name, password);
        if (ObjectUtils.isEmpty(user)) {
            // 登录失败，回显错误信息，跳转登录页
            model.addAttribute("errorMsg", "用户名或密码错误");
            return "login";
        }
        // 登录成功，重定向到学生列表页面
        return "redirect:/student/selectAll";
    }
}
```

### 6.3 GET 与 POST 请求区别

- **语义区别**：GET 用于**查询资源**，安全、幂等；POST 用于**提交数据、创建资源**。
- **参数位置**：GET 参数拼接在 URL 地址栏；POST 参数存放在请求体 Body 中。
- **长度限制**：GET 受浏览器 URL 长度限制，无法传输大量数据；POST 无长度限制。
- **安全性**：二者均为明文传输，不安全；GET 参数暴露在地址栏，更容易泄露敏感信息，生产环境需用 HTTPS 加密。
- **缓存特性**：GET 请求可被浏览器缓存；POST 请求默认不缓存。

### 6.4 转发 Forward vs 重定向 Redirect

| 对比项   | 转发（Forward）    | 重定向（Redirect）             |
| :------- | :----------------- | :----------------------------- |
| 发起方   | 服务器内部完成     | 服务器通知浏览器重新请求       |
| 请求次数 | 1 次请求           | 2 次请求                       |
| URL 地址 | 浏览器地址不变     | 浏览器地址更新                 |
| 数据共享 | 共享 Request 数据  | Request 数据丢失               |
| 性能     | 速度快，无二次请求 | 速度稍慢，需要二次请求         |
| 使用场景 | 页面跳转、数据回显 | 登录成功跳转、防止表单重复提交 |

## 七、会话技术 Cookie 与 Session

### 7.1 核心概念

HTTP 协议是**无状态协议**，无法记录用户访问状态，会话技术用于保存用户会话信息，实现登录状态保持、用户识别。

- **Cookie**：客户端会话技术，数据存储在浏览器本地。
- **Session**：服务端会话技术，数据存储在服务器内存中。

### 7.2 Cookie 与 Session 对比

| 对比项         | Cookie                               | Session                          |
| :------------- | :----------------------------------- | :------------------------------- |
| 存储位置       | 浏览器客户端                         | 服务器端                         |
| 存储数据       | 仅字符串、4KB 小容量                 | 可存对象、大容量                 |
| 安全性         | 低，可被查看、篡改、手动清除         | 高，数据存在服务器               |
| 服务器资源占用 | 不占用                               | 占用，用户越多压力越大           |
| 生命周期       | 可自定义过期时间，默认关闭浏览器失效 | 默认 30 分钟超时，服务器重启失效 |
| 典型场景       | 记住用户名、自动登录、偏好设置       | 登录状态、权限控制、用户信息存储 |

### 7.3 Cookie 实战代码

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.CookieValue;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

// 设置 Cookie
@RequestMapping("/setCookie")
public void setCookie(HttpServletResponse response) {
    Cookie cookie = new Cookie("goods", "IPhone");
    cookie.setPath("/"); // 全局生效
    cookie.setMaxAge(60 * 60); // 有效期1小时
    response.addCookie(cookie);
}

// 方式1：遍历获取 Cookie
@RequestMapping("/getCookie1")
@ResponseBody
public String getCookie1(HttpServletRequest request) {
    Cookie[] cookies = request.getCookies();
    if (cookies != null) {
        for (Cookie cookie : cookies) {
            System.out.println(cookie.getName() + ":" + cookie.getValue());
        }
    }
    return "Cookie 获取成功";
}

// 方式2：注解精准获取 Cookie
@RequestMapping("/getCookie2")
@ResponseBody
public String getCookie2(@CookieValue("goods") String goods) {
    System.out.println("获取 Cookie：" + goods);
    return "精准获取 Cookie 成功";
}
```

### 7.4 Session 登录状态保存实战

```java
import javax.servlet.http.HttpSession;

@RequestMapping("/login")
public String login(String name, String password, Model model, HttpSession session) {
    User user = userService.login(name, password);
    if (ObjectUtils.isEmpty(user)) {
        model.addAttribute("errorMsg", "用户名或密码错误");
        return "login";
    }
    // 登录成功，将用户信息存入 Session，保存登录状态
    session.setAttribute("user", user);
    return "redirect:/student/selectAll";
}
```

## 八、登录拦截器

### 8.1 拦截器作用

拦截所有浏览器请求，**在控制器执行前进行拦截校验**，实现登录权限控制、请求过滤、日志记录等功能，未登录用户禁止访问后台资源。

### 8.2 自定义登录拦截器

```java
import org.springframework.web.servlet.HandlerInterceptor;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

// 自定义登录拦截器
public class LoginInterceptor implements HandlerInterceptor {

    // 控制器执行前拦截
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 获取 Session 中的登录用户
        HttpSession session = request.getSession();
        Object user = session.getAttribute("user");

        // 未登录：跳转登录页，拦截请求
        if (user == null) {
            response.sendRedirect("/user/toLogin");
            return false;
        }
        // 已登录：放行请求
        return true;
    }
}
```

### 8.3 拦截器全局配置

通过 `WebMvcConfigurer` 注册拦截器，设置拦截规则、放行静态资源和登录接口。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

// 全局配置类，替代 XML 配置
@Configuration
public class WebConfig implements WebMvcConfigurer {

    // 注册拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .addPathPatterns("/**") // 拦截所有请求
                // 放行登录相关接口、静态资源
                .excludePathPatterns("/user/toLogin", "/user/login", "/static/**");
    }

    // 自定义 Bean 示例
    @Bean
    public Student createStudent() {
        return new Student();
    }
}
```

## 九、Ajax

### 9.1 什么是 Ajax

Ajax 全称：**Asynchronous JavaScript and XML（异步 JS 和 XML）**，是一种可以与服务器进行**异步/同步数据交互**的前端技术。

Ajax 的语言载体是 JavaScript，核心最大特点：**异步请求、局部刷新**，无需刷新整个页面，仅更新页面局部数据，极大提升用户体验。

补充概念：synchronized 代表同步，与 Ajax 异步特性相对。

### 9.2 同步与异步请求区别

**1. 同步请求**

通俗理解：你传输数据，我全程等待，必须等你传输完成，我才能做其他事情。

专业解释：浏览器向服务器发送请求后，页面会**阻塞锁定**，在服务器处理请求、返回响应的整个过程中，浏览器无法执行任何其他操作，只能等待请求结束后，页面才能继续交互。

**2. 异步请求**

通俗理解：你后台传输数据，我正常操作页面，你传输完成后再通知我结果。

专业解释：浏览器发送请求后，**页面不会阻塞**，用户可正常操作页面、点击、输入内容，服务器在后台独立处理请求，处理完成后主动回调前端，更新页面数据。

**异步交互核心优势**：可以在不重新加载整个页面的前提下，与服务器交换数据、更新网页局部内容。

### 9.3 Axios 工具介绍

原生 Ajax 语法繁琐、代码冗余、兼容性差，开发效率低。为了简化异步请求开发，我们使用 **Axios** 工具。

Axios 是对原生 AJAX 的**二次封装**，大幅简化异步请求代码，语法简洁、兼容性强、支持 Promise 语法，是目前前端主流的 HTTP 请求工具。

Axios 官方地址：https://www.axios-http.cn

### 9.4 Axios 异步登录完整实战代码

实现表单阻止默认刷新、获取表单参数、Axios 异步提交登录请求，根据后端响应结果跳转页面或弹窗提示。

```html
<!DOCTYPE html>
<html lang="zh-CN" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Axios 异步登录</title>
</head>
<body>
<!-- 登录表单 -->
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

<!-- 引入 Axios 在线 CDN 资源 -->
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
<script>
    // 绑定表单提交事件
    document.getElementById('loginForm').addEventListener('submit', function (e) {
        // 阻止表单默认刷新提交行为，实现异步请求
        e.preventDefault();
        // 获取表单输入数据
        var name = document.getElementById('name').value;
        var password = document.getElementById('password').value;

        // 封装请求参数，自动适配表单提交格式
        var params = new URLSearchParams();
        params.append('name', name);
        params.append('password', password);

        // 发送 POST 异步登录请求
        axios.post('/user/login', params)
            .then(function (resp) {
                // 获取后端响应数据
                var res = resp.data;
                // 登录成功：跳转学生列表页面
                if (res.code === 1) {
                    location.href = '/student/selectAll';
                } else {
                    // 登录失败：弹窗提示错误信息
                    alert(res.msg);
                }
            }).catch(function (err) {
                // 网络/请求异常捕获
                alert('请求失败，请稍后重试');
            });
    });
</script>
</body>
</html>
```

### 代码核心说明

- **e.preventDefault()：**阻止表单默认整页刷新提交，是异步请求必备配置。
- **URLSearchParams：**自动将参数封装为表单默认的 `application/x-www-form-urlencoded` 格式，后端可直接通过形参接收数据，无需额外解析。
- **then 回调：**请求成功后执行，处理正常业务逻辑（页面跳转、提示信息）。
- **catch 回调：**捕获网络异常、接口报错等问题，统一异常提示。