# SpringBoot：MyBatis动态SQL

## 一、概述

MyBatis 动态 SQL 是指通过 MyBatis 提供的一系列标签，实现**按需拼接 SQL 语句**的功能，可根据传入参数的有无、数值动态调整 SQL 结构。

这类标签用法类似于 JSTL 标签，通过逻辑判断语句，解决传统硬编码 SQL 中参数拼接冗余、语法报错、灵活性差等问题，是 MyBatis 核心实用功能之一。

## 二、if标签+where标签

多条件模糊查询场景，查询条件不固定（姓名、年龄、性别可传可不传），需要动态拼接查询条件，避免 SQL 语法错误。

手动拼接条件时，会出现 **where 后多余 and** 的语法错误：

- 仅传性别参数：`select * from student where and gender='男';`（语法错误）
- 传统解决方案：硬编码判断首个条件，或冗余添加 `1=1` 恒真条件，代码繁琐冗余

**where 标签作用**：自动识别并移除条件首部多余的 `AND/OR`，同时保证无查询条件时，不生成 where 关键字，彻底规避语法错误。

```xml
<select id="selectByCondition" parameterType="Student" resultMap="studentMap">
    SELECT <include refid="studentColumns"/>
    FROM student
    <where>
        <if test="name!=null and name!='' ">
            AND name LIKE concat('%', #{name} ,'%')
        </if>
        <if test="age!=null">
            AND age=#{age}
        </if>
        <if test="gender!=null and gender!='' ">
            AND gender=#{gender}
        </if>
    </where>
</select>
```

> :warning:补充知识：sql标签
>
> ```xml
> <include refid="studentColumns"/>
> ```
>
> ​	上面这条是为了快速引用sql片段，可以避免固定的sql语句多次重复书写。在使用前需要在上文中加入sql标签：
>
> ```xml
> <sql id="studentColumns">
>     id, name, age, gender, banji_id
> </sql>
> ```

测试单元：

```java
@Test
public void testselectByCondition() throws IOException {
    Student stu = new Student();
    stu.setName("王");
    stu.setGender("女");
    List<Student> list = studentMapper.selectByCondition(stu);
    for (Student student : list) {
        System.out.println(student);
    }
}
```

## 三、set标签

动态更新数据：仅更新传入的非空字段，空字段保留数据库原有值，不覆盖为空。同时解决手动拼接 SQL 时，**字段末尾多余逗号**的语法问题。

**set 标签作用**：自动移除更新语句中最后一个字段后的多余逗号，同时自动拼接 SET 关键字，适配动态字段更新。

```xml
<update id="updateCondition" parameterType="Student">
    UPDATE student
    <set>
        <if test="name!=null and name!=''">
            name=#{name},
        </if>
        <if test="age!=null">
            age=#{age},
        </if>
        <if test="gender!=null and gender!='' ">
            gender=#{gender},
        </if>
    </set>
    WHERE id=#{id}
</update>
```

测试单元：

```java
@Test
public void testUpdateCondition() {
    Student student = new Student();
    student.setId(1);
    student.setName("小李1");
    student.setAge(25);
    // 不赋值gender，不更新该字段
    int count = studentMapper.updateCondition(student);
    System.out.println(count);
    sqlSession.commit();
}
```

## 四、foreach标签

用于处理**数组、集合**类型参数，核心场景：批量删除、批量新增、in 范围查询等。

- `collection`：必填，参数类型（数组填 `array`，集合填`list`）
- `item`：遍历后的单个元素变量名
- `open`：遍历起始拼接字符
- `close`：遍历结束拼接字符
- `separator`：元素之间的分隔符

```xml
<!-- 数组参数批量删除 -->
<delete id="deleteAllByArray">
    DELETE FROM student
    WHERE id IN
    <foreach collection="array" open="(" item="id" close=")" separator=",">
        #{id}
    </foreach>
</delete>

<!-- List集合参数批量删除 -->
<delete id="deleteAllByList">
    DELETE FROM student
    WHERE id IN
    <foreach collection="list" open="(" item="id" close=")" separator=",">
        #{id}
    </foreach>
</delete>
```

测试单元：

```java
// 数组参数测试
@Test
public void testDeleteAllByArray() {
   int[] array = {66};
   int count = studentMapper.deleteAllByArray(array);
   System.out.println("count: " + count);
   sqlSession.commit();
   sqlSession.close();
}

// 集合参数测试
@Test
public void testDeleteAllByList() {
   List<Integer> list = new ArrayList<Integer>();
   list.add(68);
   list.add(69);
   int count = studentMapper.deleteAllByList(list);
   System.out.println("count: " + count);
   sqlSession.commit();
   sqlSession.close();
}
```

## 五、choose、when、otherwise标签

等价于 Java 中的 `if...else if...else` 互斥逻辑，**只会执行第一个满足条件的分支**，所有条件互斥，不会多条件同时拼接。

查询优先级：姓名(name) > 年龄(age) > 性别(gender)，高优先级条件生效后，忽略所有低优先级条件。

```xml
<select id="selectByCondition2" parameterType="Student" resultMap="studentMap">
   SELECT <include refid="studentColumns"/>
   FROM  student
   WHERE
      <choose>
         <when test="name!=null and name!='' ">
            name LIKE concat('%', #{name}, '%')
         </when>
         <when test="age!=null"> 
            age=#{age}
         </when>
         <when test="gender!=null and gender!='' ">
            gender=#{gender}
         </when>
         <otherwise>
             -- 无任何条件时查询所有数据
         </otherwise>
      </choose>
</select>
```

测试单元：

```java
@Test
public void testSelectByCondition1() {
    Student student = new Student();
    // 未传姓名，生效年龄条件
    //student.setName("王");
    student.setAge(23);
    student.setGender("女");
    // 仅按年龄查询，性别条件失效
    List<Student> list = studentMapper.selectByCondition2(student);
    for (Student s : list) {
        System.out.println(s);
    }
}
```

## 六、trim标签

trim 是通用动态 SQL 标签，可**自定义拼接前缀、后缀，自动移除首尾指定字符**，可替代 where、set 标签，通用性更强。

- `prefix`：拼接 SQL 前缀（如 WHERE、SET、( ）
- `suffix`：拼接 SQL 后缀（如 ) ）
- `prefixOverrides`：移除语句首部指定字符（如 AND）
- `suffixOverrides`：移除语句尾部指定字符（如 ,）

### 6.1 场景1：动态插入（选择性插入字段）

只插入非空字段，自动拼接括号、移除字段末尾多余逗号

```xml
<insert id="addSelective" parameterType="Student">
    INSERT INTO student
    <trim prefix="(" suffix=")" suffixOverrides=",">
        <if test="id != null">id,</if>
        <if test="name != null and name != ''">name,</if>
        <if test="age != null">age,</if>
        <if test="gender != null and gender != ''">gender,</if>
    </trim>
    VALUES
    <trim prefix="(" suffix=")" suffixOverrides=",">
        <if test="id != null">#{id},</if>
        <if test="name != null and name != ''">#{name},</if>
        <if test="age != null">#{age},</if>
        <if test="gender != null and gender != ''">#{gender},</if>
    </trim>
</insert>
```

### 6.2 场景2：动态更新（替代 set 标签）

```xml
<update id="updateSelective" parameterType="Student">
    UPDATE student
    SET
    <trim suffixOverrides=",">
        <if test="name != null and name != ''">
            name = #{name},
        </if>
        <if test="age != null">
            age = #{age},
        </if>
        <if test="gender != null and gender != ''">
            gender = #{gender},
        </if>
    </trim>
    WHERE id = #{id}
</update>
```

### 6.3 场景3：动态查询（替代 where 标签）

```xml
<select id="selectAll" parameterType="Student" resultType="Student">
    SELECT * FROM student
    <trim prefix="WHERE" prefixOverrides="AND">
        <if test="name != null and name != ''">
            AND name LIKE concat('%', #{name}, '%')
        </if>
        <if test="age != null">
            AND age = #{age}
        </if>
    </trim>
</select>
```

### 6.4 动态插入测试代码

```java
@Test
public void testAddSelective() {
    Student student = new Student();
    student.setAge(23);
    student.setGender("女");
    // 不赋值姓名、id，仅插入已有字段
    int count = studentMapper.addSelective(student);
    System.out.println("count: " + count);
    sqlSession.commit();
    sqlSession.close();
}
```