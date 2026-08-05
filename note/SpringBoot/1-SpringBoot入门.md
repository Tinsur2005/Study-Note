# SpringBoot

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

> 在Mabytis中，**参数是可以不用写的**，返回值必须要写

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

```text
           浏览器
             ↓↑
┌──────────────────────────┐
│表现层(Servlet、Controller)│  ← StudentController
└──────────────────────────┘
             ↓↑
    ┌──────────────┐
    │逻辑层(Service)│  ← IStudentService、StudentServiceImpl
    └───────┬──────┘
            ↓↑
    ┌──────────────┐
    │数据访问层(Dao)│  ← IStudentDao、StudentDaoImpl、StudentMapper
    └───────┬──────┘
            ↓↑
        ┌───────┐
        │  DB   │
        └───────┘
```

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

```java
@Mapper   //依赖注入
public interface StudentMapper {
    List<Student> selectAll();
}
```

```java
@Autowired	//取出对象
private StudentMapper studentMapper;
```

### 4.3 IOC+DI 最终效果

三层架构彻底解耦：

- Controller 层只依赖 Service 接口，不依赖具体实现类
- Service 层只依赖 Dao 接口，不依赖具体实现类
- 所有对象由 Spring 容器统一管理、自动注入

> :warning:**注意：**
>
> ​	@Component 是new对象放到容器中
>
> ​	@Controller、@Service、@Repository new对象放到Spring容器中
>
> ​	和@Component功能一样，就是@Component的别名，主要是为了区分三层架构不同的层

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

#### 1. 简介

Thymeleaf 是 SpringBoot 官方推荐的服务端模板引擎，替代传统 JSP，支持 HTML 原型开发，可直接在浏览器打开预览，无需服务器。

#### 2.核心依赖

```xml
<!-- Thymeleaf 模板引擎依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

#### 3.控制器视图跳转代码

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

#### 4.HTML页面源码

> 应该新建一个 `student_list.html` ，所有的HTML都应该放在 `References` 下的`templates` 文件夹中

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
	<!-- 获取model内的数据 -->
    <div th:if="${errorMsg}" th:text="${errorMsg}" style="color:red;"></div>
    <!-- 获取session内的数据 -->
	<div th:if="${session.errorMsg}" th:text="${session.errorMsg}" style="color:red;"></div>
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

### 6.4 转发（Forward）和重定向（Redirect）

| 对比项         | 转发（Forward）    | 重定向（Redirect）             |
| :------------- | :----------------- | :----------------------------- |
| 发起方         | 服务器内部完成     | 服务器通知浏览器重新请求       |
| 请求次数       | 1 次请求，1 次响应 | 2 次请求，2 次响应             |
| URL 地址       | ❌浏览器地址不变    | ✅浏览器地址更新                |
| 浏览器是否知道 | ❌不知道            | ✅知道                          |
| 数据共享       | 共享 Request 数据  | Request 数据丢失               |
| 性能           | 速度快，无二次请求 | 速度稍慢，需要二次请求         |
| 使用场景       | 页面跳转、数据回显 | 登录成功跳转、防止表单重复提交 |
