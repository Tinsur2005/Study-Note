# JDBC

## 一、JDBC 简介

### 1.1 核心概念

JDBC（Java Database Connectivity，Java数据库连接）是 Java 官方提供的一套**数据库操作标准接口**，用于实现 Java 程序与各类关系型数据库的交互。

### 1.2 核心设计思想：面向接口编程

JDBC 采用**接口与实现分离**的设计模式，是典型的面向接口编程思想：

- **Java 官方**：只定义统一的数据库操作核心接口，不提供具体实现。核心接口包括 `Connection`（数据库连接）、`Statement`（SQL执行）、`ResultSet`（结果集）。
- **数据库厂商**（MySQL、Oracle 等）：根据官方接口标准，开发对应的**数据库驱动程序**，实现接口中的所有方法。例如 MySQL 驱动包中包含了 JDBC 接口的具体实现类。
- **Java 开发者**：仅需要调用 JDBC 接口方法即可操作数据库，无需关注底层实现。更换数据库时，仅需替换驱动包和连接地址，核心业务代码无需修改，兼容性极强。

## 二、JDBC开发六大步骤

所有 JDBC 数据库增删改查操作，均遵循固定六大开发步骤，是 JDBC 编程的核心规范：

1. **加载数据库驱动**：通过反射加载数据库驱动类，注册驱动程序
2. **获取数据库连接**：通过 `DriverManager` 获取 `Connection` 连接对象
3. **编写 SQL 语句**：根据业务需求编写 DQL、DML 等 SQL 语句
4. **创建 SQL 执行对象**：创建 `Statement` 或 `PreparedStatement` 对象
5. **执行 SQL 语句**：根据 SQL 类型选择对应执行方法，获取执行结果
6. **关闭资源**：逆序关闭结果集、执行对象、连接对象，释放资源

### 2.1 核心步骤细节说明

#### 1. 加载驱动

MySQL 8.x 版本驱动类全路径：`com.mysql.cj.jdbc.Driver`

代码示例：

```Plain
Class.forName("com.mysql.cj.jdbc.Driver");
```

补充：MySQL8.0.6 及以上版本可省略该代码（SPI 自动注册驱动），为兼容低版本，建议保留。

#### 2. 获取连接对象

连接地址包含时区、编码、SSL 等必备参数，避免数据库连接报错：

```java
String url = "jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8";
String username = "root";
String password = "1234";
Connection connection = DriverManager.getConnection(url, username, password);
```

#### 3. SQL 执行方法区分

| SQL 类型                            | 执行方法          | 返回值           | 作用                            |
| :---------------------------------- | :---------------- | :--------------- | :------------------------------ |
| DML（insert/update/delete）更新语句 | `executeUpdate()` | int 类型         | 返回 SQL 执行后影响的数据库行数 |
| DQL（select）查询语句               | `executeQuery()`  | ResultSet 结果集 | 返回查询到的数据库数据集合      |

#### 4. 资源关闭规则

资源关闭遵循**逆序关闭**原则：ResultSet 结果集 > Statement/PreparedStatement 执行对象 > Connection 连接对象，且需做空判断，避免空指针异常。

### 2.2 基础查询 Statement

> 使用`Statement`有**SQL注入**风险，所以一般不推荐使用，更推荐使用**预编译查询** `PreparedStatement` ，可以防止SQL注入。

```java
@Test
public void test1() {
    try {
        // 1、加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2、获得连接对象Connection
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8", "root", "1234");
        // 3、写sql语句
        String sql = "SELECT id,name,age,gender FROM student";
        // 4、创建Statement执行对象
        Statement statement = connection.createStatement();
        // 5、执行sql语句，获取结果集
        ResultSet resultSet = statement.executeQuery(sql);
        // 封装结果集数据
        List<Student> list = new ArrayList<>();
        // resultSet.next()：指针下移，有数据返回true，无数据返回false
        while (resultSet.next()) {
            // 根据字段名获取对应数据
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            int age = resultSet.getInt("age");
            String gender = resultSet.getString("gender");
            // 封装为实体类对象
            Student student = new Student(id, name, age, gender);
            list.add(student);
        }
        // 遍历输出结果
        for (Student student : list) {
            System.out.println(student);
        }
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        // 6、关闭资源（简易版，完整关闭见后续示例）
    }
}
```

### **2.3 预编译查询 PreparedStatement**

```java
@Test
public void test2() {
    try {
        // 1、加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2、获取连接
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8", "root", "1234");
        // 3、编写SQL语句
        String sql = "SELECT id,name,age,gender FROM student";
        // 4、创建预编译执行对象
        PreparedStatement statement = connection.prepareStatement(sql);
        // 5、执行查询（无需传入SQL参数）
        ResultSet resultSet = statement.executeQuery();
        // 数据封装
        List<Student> list = new ArrayList<>();
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            int age = resultSet.getInt("age");
            String gender = resultSet.getString("gender");
            Student student = new Student(id, name, age, gender);
            list.add(student);
        }
        // 遍历输出
        for (Student student : list) {
            System.out.println(student);
        }
    } catch (ClassNotFoundException e) {
        throw new RuntimeException(e);
    } catch (SQLException e) {
        throw new RuntimeException(e);
    }
}
```

### 2.4 资源关闭

```java
@Test
public void test3() {
    // 提前声明所有资源对象，用于finally关闭
    Connection connection = null;
    PreparedStatement statement = null;
    ResultSet resultSet = null;
    try {
        // 1、加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2、获取连接
        connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8", "root", "1234");
        // 3、编写SQL
        String sql = "SELECT id,name,age,gender FROM student";
        // 4、创建预编译对象
        statement = connection.prepareStatement(sql);
        // 5、执行SQL
        resultSet = statement.executeQuery();
        // 数据封装
        List<Student> list = new ArrayList<>();
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            int age = resultSet.getInt("age");
            String gender = resultSet.getString("gender");
            Student student = new Student(id, name, age, gender);
            list.add(student);
        }
        for (Student student : list) {
            System.out.println(student);
        }
    } catch (ClassNotFoundException e) {
        throw new RuntimeException(e);
    } catch (SQLException e) {
        throw new RuntimeException(e);
    } finally {
        // 6、逆序关闭所有资源，做空判断
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```

## 三、JDBC 工具类封装

为避免代码冗余，统一管理数据库配置、连接获取、资源关闭，封装通用 JDBC 工具类，结合 `db.properties` 配置文件实现解耦。

### 3.1 配置文件 db.properties

放置在项目 resources 目录下，统一存储数据库参数：

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/study?useSSL=false&useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2b8
username=root
password=1234
```

### 3.2 工具类

```java
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Properties;

/**
 * JDBC 通用工具类
 * 功能：获取数据库连接、统一关闭资源
 */
public class JDBCUtil {
    // 数据库配置参数
    private static String driver;
    private static String url;
    private static String username;
    private static String password;

    // 静态代码块：类加载时执行一次，初始化驱动和配置参数
    static {
        try {
            // 1、获取类加载器，读取配置文件
            ClassLoader classLoader = JDBCUtil.class.getClassLoader();
            InputStream inputStream = classLoader.getResourceAsStream("db.properties");
            // 2、加载配置文件参数
            Properties properties = new Properties();
            properties.load(inputStream);
            // 3、赋值全局变量
            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 加载数据库驱动
        try {
            Class.forName(driver);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取数据库连接对象
     * @return Connection 连接对象
     * @throws SQLException 数据库异常
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, username, password);
    }

    /**
     * 统一关闭资源方法
     * @param connection 连接对象
     * @param statement 执行对象
     * @param resultSet 结果集
     */
    public static void close(Connection connection, PreparedStatement statement, ResultSet resultSet) {
        // 逆序关闭资源，做空校验
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

### 3.3 测试代码

#### 1. 通用查询测试

```java
@Test
public void testPreparedStatement() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    try {
        // 调用工具类获取连接
        connection = JDBCUtil.getConnection();
        String sql = "SELECT id,name,age,gender FROM student";
        preparedStatement = connection.prepareStatement(sql);
        resultSet = preparedStatement.executeQuery();
        // 数据封装
        List<Student> list = new ArrayList<>();
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            int age = resultSet.getInt("age");
            String gender = resultSet.getString("gender");
            Student student = new Student(id, name, age, gender);
            list.add(student);
        }
        // 遍历结果
        for (Student student : list) {
            System.out.println(student);
        }
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        // 调用工具类关闭资源
        JDBCUtil.close(connection, preparedStatement, resultSet);
    }
}
```

#### 2. 新增数据测试

```java
@Test
public void testInsert() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    try {
        connection = JDBCUtil.getConnection();
        // ? 为占位符，避免SQL拼接
        String sql = "insert into student(name,age,gender) values(?,?,?)";
        preparedStatement = connection.prepareStatement(sql);
        // 给占位符赋值（下标从1开始）
        preparedStatement.setString(1, "张三");
        preparedStatement.setInt(2, 23);
        preparedStatement.setString(3, "女");
        // 执行更新
        int count = preparedStatement.executeUpdate();
        System.out.println("受影响行数：" + count);
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        JDBCUtil.close(connection, preparedStatement, null);
    }
}
```

#### 3. 删除数据测试

```java
@Test
public void testDelete() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    try {
        connection = JDBCUtil.getConnection();
        String sql = "delete from student where id=?";
        preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setInt(1, 10);
        int count = preparedStatement.executeUpdate();
        System.out.println("受影响行数：" + count);
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        JDBCUtil.close(connection, preparedStatement, null);
    }
}
```

#### 4. 修改数据测试

```java
@Test
public void testUpdate() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    try {
        connection = JDBCUtil.getConnection();
        String sql = "update student set name=?,age=?,gender=? where id=?";
        preparedStatement = connection.prepareStatement(sql);
        // 依次给占位符赋值
        preparedStatement.setString(1, "小张");
        preparedStatement.setInt(2, 23);
        preparedStatement.setString(3, "男");
        preparedStatement.setInt(4, 9);
        int count = preparedStatement.executeUpdate();
        System.out.println("受影响行数：" + count);
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        JDBCUtil.close(connection, preparedStatement, null);
    }
}
```

#### 5. 模糊查询测试

```java
@Test
public void testLike() {
    Connection connection = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    try {
        connection = JDBCUtil.getConnection();
        String sql = "select id,name,age,gender from student where name like ?";
        preparedStatement = connection.prepareStatement(sql);
        // 模糊查询占位符赋值
        preparedStatement.setString(1, "%张%");
        resultSet = preparedStatement.executeQuery();
        // 数据封装
        List<Student> list = new ArrayList<>();
        while (resultSet.next()) {
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            int age = resultSet.getInt("age");
            String gender = resultSet.getString("gender");
            Student student = new Student(id, name, age, gender);
            list.add(student);
        }
        for (Student student : list) {
            System.out.println(student);
        }
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        JDBCUtil.close(connection, preparedStatement, resultSet);
    }
}
```

## 四、Statement与PreparedStatement的区别

`PreparedStatement`（预编译执行对象）是 `Statement` 的升级版，实际开发中**优先使用 PreparedStatement**。

| 对比维度          | Statement                         | PreparedStatement                                  |
| :---------------- | :-------------------------------- | :------------------------------------------------- |
| SQL 拼接方式      | 手动字符串拼接，代码繁琐          | 使用 `?` 占位符，无需手动拼接                      |
| 安全性（SQL注入） | 极易遭受 SQL 注入攻击，安全性低   | 预编译机制，彻底杜绝 SQL 注入，安全性高            |
| 执行性能          | 每次执行 SQL 都会重新编译，效率低 | SQL 仅预编译一次，可多次复用参数，循环场景效率极高 |
| 代码可读性        | 拼接代码混乱，易出错              | 结构清晰，参数单独赋值，便于维护                   |

### 4.1 SQL 注入漏洞演示

#### 1. 测试表创建语句

```sql
CREATE TABLE user(
    id INT PRIMARY KEY AUTO_INCREMENT,
    `name` VARCHAR(10),
    `password` VARCHAR(10)
);
INSERT INTO user(`name`, `password`) VALUES('lisi',123);
```

#### 2. 注入原理

利用字符串拼接漏洞，输入恶意参数篡改 SQL 逻辑，实现非法查询：

```sql
-- 正常查询语句
SELECT * FROM user WHERE `name`='lisi' AND `password`='123';

-- 恶意注入参数：zhangsan' OR 1=1 -- y
-- 拼接后SQL语句（恒成立，查询所有数据）
SELECT * FROM user WHERE `name`='zhangsan' OR 1=1 -- y' AND `password`='';
```

**核心问题**：Statement 直接拼接字符串，恶意参数会改变 SQL 原有逻辑；PreparedStatement 预编译会将占位符参数**统一当做普通字符串处理**，不会解析 SQL 关键字，彻底避免注入。
