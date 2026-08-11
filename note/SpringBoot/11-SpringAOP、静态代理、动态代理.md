# SpringAOP 静态代理 动态代理

## 一、设计模式与代理模式概述

### 1.1 设计模式定义

设计模式是对软件开发中已有通用问题的固定、高效解决方法的总结，可复用、可优化代码结构，提升代码扩展性与维护性。

### 1.2 代理模式核心定义

为**目标对象**提供一个代理对象，通过代理对象控制对目标对象的访问。

> **核心特性：**不改变目标对象原有行为的前提下，对目标对象的功能进行增强。

### 1.3 代理模式核心角色

- **客户端**：发起请求的调用方，不直接访问目标对象，仅访问代理对象
- **目标对象**：真正实现核心业务逻辑的对象，仅专注核心功能
- **代理对象**：拦截客户端请求，在调用目标方法前后实现权限控制、日志、事务等增强功能

通俗案例：文件审批场景。客户无法直接找董事长（目标对象）审批文件，需通过秘书（代理对象）。秘书负责前期文件合规审核、后期事务收尾，真正的审批签字由董事长完成。

## 二、静态代理

### 2.1 实现原理

![image-20260811093052218](./../assets/11-SpringAOP%E3%80%81%E9%9D%99%E6%80%81%E4%BB%A3%E7%90%86%E3%80%81%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86/image-20260811093052218.png)

目标对象与代理对象**实现同一个业务接口**，保证对外行为一致性；代理类由开发者手动编写，编译阶段就确定代理关系。

> **核心作用：**保护目标对象、控制目标对象访问权限、对核心业务方法做前置/后置功能增强。

### 2.2 完整代码示例

#### 1. 统一业务接口

```java
// 签字业务统一接口
public interface IQianzi {
    void qianzi();
}
```

#### 2. 目标对象（核心业务实现）

```java
// 目标对象：董事长，仅实现核心签字业务
public class DongShiZhang implements IQianzi{
    @Override
    public void qianzi() {
        System.out.println("DongShiZhang.qianzi");
    }
}
```

#### 3. 代理对象（功能增强）

```java
// 代理对象：秘书，实现相同接口，完成功能增强
public class MiShu implements IQianzi{
    // 持有目标对象引用
    private DongShiZhang dongShiZhang;

    // 构造方法注入目标对象
    public MiShu(DongShiZhang dongShiZhang) {
        this.dongShiZhang = dongShiZhang;
    }

    @Override
    public void qianzi() {
        // 前置增强：开启事务、文件审核
        System.out.println("MiShu.qianzi before 开始事务");
        System.out.println("MiShu.qianzi 前期审核文件");
        
        // 调用目标对象核心业务方法
        dongShiZhang.qianzi();
        
        // 后置增强：收尾、关闭事务
        System.out.println("MiShu.qianzi 后期收尾处理");
        System.out.println("MiShu.qianzi after 关闭事务");
    }
}
```

#### 4. 客户端测试调用

```java
public class Client {
    public static void main(String[] args) {
        // 1. 创建目标对象
        DongShiZhang dongShiZhang = new DongShiZhang();
        // 2. 创建代理对象，注入目标对象
        IQianzi mishu = new MiShu(dongShiZhang);
        // 3. 客户端调用代理对象方法
        mishu.qianzi();
    }
}
```

#### 5. 运行结果

```
MiShu.qianzi before 开始事务
MiShu.qianzi 前期审核文件
DongShiZhang.qianzi
MiShu.qianzi 后期收尾处理
MiShu.qianzi after 关闭事务
```

### 2.3 静态代理优缺点

**优点：**在不修改目标对象代码的前提下，实现对目标对象功能的扩展，核心业务与增强业务解耦。目标对象仅专注核心逻辑，日志、事务、权限等通用功能由代理类实现。

**缺点：**

1. 冗余度高：一个目标对象必须对应一个手动编写的代理类，会产生大量代理类文件
2. 维护成本高：一旦公共接口新增/修改方法，目标对象和所有代理对象都必须同步修改

## 三、动态代理（JDK代理）

**动态代理**又称**JDK代理**。

### 3.1 核心特点

1.  无需手动编写代理类，JDK通过API在**内存中动态生成代理对象**
2.  使用条件：**目标对象必须实现接口**，因此也叫接口代理
3.  核心API：`java.lang.reflect.Proxy`

### 3.2 核心API方法说明

```java
// 动态生成代理对象的核心方法
static Object newProxyInstance( 	//新增一个代理实例
    ClassLoader loader,       // 目标对象的类加载器
    Class<?>[] interfaces,    // 目标对象实现的所有接口
    InvocationHandler h       // 方法调用处理器（实现增强逻辑）
)
```

### 3.3 动态代理工厂

```java
public class ProxyFactory {
    /**
     * 创建一个代理实例
     * @param target 被代理对象，可以传递任意对象，但是这个对象必须实现一个接口
     * @return  代理对象
     */
    public static Object getProxyInstance(Object target){
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(), //目标类的加载器
                target.getClass().getInterfaces(),  //目标类实现的接口
                new InvocationHandler() {
                    // Object proxy 代理对象
                    // Method method 调用的方法
                    // Object[] args 调用的方法的参数
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        long beginTime = System.currentTimeMillis();
                        System.out.println("前期审核");
                        System.out.println("开启事务");
                        //调用目标对象方法
                        Object returnValue = method.invoke(target, args);
                        System.out.println("提交事务");
                        System.out.println("后期收尾");
                        long endTime = System.currentTimeMillis();
                        System.out.println(endTime);
                        long takeTime = endTime - beginTime;
                        System.out.println("takeTime: " + takeTime);
                        return returnValue;
                    }
                }
        );
    }
}
```

`Proxy.newProxyInstance()` 用来**动态在内存里生成一个代理类、创建代理对象**；

`InvocationHandler` 是**代理对象所有方法被调用时的统一回调处理器**。

> `Proxy.newProxyInstance (ClassLoader, Class<?>[] interfaces, InvocationHandler h)`
>
> **参数 1：**`target.getClass().getClassLoader()` 类加载器
>
> 作用：**负责把动态生成的代理类加载到 JVM 内存**。
>
> 通俗理解：
>
> 要造一个全新的代理类，总得有人把这个类放进虚拟机。直接复用目标对象的类加载器就行。
>
> 固定写法：被代理对象的类加载器。
>
> ---
>
> **参数 2：**`target.getClass().getInterfaces()` 接口数组
>
> 作用：告诉 JDK，**代理类要实现哪些接口**。
>
> 通俗理解：
>
> 代理对象必须和目标对象实现一模一样的接口，外面调用时才能像使用原对象一样调用方法。
>
> ⚠️这也是 JDK 动态代理限制：**目标类必须实现接口，没有接口不能用 JDK 代理**。
>
> ---
>
> **参数 3：**`new InvocationHandler()` 调用处理器（最重要）
>
> 作用：**所有代理对象的方法，被别人调用时，都会跑到这里处理**。
>
> 代理对象本身没有业务逻辑，它只是一个 “外壳”，一调用方法就转发给这个处理器。

> `InvocationHandler` 接口和 `invoke ()` 方法
>
> 接口只有一个必须重写的方法：
>
> ```java
> public Object invoke(Object proxy, Method method, Object[] args) throws Throwable
> ```
>
> **什么时候执行 invoke？**
>
> ​	只要外部调用代理对象的任意方法 → 自动触发 invoke ()
>
> **invoke 的三个参数：**
>
> 1. Object proxy
>
>    当前生成的代理对象本身
>
> 2. Method method
>
>    当前被调用的那个方法的反射对象。
>
>    例子：代理调用 `userService.addUser()`，method 就代表 addUser 这个方法。
>
> 3. Object[] args
>
>    调用这个方法传入的所有参数，封装成数组。
>
>    `addUser (1,"张三") → args = [1,"张三"]`


### 3.4 场景测试案例

#### 1. 业务接口与实现类

```java
// 班级业务接口
public interface IBanjiService {
    void selectAll();
    void deleteById(Integer id);
}

// 目标对象：班级业务实现类
public class BanjiServiceImpl implements IBanjiService {
    @Override
    public void selectAll() {
        System.out.println("BanjiServiceImpl.selectAll：查询所有班级数据");
        try {
            Thread.sleep(2000); // 模拟业务耗时
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public void deleteById(Integer id) {
        System.out.println("BanjiServiceImpl.deleteById：删除班级，ID=" + id);
        try {
            Thread.sleep(256); // 模拟业务耗时
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```

#### 2. 动态代理测试

```java
import org.junit.Test;

public class ProxyTest {
    @Test
    public void test3() {
        // 创建目标对象
        IBanjiService banjiService = new BanjiServiceImpl();
        // 获取动态代理对象
        IBanjiService proxy = (IBanjiService) ProxyFactory.getProxyInstance(banjiService);
        // 调用业务方法
        proxy.selectAll();
        proxy.deleteById(1);
    }
}
```

### 3.5 动态代理优缺点

优点：无需手动创建大量代理类，一套代理工厂可适配所有实现接口的目标对象，极大减少代码冗余，降低维护成本。

缺点：存在强制限制：**目标对象必须实现接口**，无接口的类无法使用JDK动态代理。

## 四、Cglib代理（子类代理）

### 4.1 核心作用

弥补JDK动态代理的缺陷，**无需目标对象实现接口**。通过在内存中动态生成目标对象的子类，重写父类方法实现功能增强，也称为子类代理。

### 4.2 使用限制

1. 目标对象不能被`final`修饰（无法继承生成子类）
2. 目标对象中`final/static`修饰的方法无法被拦截，不能实现功能增强

### 4.3 Spring 代理选择规则

在SpringAOP编程中：

1. 若目标对象**实现了接口**：默认使用 **JDK动态代理**
2. 若目标对象**未实现接口**：自动使用 **Cglib代理**

## 五、Spring AOP 面向切面编程

### 5.1 AOP核心定义

AOP（Aspect Oriented Programming）面向切面编程，是基于代理模式的编程思想。核心目的：**将通用关注点代码（事务、日志、权限、性能统计）与核心业务代码彻底分离**，实现代码解耦、功能统一增强。

> **事务：**逻辑上的一组操作，保证**要么同时成功，要么同时失败**（失败之后会回滚） 

### 5.2 核心应用场景

事务管理、日志记录、权限校验、方法性能统计、参数校验等通用功能，无需嵌入业务代码，通过AOP统一增强。

> 之前的拦截器实现登录校验就使用了AOP的思想

### 5.3 AOP核心专业术语

- **连接点（JointPoint）**：目标对象中**所有可以被增强的方法**
- **切入点（Pointcut）**：目标对象中**真正被选中、需要增强的方法**（连接点的子集）
- **通知/增强（Advice）**：需要额外添加的通用功能代码（前置、后置、异常、环绕增强）
- **织入（Weaving）**：将通知代码应用到切入点的过程
- **代理（Proxy）**：织入增强逻辑后，生成的代理对象
- **切面（Aspect）**：**切入点 + 通知**，是AOP的核心单元



### 5.4 AOP核心价值案例（事务场景）

#### 1. 传统写法（代码耦合严重）

事务代码嵌套在业务代码中，每个转账业务都要重复写开启、关闭事务逻辑，冗余且难维护。

```java
public void transfer(A a, B b, double money) {
    // 通用事务代码（重复代码）
    开启事务;
    // 核心业务代码
    a账户减款;
    b账户加款;
    // 通用事务代码（重复代码）
    关闭事务;
}
```

#### 2. AOP优化写法（彻底解耦）

核心业务只保留纯业务逻辑，事务、日志等通用功能通过AOP切面统一增强，无需修改业务代码。

```java
public void transfer(A a, B b, double money) {
    // 仅保留核心业务代码
    a账户减款;
    b账户加款;
}
```

AOP自动在方法执行前开启事务，执行后关闭事务，异常时自动回滚。

## 六、三种代理模式总结对比

| 代理类型    | 实现方式                   | 使用条件                                  | 优点                           | 缺点                             |
| :---------- | :------------------------- | :---------------------------------------- | :----------------------------- | :------------------------------- |
| 静态代理    | 手动编写代理类             | 目标类与代理类实现同一接口                | 结构简单、易于理解、无依赖     | 代理类冗余、接口变更维护成本极高 |
| JDK动态代理 | JDK内存动态生成代理类      | 目标对象**必须实现接口**                  | 无需手动写代理类、代码复用性强 | 不支持无接口类代理               |
| Cglib代理   | 动态生成目标类子类实现代理 | 目标类不能为final，方法不能为final/static | 支持无接口类代理、适配范围广   | 无法代理最终类、最终方法         |

## 七、补充拓展

过滤器、拦截器的底层核心思想均基于**代理模式与AOP切面思想**，通过拦截请求、统一增强逻辑，实现业务与通用功能的解耦，是Spring框架核心功能的底层支撑。