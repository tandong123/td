# 1.Spring

## 1.1简介

Spring框架是一个开放源代码的J2EE应用程序框架，是针对bean的生命周期进行管理的轻量级容器（lightweight container），它解决了开发者在J2EE开发中遇到的许多常见的问题。

简单来说，Spring的目的就是为了**帮助解决企业应用开发的复杂性**。

开发者:Rod Johnson 在悉尼大学不仅获得了计算机学位，同时还获得了音乐学位，更令人吃惊的是在回到软件开发领域之前，他还获得了音乐学的博士学位。

理念:使现有的技术更加容易使用 本身是一个大杂烩,整合了现有的技术框架



## 1.2设计哲学

https://blog.csdn.net/Cap220590/article/details/107070114

1. 当你接触到一个框架的时候，不仅仅要知道这个框架是怎么使用的，更需要了解框架的设计原则，下面是Spring框架的设计原则：
2. 在每一层都提供选项。Spring可以让你尽可能的推迟选择。比如你可以通过配置文件来切换数据存储的提供方而不需要改动代码。这个规则也很好的应用在和第三方API集成中。
3. 适应不同的视角。Spring具有灵活性，它不会强制为你决定该怎么选择。它以不同的视角支持广泛的应用需求。
4. 保持强大的向后兼容性。Spring的发展经过了精心的管理，使得版本之间几乎没有割裂的变化。Spring支持精心选择的一系列JDK版本和第三方库，以便于维护依赖于Spring的应用程序和库。
5. 关心API设计。Spring团队投入了大量的思想和时间来制作直观的API，这些API可以在多个版本中使用，并可以使用很多年。
6. 为代码质量设定高标准。Spring框架将重点放在有意义、最新和准确的JavaDoc上。它是少数几个可以声明在包之间没有循环依赖关系的干净代码结构的项目之一。

```xml
		<dependencies> 
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
                <dependency>
                        <groupId>org.springframework</groupId>
                        <artifactId>spring-webmvc</artifactId>
                        <version>5.2.0.RELEASE</version>
                </dependency>
        </dependencies>

		<dependencies> 
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
                <dependency>
                        <groupId>org.springframework</groupId>
                        <artifactId>spring-jdbc</artifactId>
                        <version>5.2.0.RELEASE</version>
                </dependency>
        </dependencies>
```

## 1.2 优点

- Spring是一个开源的免费框架(容器)
- Spring是一个轻量级的,非侵入式的框架
- **控制反转(IOC)  面向切面编程(AOP)**
- 支持事务的处理,对框架整合的支持

<u>Spring是一个轻量级的 控制反转(IOC) 和面向切面(AOP)编程的框架</u>



## 1.3组成

![img](http://static.oschina.net/uploads/space/2014/1231/111732_x0O8_1249631.png)

- Spring Boot

  - 一个快速开发的脚手架
  - 基于SpringBoot可以快速的开发单个微服务
  - 约定大于配置

- Spring Cloud

  - ​	SpringCloud 是基于SpringBoot实现的

  

**学习SpringBoot的前提是掌握Spring及SpringMVC**  

弊端:发展太久以后,与原本来的理念背道而驰, 配置比较繁琐   戏称"配置地狱"



# 2.IOC理论推导

1.UserDao接口

2.UserDaoImpl实现类

3.UserService业务接口

4.UserServiceImpl业务实现类



最大的改变,使得原本开发中程序员要根据用户需求不断更改代码(显然这很耗费人力物力)转变为可根据用户不同需求程序被动改变



使用一个Set接口实现,已经发生革命性变化

```java
public class UserServiceImpl implements UserService {

    private UserDao userDao;
    //利用Set进行动态实现值的注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

从本质上解决了业务中人为管理对象创建的 不便性,系统耦合性也大大降低,可以更加专注于业务的实现



## IOC本质

**控制反转IoC(Inversion of Control)**是一种设计思想,**DI(依赖注入)** 是实现IoC的一种方法,也有人认为DI只是IOC的另一种说法,没有IoC的程序中,我们使用面向对象编程,对象的创建与对象的依赖关系完全硬编码在程序中,对象的创建由程序自己控制,控制反转后由程序自己控制,控制反转后对象的创建转移给第三方

(狂神)个人认为所谓控制反转就是:获得依赖对象的方式反转了

控制反转是一种通过描述(XML或注解)并通过第三方去生产或获取特定对象的方式.在Spring中实现控制反转的是IoC容器,其实现方法是依赖注入(Dependency Injection,DI)



# 3.HelloSpring

利用maven , 他会自动下载对应的依赖项

```xml
<dependency>
   <groupId>org.springframework</groupId>
   <artifactId>spring-webmvc</artifactId>
   <version>5.1.10.RELEASE</version>
</dependency>
```



### Hello实体类

```java
public class Hello {
   private String name;

   public String getName() {
       return name;
  }
   public void setName(String name) {
       this.name = name;
  }

   public void show(){
       System.out.println("Hello,"+ name );
  }
}
```



### 编写spring文件 

 这里我们命名为beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

   <!--bean就是java对象 , 由Spring创建和管理-->
   <bean id="hello" class="com.kuang.li.Hello">
       <property name="name" value="Spring"/>
   </bean>

</beans>
```



### 测试

```java
@Test
public void test(){
   //解析beans.xml文件 , 生成管理相应的Bean对象
   ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   //getBean : 参数即为spring配置文件中bean的id .
   Hello hello = (Hello) context.getBean("hello");
   hello.show();
}
```



- Hello 对象是谁创建的 ?  【hello 对象是由Spring创建的
- Hello 对象的属性是怎么设置的 ?  hello 对象的属性是由Spring容器设置的

这个过程就叫控制反转 :

- 控制 : 谁来控制对象的创建 , 传统应用程序的对象是由程序本身控制创建的 , 使用Spring后 , 对象是由Spring来创建的
- 反转 : 程序本身不创建对象 , 而变成被动的接收对象 .

依赖注入 : 就是利用set方法来进行注入的.

 IOC是一种编程思想，由主动的编程变成被动的接收

### 修改案例一

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    使用Spring创建对象,在Spring这些都称为Bean
            类型 变量名 = new 类型();
            id = 变量名    class = new Hello();
            property相当于给对象中的属性设置一个值
    -->

    <bean id="mysqlImpl" class="com.li.dao.UserDaoMysqlImpl"/>
    <bean id="oracleImpl" class="com.li.dao.UserDaoOracleImpl"/>
    <bean id="SqlserverImpl" class="com.li.dao.UserSqlserverImpl"/>

    <bean id="UserServiceImpl" class="com.li.service.UserServiceImpl">
        <property name="userDao" ref="SqlserverImpl"/>
    </bean>
    <!--        ref: 引用Spring容器中创建好的对象
            value:具体的值,基本数据类型-->

</beans>
```

测试

```java
public class MyTest {
    public static void main(String[] args) {
        //获取ApplicationContext
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //容器在手,天下我有,需要什么 get什么

        UserServiceImpl userServiceImpl = (UserServiceImpl) context.getBean("UserServiceImpl");

        userServiceImpl.getUser();
    }
}
```

# 4.IOC创建对象方式

通过无参构造函数

user.java

```java
public class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void show(){
        System.out.println("name: " + name);
    }
}
```

beans.xml

```xml
<!--第一种,下标赋值-->
<!--    <bean id="user" class="com.li.pojo.User">-->
<!--      <constructor-arg index="0" value="LI"/>-->
<!--    </bean>-->

<!--    第二种,通过类型创建   不建议使用-->
<!--    <bean id="user" class="com.li.pojo.User">-->
<!--        <constructor-arg type="java.lang.String" value="Lucy"/>-->
<!--    </bean>-->

<!--    第三种,之间通过参数名设置-->
    <bean id="user" class="com.li.pojo.User">
        <constructor-arg name="name" value="Liu"/>
    </bean>
```

测试类

```java
public class MyTest {
    public static void main(String[] args) {

        //Spring容器, 类似于婚介网站
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        User user = (User) context.getBean("user");
        user.show();
    }
}
```

![image-20210416101218648](https://raw.githubusercontent.com/Li-00/repo/master/img/20210416101218.png?token=AQ6X5OBH5J5IDXZ62MXCN33APDZL6)



通过有参构造方法来创建







## 7.4 用注解实习自动装配



jdk1.5支持的注解,Spring2.5就已经支持

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.

**使用注解须知**

1.导入约束

2.配置注解的支持    <context:annotation-config/>    很重要(不导入就无法使用注解)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



**@Autowrise**

直接在属性上使用   也可以在set方式上使用	 底层是反射原理 

使用Autowired 我们可以不用编写set方法,前提是你这个地洞装配的属性在IOC(Spring)容器中存在,且符合名字byname

科普:

```xml
@Nullable  字段标记了这个注解,说明这个字段可以为null
```

```java
public @interface Autowired{
    boolean required() default true;
}
```

测试代码

```java
public class People {
//显示Autowired(required = false) 说明这个对象的属性可以为null,否则不允许为空
    @Autowired(required = false)
    private Cat cat;
    @Autowired
    private Dog dog;

    private String name;
}
```



Auotowired注解是智能识别的,当注入在IoC 容器中该类型只有一个时,就通过byType进行匹配,当注入容器存在多个同一类型的对象时,就根据byName进行装配

如果@Auotowired自动装配环境比较复杂,自动装配比较复杂,无法通过一个注解[Auotowired]完成时候,就可以使用 @Qualifier(value="***") 去配合@Auotowired的使用,指定一个唯一的bean对象注入

```java
public class People {
//显示Autowired(required = false) 说明这个对象的属性可以为null,否则不允许为空
    @Autowired
    @Qualifier(value = "cat")
    private Cat cat;
    @Autowired
    private Dog dog;

    private String name;
}
```



小结:

```和
@Resource 和  @Autowired 
区别:@Resource默认通过byName的方式实现,如果找不到则通过byType实现;如果两个都找不到就报错{常用}
执行顺序不同:@Autowired 通过byType实现  @Resource默认通过byName的方式实现

相同:都是自动装配,都可以放在属性字段上
```



# 8. 使用注解开发

在Spring4后,要使用注解开发,必须保证AOP的包导入了





![image-20210416165030242](https://raw.githubusercontent.com/Li-00/repo/master/img/20210416165030.png?token=AQ6X5OD5X5IMG6RKRNFGN73APFIBK)



使用注解需要导入context约束,增加注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



1.bean

2.属性如何注入

```java
//等价于<bean id="user" class="com.li.pojo.User"/>
//@Component组件
@Component
public class User {

    public String name;
    //相当于 <property name="name" value="Zy"/>
    @Value("Zy")
    public void setName(String name) {
        this.name = name;
    }
}
```



3.衍生的注解

@Component有几个衍生注解,我们在web开发中,会按照MVC三层架构分层

- dao        [@Repository]
- service   [@Service]
- controller   [@Controller]

这四个注解的功能相同,代表将某个类注册到Spring中,装配Bean



4.自动装配注解

```xml
##注解说明
-@Autowired:自动装配通过类型,名字
    如果@Autowired不能唯一自动装配上属性,需要通过@Qualifier(value="***")
-@Nullable   字段标记这个属性代表这个字段可以为null
-@Resource   自动装配通过名字,类型
```

5.作用域

```java
@Component
@Scope("prototype")
public class User {

    public String name;
    //相当于 <property name="name" value="Zy"/>
    @Value("Zy")
    public void setName(String name) {
        this.name = name;
    }
}

```



6.小结

xml与注解:

- xml更加万能,适用于任何场合,维护更加简单
- 注解 不是自己类用不了,维护相对复杂
- xml用了管理bean
- 注解只负责完成属性的输入
- 我们在使用的过程中,只需要注意一个问题:必须让注解生效,就是需要开启注解的支持

```xml
<!--    指定要扫描的包,这个包下的注解就会生效-->
    <context:component-scan base-package="com.li"/>
    <context:annotation-config/>
```



# 9.使用Java的方式配置Spring

JavaConfig是Spring的一个子项目,在Spring4之后,它成为了一个核心功能

### 实体类

```java
//这个类被Spring接管注册到了容器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("Zy")//注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

### 配置类

```java
package com.li.config;

import com.li.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

//注册到Spring容器中,因为它本身是一个@Component
//Configuration代表这是一个配置类,和我们之前看的bean.xml相似
@Configuration
@ComponentScan("com.li.pojo")
@Import(MyConfig2.class)
public class MyConfig {

    //注册一个bean,相当于我们之前写的bean标签
    //这个方法相当于bean标签中的id属性  返回值相当于bean中的Class属性
    @Bean
    public User getUser(){
        return new User();
    }
}
```



### 测试

```java
public class MyTest {
    public static void main(String[] args) {
        //如果完全使用了配置类方式去做,我们只能通过AnnotationConfig上下文来获取容器,通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User getUser = context.getBean("user", User.class);
        System.out.println(getUser.getName());
    }
}
```

这种纯Java的配置方式在SpringBoot中随处可见  



# 10 代理模式

为什么学代理模式

代理模式就是SpringAOP的底层     [SpringAOP  SpringMVC]

分类:

- 静态代理
- 动态代理



## 10.1 静态代理

角色分析:

抽象角色:一般会使用接口或抽象类来解决

真实角色:被代理的角色

代理角色:代理真实角色,然后做一些附属操作

客户:访问代理对象的人



代码步骤:

1. 接口

   ```java
   //租房
   public interface Rent {
       public void rent();
   }
   ```

2. 真实角色

   ```java
   //房东
   public class Host implements Rent{
       public void rent(){
           System.out.println("出租房子");
       }
   }
   ```

3. 代理角色

   ```java
   public class Proxy implements Rent{
       private Host host;
   
       public Proxy() {
       }
   
       public Proxy(Host host) {
           this.host = host;
       }
   
       public void rent(){
           seeHouse();
           host.rent();
           heTong();
           fare();
       }
   
       //看房
       private void seeHouse(){
           System.out.println("中介带你看房");
       }
   
       private void heTong(){
           System.out.println("签合同");
       }
   
       //收中介费
       private void fare(){
           System.out.println("收中介费");
       }
   }
   ```

4. 客户端访问代理角色

   ```java
   public class Client {
       public static void main(String[] args) {
           Host host = new Host();
   
           //代理  中介帮房东出租房子,但是中间有一些附属操作
           Proxy proxy = new Proxy(host);
           //你不用面对房东,直接找中介
           proxy.rent();
       }
   }
   ```



静态代理模式优势:

- 可以使真实角色更加纯粹,不用关注一些公共业务

- 公共也就交给代理角色,实现业务分工

- 公共业务发生扩展时,方便集中管理

  

缺点:

- 一个真实角色会产生一个代理角色,代码量会翻倍--开发效率变低--



## 10.2加深理解

代码:  demo_02





## 10.3 动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的,不是直接写好的
- 动态代理分为两大类:  基于代理的动态代理   基于类的动态代理
  - 基于接口--JDK动态代理
  - 基于类: cglib
  - Java字节码实现:Javassist



需要了解两个类: Proxy  invocationHandier 



静态代理模式优势:

- 可以使真实角色更加纯粹,不用关注一些公共业务
- 公共也就交给代理角色,实现业务分工
- 公共业务发生扩展时,方便集中管理
- 一个动态代理类代理的是一个接口,一般就是对应的一类业务
- 一个动态代理可以代理多个类,只要是实现了同一个接口即可





# 11.AOP



### 11.1什么是AOP



### 11.2 AOP在Spring中的作用











### 11.3使用Spring实现AOP

[AOP]植入,需要导入一个包

```xml
   <dependencies>
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjjweaver      -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
    </dependencies>
```



方式一:使用Spring的 API 接口  【主要是Spring的 API】

方式二：自定义实现AOP  【主要是切面定义】

方式三：使用注解实现



# 12. 整合Mybatis

步骤：

1.导入相关jar包

- junit
- mybatis
- mysql数据库
- spring相关
- AOP织入
- mybatis-spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>Spring_Study</artifactId>
        <groupId>com.li</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>spring_10_mybatis</artifactId>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.0.RELEASE</version>
        </dependency>
<!--        Spring连接操作数据库的话，还需要一个spring-jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.0.RELEASE</version>
        </dependency>
        
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
        
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.2</version>
        </dependency>
    </dependencies>

</project>
```



2.编写配置文件

3.测试



### 12.1 回忆mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试



### 12.2 Mybatis-Spring

**中智科学院**

1. 编写数据源配置
2. sqlSessionFactory
3. sqlSessionTemplate
4. 需要给接口加实现类
5. 将自己写的实现类，注入到Spring类中
6. 测试



User实体类 ---> UserMapper接口 ----> UserMapper.xml ----->	Mybatis配置（Spring-dao.xml)  ---->  通过Spring实现类（UserMapMapperImpl）注入到Spring（applicationContext）---->通过Spring测试





# 13. 声明式事务



### 1.回顾事务

- 把一组业务当成一个业务来做   要么都成功，要么都失败
- 事务在项目开发中，十分重要，涉及数据的一致性问题，
- 确保完整和一致性



事务的ACID原则：

- A 原子性
- C 一致性
- I 隔离性
  - 多个业务可能操作同一个资源，防止数据损坏
- D 持久性
  - 事务一旦提交，结果不会受到系统异常运行影响   被持久化写到存储器中



### 2. spring 中的

- 声明事务： AOP
- 编程式事务： 需要在代码中，进行事务管理



为什么需要事务

- 如果不配置事务，可能存在数据提交不一致的情况下；
- 如果我们不在Spring中配置声明式事务，我们就需要在代码中手动配置事务
- 事务在项目中的开发中十分重要，设计到数据的一致性和完整性问题，不容马虎



567