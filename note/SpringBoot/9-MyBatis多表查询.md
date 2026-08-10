# SpringBoot：MyBatis多表查询

## 一、多表基础结构

本次多表查询案例基于四张数据表实现，分别为：**学生表、班级表、课程表、班级课程中间表**，各表核心作用与关联关系如下：

- **student 学生表**：存储学生基础信息，包含外键 `banji_id`，关联班级表主键id
- **banji 班级表**：存储班级基础信息，主键id
- **course 课程表**：存储课程基础信息，主键id
- **banji_course 班级课程表（中间表）**：解决班级和课程的多对多关系，存储 `banji_id`、`course_id` 两个外键

核心表关系：

- 一对一：学生 → 一个学生仅归属一个班级
- 一对多：班级 → 一个班级包含多名学生
- 多对多：班级 <→ 课程（通过中间表拆解为两个一对多）

## 二、一对一关联查询

### 2.1 业务关系

一个学生只属于一个班级，属于**一对一单向关联**。

### 2.2 关联查询SQL

查询学生id、姓名、年龄、性别、所属班级名称

```sql
SELECT s.id,s.`name`,s.age,s.gender,b.id AS banjiId,b.name AS banjiName
FROM student AS s INNER JOIN banji AS b
ON s.banji_id=b.id;
```

#### 2.3 实体类定义

学生实体中嵌入班级实体对象，实现一对一关联封装

```java
public class Student{
    private Integer id;
    private String name;
    private Integer age;
    private String gender;
    // 一对一关联班级对象
    private Banji banji;
    
    // 省略 getter、setter、toString 方法
}

// 班级实体类
public class Banji{
    private Integer id;
    private String name;
    
    // 省略 getter、setter、toString 方法
}
```

### 2.4 Mapper映射文件

MyBatis 使用 **association 标签** 实现一对一对象关联封装

```xml
<!-- 自定义学生结果映射，关联班级信息 -->
<resultMap id="studentMap" type="Student">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <result column="banji_id" property="banjiId"/>
    <!-- 一对一关联班级对象 -->
    <association property="banji" javaType="Banji">
        <id property="id" column="banjiId"/>
        <result property="name" column="banjiName"/>
    </association>      
</resultMap>

<!-- 查询学生及对应班级信息 -->
<select id="selectStudentBanjiInfo" resultMap="studentMap">
    SELECT s.id,s.`name`,s.age,s.gender,b.id AS banjiId,b.name AS banjiName
    FROM student AS s INNER JOIN banji AS b
    ON s.banji_id=b.id
</select>
```

### 2.5 association常用属性

- **property**：实体类中关联对象的属性名称
- **javaType**：关联对象的实体类类型
- **column**：数据库查询字段（可为SQL起的别名）

```java
@SpringBootTest
public class MyBatisDemo {
    @Autowired
    private StudentMapper studentMapper;

    // 一对一关联查询测试
    @Test
    public void testOne2One() {
        List<Student> list = studentMapper.selectStudentBanjiInfo();
        for (Student student : list) {
            System.out.println(student);
        }
    }
}
```

输出结果：

```bash
Student [id=1, name=张三, age=21,  gender=男, banji=Banji [id=1,  name=java1807]]
Student [id=2, name=zhangsan,  age=12, gender=男, banji=Banji  [id=1, name=java1807]]
Student [id=4, name=王五2, age=12,  gender=男, banji=Banji [id=2,  name=java1812]]
```

## 三、一对多关联查询

### 3.1 业务关系

一个班级可以包含多名学生，属于**一对多关联**，班级为“一”，学生为“多”。

### 3.2 实体类补充

班级实体中嵌入学生集合，存储班级下所有学生信息

```java
public class Banji{
    private Integer id;
    private String name;
    // 一对多：一个班级对应多个学生
    private List<Student> studentList;
    
    // 省略 getter、setter、toString 方法
}
```

### 3.3 Mapper映射文件

MyBatis 使用 **collection 标签** 实现一对多集合关联封装

```xml
<mapper namespace="com.mapper.BanjiMapper">
    <!-- 自定义班级结果映射，关联班级下所有学生 -->
    <resultMap id="banjiMap" type="Banji">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <!-- 一对多关联学生集合，ofType 指定集合泛型类型 -->
        <collection property="studentList" ofType="Student">
            <id column="studentId" property="id"/>
            <result column="studentName" property="name"/>
            <result column="studentAge" property="age"/>
            <result column="studentGender" property="gender"/>
        </collection>
    </resultMap>

    <!-- 查询班级及对应所有学生信息 -->
    <select id="selectBanjiStudentInfo" resultMap="banjiMap">
        SELECT b.id,b.`name`,s.id AS studentId,s.`name` AS studentName,s.age AS studentAge,s.gender AS studentGender
        FROM student AS s INNER JOIN banji AS b
        ON s.banji_id=b.id
    </select>
</mapper>
```

### 3.4 collection常用属性

- **property**：班级实体中存储学生的集合属性名
- **ofType**：集合中存储的实体泛型类型（必须指定）

```java
@SpringBootTest
public class MyBatisOne2ManyTest {
    @Autowired
    private BanjiMapper banjiMapper;

    @Test
    public void testOne2Many() {
        // 查询所有班级及对应学生
        List<Banji> list = banjiMapper.selectBanjiStudentInfo();
        for (Banji banji : list) {
            // 打印班级信息
            System.out.println("班级信息：" + banji);
            // 打印班级下所有学生
            List<Student> studentList = banji.getStudentList();
            for (Student student : studentList) {
                System.out.println("班级学生：" + student);
            }
        }
    }
}
```

## 四、多对多关联查询

### 4.1 业务关系

班级和课程为**多对多关系**：一个班级可以选择多门课程，一门课程可以被多个班级选择。

### 4.2 核心原理

MyBatis 中**没有专门的多对多标签**，多对多的本质是：**通过中间表，拆解为两个一对多关系**。

- 班级 → 班级课程中间表：一对多
- 课程 → 班级课程中间表：一对多

### 4.3 核心实现思路

1. 通过中间表 `banji_course` 关联班级表和课程表
2. 实体类中互相嵌入对方集合（班级含课程集合、课程含班级集合）
3. Mapper 文件中通过 `collection` 标签，分别实现两组一对多关联查询
4. SQL 通过三张表联查（班级表、中间表、课程表）实现数据关联

## 五、多表查询标签总结

| 关联关系 | 核心标签           | 核心属性           | 适用场景                   |
| :------- | :----------------- | :----------------- | :------------------------- |
| 一对一   | association        | property、javaType | 实体包含单个关联对象       |
| 一对多   | collection         | property、ofType   | 实体包含关联对象集合       |
| 多对多   | collection（两次） | property、ofType   | 借助中间表，拆分两个一对多 |