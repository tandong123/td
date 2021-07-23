404：路径问题

405：代码问题

500：文件  导包问题 环境问题

Ambiguous ：  方法重复  二义性

# SpringMVC

ssm：mybatis + Spring + MVC三层架构



JavaSE  JavaWeb  SSM     SpringMVC Vue  SpringBoot  SpringCloud   Linux



Spring： ＩＯＣ　ＡＯＰ

SpringMVC的执行流程



MVC思想： Model（dao，service）		View（jsp）		Controller（Servlet）

​					业务逻辑  保存数据的状态		显示页面			取得表单数据  调用业务逻辑	转向指定页面

JSP：本质就是Servlet







### SpringＭＶＣ优点：

1. 轻量级，简单易学
2. 高效，基于请求响应的MVC框架
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大：RESful，数据验证，格式化，本地化，主题等
6. 简洁灵活

用户通过调度器请求Servlet



Spring：大杂烩，我们可以将SpringMVC中所有使用的



```xml

<!--处理器映射器-->
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>

<!--处理器适配器-->
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>


<!--视图解析器:DispatcherServlet给他的ModelAndView		模版引擎 Thymeleaf  Freemarker-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
   <!--前缀-->
   <property name="prefix" value="/WEB-INF/jsp/"/>
   <!--后缀-->
   <property name="suffix" value=".jsp"/>
</bean>
```



### Maven可能存在资源过滤的问题，以下配置完善

```xml

<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

### 替代默认适配器

```xml
 <!-- 让Spring MVC不处理静态资源     .css  .js  .html  .mp3  .mp4 -->
    <mvc:default-servlet-handler/>
    <!--
    支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping  处理器映射器
        和一个AnnotationMethodHandlerAdapter实例   处理器适配器
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
     -->
    <mvc:annotation-driven />
```



遇到404常见解决：

**· 导包配置**

![image-20210424140103089](https://gitee.com/zy0098/image_repo/raw/master/lib%E9%85%8D%E7%BD%AE%E5%9B%BE.png)



·TomCat项目配置

![image-20210424141313804](https://gitee.com/zy0098/image_repo/raw/master/TomCat%E6%89%93%E5%8C%85.png)



# 使用注解开发整体流程

#### **1、新建一个Moudle，springmvc-03-hello-annotation 。添加web支持！**

#### 2、由于Maven可能存在资源过滤的问题，我们将配置完善

#### 3、在pom.xml文件引入相关的依赖：主要有Spring框架核心库、Spring MVC、servlet , JSTL等。我们在父依赖中已经引入了！

#### **4、配置web.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

</web-app>
```



注意点：

**/ 和 /\* 的区别：**< url-pattern > / </ url-pattern > 不会匹配到.jsp， 只针对我们编写的请求；即：.jsp 不会进入spring的 DispatcherServlet类 。< url-pattern > /* </ url-pattern > 会匹配 *.jsp，会出现返回 jsp视图 时再次进入spring的DispatcherServlet 类，导致找不到对应的controller所以报404错。

- 注意web.xml版本问题，要最新版！

![image-20210424141751778](https://gitee.com/zy0098/image_repo/raw/master/%EF%BD%97%EF%BD%85%EF%BD%82%EF%BC%8E%EF%BD%98%EF%BD%8D%EF%BD%8C%E9%85%8D%E7%BD%AE.png)

- 注册DispatcherServlet
- 关联SpringMVC的配置文件
- 启动级别为1
- 映射路径为 / 【不要用/*，会404】

1. ### **添加Spring MVC配置文件**

2. 在resource目录下添加springmvc-servlet.xml配置文件，配置的形式与Spring容器配置基本类似，为了支持基于注解的IOC，设置了自动扫描包的功能，具体配置信息如下：

3. ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc
          https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
      <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
      <context:component-scan base-package="com.li.controller"/>
      <!-- 让Spring MVC不处理静态资源 -->
       
      <mvc:default-servlet-handler />
      <mvc:annotation-driven />
   
      <!-- 视图解析器 -->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
            id="internalResourceViewResolver">
          <!-- 前缀 -->
          <property name="prefix" value="/WEB-INF/jsp/" />
          <!-- 后缀 -->
          <property name="suffix" value=".jsp" />
      </bean>
   
   </beans>
   ```

4. - #### **在视图解析器中我们把所有的视图都存放在/WEB-INF/目录下，这样可以保证视图安全，因为这个目录下的文件，客户端不能直接访问。**

   - 让IOC的注解生效

   - 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 .....

   - MVC的注解驱动

   - 配置视图解析器

   - - 

1. #### 6.创建Controller

2. #### 编写Java控制类:com.li.controller.HelloController

7. ```java
   package com.li.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   @RequestMapping("/HelloController")
   public class HelloController {
   
       //真实访问地址 : 项目名/HelloController/h1
       @RequestMapping("/h1")
       public String sayHello(Model model){
           //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
           model.addAttribute("msg","hello,SpringMVCAnnotation");
           //web-inf/jsp/hello.jsp
           return "hello";
       }
   }
   ```

   

   

   

   

   

   ## Controller总结

   - 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
   - 控制器负责解析用户的请求并将其转换为一个模型。
   - 在Spring MVC中一个控制器类可以包含多个方法
   - 在Spring MVC中，对于Controller的配置方式有很多种

   

   ![image-20210425134523644](https://gitee.com/zy0098/image_repo/raw/master/Controller.png)

   ![image-20210425141430770](https://gitee.com/zy0098/image_repo/raw/master/Controller%E9%85%8D%E7%BD%AE.png)

   

```xml
//通过以下扫描器配合@Controller注解直接匹配省去bean的配置

<context:component-scan base-package="com.li.controller"/>
```

@Component   组件

@Service		service

@Controller	controller

@Repository    dao

```java
public class ControllerTest2 {

    @RequestMapping("/t2")
    public String test1(Model model){

        model.addAttribute("msg","ControllerTest2");

        return "test"; //WEB-INF/jsp/test
    }

    @RequestMapping("/t3")
    public String test2(Model model){

        model.addAttribute("msg","T3");

        return "test"; //WEB-INF/jsp/test
    }
}
```

我们的两个请求都可以指向一个视图，但是页面结果的结果是不一样的，从这里可以看出

**视图是被复用的，而控制器与视图之间是弱偶合关系。**

## RequestingMapping总结

- @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

控制类的加载

```java
@Controller
public class ControllerTest3 {

    @RequestMapping("/c/t1")
    public String test1(Model model){
        model.addAttribute("msg","ControllerTest3");
        return "test";//WEB-INF/c/t1
    }
}
```

@Requesting可以加在类的上方，也可以加在方法上方，不同公司规范不同



## RestFul 风格

**概念**

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，**只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。**

请求方式有所不同

**功能**

资源：互联网所有的事物都可以被抽象为资源

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。

分别对应 添加、 删除、修改、查询。



**使用路径变量的好处？**

1. - 使路径变得更加简洁；
   - 获得参数更加方便，框架会自动进行类型转换。
   - 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/commit/1/a，则路径与方法不匹配，而不会是参数转换失败。
   - 安全性有所提升（隐藏了后台参数）

在Spring MVC中可以使用  @PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上。

```java

//初试风格：   http://localhost:8080/add?a=1&b=2
//RestFul:    http://localhost:8080/add/a/b

@Controller
public class RestFulController {

//    @RequestMapping(value = "/add/{a}/{b}",method = RequestMethod.GET) 与以下简化注解同义
    //http://localhost:8080/add/128/2
    @PostMapping("/add/{a}/{b}")
    public String test(@PathVariable int a,@PathVariable int b, Model model){
        int res = a + b;
        model.addAttribute("msg","结果1：" + res);
        return "test";
    }

    //http://localhost:8080/add/128/2
    @GetMapping("/add/{a}/{b}")
    public String test2(@PathVariable int a,@PathVariable int b, Model model){
        int res = a + b;
        model.addAttribute("msg","结果2：" + res);
        return "test";
    }
}
// @PostMapping  @GetMapping 代表不同请求方法 括号中是最后执行的地址，不同方法可设定相同地址
```



**所有的地址栏请求默认都会是 HTTP GET 类型的。**

方法级别的注解变体有如下几个：组合注解

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

**@GetMapping 是一个组合注解，平时使用的会比较多！**

它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。



```java
//redirect 重定向
        model.addAttribute("msg","ModelTest");
        return "redirect:/a.jsp";
```

![image-20210425172627930](https://gitee.com/zy0098/image_repo/raw/master/11.png)

会自动转载路径，大于视图处理器配置权限





# 结果跳转方式





就对于新手而言简单来说使用区别就是：

```
Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；

ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；

ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。
```

当然更多的以后开发考虑的更多的是性能和优化，就不能单单仅限于此的了解。

**请使用80%的时间打好扎实的基础，剩下18%的时间研究框架，2%的时间去学点英文，框架的官方文档永远是最好的教程。**



```java
@Controller
@RequestMapping("/user")
public class UserController {

    //localhost:8080/user/t1 ? name=xxx
    //在类中用@RequestParam定义请求类名可避免无效请求返回空值
    @GetMapping("/t1")
    public String test1(@RequestParam("username") String name, Model model) {
        //1·接受前端参数
        System.out.println("前端参数为： " + name);

        // 2·将返回的结果传递给前端  Model
        model.addAttribute("msg", name);

        //3`跳转视图
        return "test";
    }

    /*   前端接受的是一个对象: id name age
        1接收前端用户传递的参数，判断参数名字，假设名字直接在方法上，可以直接使用
        2·结社传递的是一个对象User，匹配User对象中的字段名，与字段名一致则可以匹配`
    */
    @GetMapping("/t2")
    public String test2(User user) {
        System.out.println(user);
        return "test";
    }

    @GetMapping("/t3")
    public String test3(ModelMap map){
  
        return "test";
    }
}
```



http://localhost:8080/user/t1?username=zy

![image-20210425184928256](https://gitee.com/zy0098/image_repo/raw/master/%E8%B7%B3%E8%BD%AC1.png)

**如果传值（字段）名字不一样就报错**

![image-20210425185147477](https://gitee.com/zy0098/image_repo/raw/master/%E5%90%8D%E5%AD%97%E4%B8%8D%E4%B8%80%E8%87%B4%E6%8A%A5%E9%94%99.png)

第二种传值，只有字段名一致才会传值（不然就传空）；起到的作用：避免输入错误地址服务器依旧加载浪费内存

http://localhost:8080/user/t2?id=1&name=zy&age=22



![image-20210425185349338](https://gitee.com/zy0098/image_repo/raw/master/%E7%AC%AC%E4%BA%8C%E7%A7%8D%E4%BC%A0%E5%80%BC.png)

SpringMVC乱码过滤，一般情况下的乱码都可正常过滤

```xml
<!--    2.配置SpringMVC的乱码过滤-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```



# 前后端分离时代

后端部署后端，提供接口，提供数据



- JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛。
- 采用完全独立于编程语言的**文本格式**来存储和表示数据。
- 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

前端独立部署，负责渲染后端数据





# JSON的使用

## 简介

- JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛。
- 采用完全独立于编程语言的**文本格式**来存储和表示数据。
- 简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率



## 快速流程



### 首先 导入包

```xml
  <dependencies>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version>
        </dependency>
    </dependencies>
```



### xml默认配置

**web·xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

   <filter>
       <filter-name>encoding</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
           <param-name>encoding</param-name>
           <param-value>utf-8</param-value>
       </init-param>
   </filter>
   <filter-mapping>
       <filter-name>encoding</filter-name>
       <url-pattern>/</url-pattern>
   </filter-mapping>

</web-app>
```

**springmvc-servlet·xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

   <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
   <context:component-scan base-package="com.kuang.controller"/>

   <!-- 视图解析器 -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
         id="internalResourceViewResolver">
       <!-- 前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
       <!-- 后缀 -->
       <property name="suffix" value=".jsp" />
   </bean>

</beans>
```

**实体类**

```java
//Getting  Setting  省略
public class User {

   private String name;
   private int age;
   private String sex;
   
}
```

**测试**

```java
@Controller
public class UserController {
    @RequestMapping("/j1")
    //不走视图解析器，直接返回字符串
    @ResponseBody
    public String json1() throws JsonProcessingException {
        //jackson, ObjectMapper
        ObjectMapper mapper = new ObjectMapper();
        //创建对象
        User user = new User("ZY",5,"man");

        String str = mapper.writeValueAsString(user);
        return str;
    }
}
```

![image-20210426151428283](https://gitee.com/zy0098/image_repo/raw/master/Json.png)





### 乱码配置处理

**springmvc-servlet·xml**

```xml
<!--    JSON乱码问题配置-->
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>	
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```

### 日期转换

**工具类**

```java
public class JsonUtils {
    public static String getJson(Object object,String dateFormat){

        ObjectMapper mapper = new ObjectMapper();
        //不使用时间戳的方式
        mapper.configure(SerializationFeature.WRITE_DATE_KEYS_AS_TIMESTAMPS,false);
        //自定义时间戳格式
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);

        mapper.setDateFormat(sdf);

        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

**调用工具类输出**

```java
@RestController
public class UserController {
@RequestMapping(value = "/j3")
    public String json3() throws JsonProcessingException {
        Date date = new Date();

        return JsonUtils.getJson(date,"yyyy-MM-dd HH:mm:ss");
	｝
｝
```

![image-20210426163959801](https://gitee.com/zy0098/image_repo/raw/master/Data%E8%BD%AC%E6%8D%A2.png)



### 注解解析

```xml
@Controller 会经过视图解析器
@ResponseBody   配合@Controller使用
@RestController  不经过视图解析 直接返回JSON字符
```

在类上直接使用 **@RestController** ，这样子，里面所有的方法都只会返回 json 字符串了，不用再每一个都添加@ResponseBody ！我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！



## fastjson

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的。

```xml
<dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.60</version>
</dependency>
```



# SSM整合



## Mybatis层

### 新建数据库

```sql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

DROP TABLE IF EXISTS `books`;

CREATE TABLE `books` (
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARCHAR(200) NOT NULL COMMENT '描述',
KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```



### 环境配置

```xml
<dependencies>
   <!--Junit-->
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <version>4.12</version>
   </dependency>
   <!--数据库驱动-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.47</version>
   </dependency>
   <!-- 数据库连接池 -->
   <dependency>
       <groupId>com.mchange</groupId>
       <artifactId>c3p0</artifactId>
       <version>0.9.5.2</version>
   </dependency>

   <!--Servlet - JSP -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>servlet-api</artifactId>
       <version>2.5</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet.jsp</groupId>
       <artifactId>jsp-api</artifactId>
       <version>2.2</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jstl</artifactId>
       <version>1.2</version>
   </dependency>

   <!--Mybatis-->
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.2</version>
   </dependency>
   <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis-spring</artifactId>
       <version>2.0.2</version>
   </dependency>

   <!--Spring-->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-jdbc</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
</dependencies>
```



### 过滤器

```xml
<build>
   <resources>
       <resource>
           <directory>src/main/java</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
       <resource>
           <directory>src/main/resources</directory>
           <includes>
               <include>**/*.properties</include>
               <include>**/*.xml</include>
           </includes>
           <filtering>false</filtering>
       </resource>
   </resources>
</build>
```

### xml配置

```

```

    com.kuang.pojo
    com.kuang.dao
    com.kuang.service
    com.kuang.controller
####  **mybatis-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
   
   <typeAliases>
       <package name="com.li.pojo"/>
   </typeAliases>
   <mappers>
       <mapper resource="com/li/dao/BookMapper.xml"/>
   </mappers>

</configuration>
```

####  **applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

 **data.properties**

```
##如果使用的Mysql8.0+，需要加一个时区的配置  &serverTimezone=Asia/Shanghai
```

```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=123456
```

### 实体类

```java
public class Books {
   
   private int bookID;
   private String bookName;
   private int bookCounts;
   private String detail;
   
}
```

### DAO配置

#### **Mapper接口**

```java
public interface BookMapper {

    //增加一本书
    int addBook(Books books);

    //删除一本书
    int deleteBookById(@Param("bookId") int id);

    //更改一本书
    int updateBook(Books books);

    //查询一本书
    Books queryBookById(@Param("bookId") int id);

    //查询全部书
    List<Books> queryAllBook();

    Books queryBookByName(@Param("bookName") String bookName);
}
```

**Mapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.li.dao.BookMapper">

    <insert id="addBook" parameterType="Books">
        insert into ssmbuild.books (bookName, bookCounts, detail)
        values (#{bookName},#{bookCounts},#{detail});
    </insert>

    <delete id="deleteBookById" parameterType="int">
        delete from ssmbuild.books where bookID = #{bookId}
    </delete>

    <update id="updateBook" parameterType="Books">
        update ssmbuild.books
        set bookName=#{bookName},bookCounts=#{bookCounts},detail=#{detail}
        where bookID=#{bookID};
    </update>

    <select id="queryBookById" resultType="Books">
        select * from ssmbuild.books
        where bookID = #{bookId}
    </select>

    <select id="queryAllBook" resultType="Books">
        select * from ssmbuild.books
    </select>

    <select id="queryBookByName" resultType="Books">
        select * from ssmbuild.books where bookName = #{bookName}
    </select>

</mapper>
```

#### 编写Service层的接口和实现类

**接口**

```java
public interface BookService {

    //增加一本书
    int addBook(Books books);


    //删除一本书
    int deleteBookById(int id);

    //更新Book
    int updateBook(Books books);

    //查询一本书
    Books queryBookById(int id);


    //查询全部一本书
    List<Books> queryAllBook();

    Books queryBookByName(String bookName);
}

```

**实现类**

```java
//通过注解与spring-service.xml绑定
@Service
public class BookServiceImpl implements BookService{

    //service调用dao层，组合Dao
    @Autowired
    private BookMapper bookMapper;

    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }

    @Override
    public int addBook(Books books) {
        return bookMapper.addBook(books);
    }

    @Override
    public int deleteBookById(int id) {
        return bookMapper.deleteBookById(id);
    }

    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }


    @Override
    public Books queryBookById(int id) {
        return bookMapper.queryBookById(id);
    }

    @Override
    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }

    @Override
    public Books queryBookByName(String bookName) {
        return bookMapper.queryBookByName(bookName);
    }
}
```



## *Spring层*

#### **spring-dao**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

<!--    1.关联数据库-->
    <context:property-placeholder location="classpath:database.properties"/>

<!--    2、连接池   dbcp:半自动化，不能自动连接  c3p0自动化操作（自动化加在配置文件，并可以自动配置到对象中
   druid：    hikari-->

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <!-- 配置连接池属性 -->
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
    
    <!-- c3p0连接池的私有属性 -->
    <property name="maxPoolSize" value="30"/>
    <property name="minPoolSize" value="10"/>
    <!-- 关闭连接后不自动commit -->
    <property name="autoCommitOnClose" value="false"/>
    <!-- 获取连接超时时间 -->
    <property name="checkoutTimeout" value="10000"/>
    <!-- 当获取连接失败重试次数 -->
    <property name="acquireRetryAttempts" value="2"/>
 </bean>

<!--    3.sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

    <!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中  BookMapperImpl动态注入 -->
    <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.li.dao"/>
    </bean>

</beans>
```

#### **spring-service.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 1 扫描service相关的bean -->
    <context:component-scan base-package="com.li.service" />

    <!--2 BookServiceImpl注入到IOC容器中-->
    <bean id="BookServiceImpl" class="com.li.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

    <!-- 3 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--  注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
    </bean>

<!--   4 AOP -->
</beans>
```



## SpringMVC 层

#### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

<!--    DispatchServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--一定要注意:我们这里加载的是总的配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

<!--    乱码过滤-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>*/</url-pattern>
    </filter-mapping>

<!--    Session过期时间-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

#### **spring-mvc.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置SpringMVC -->
    <!-- 1.开启SpringMVC注解驱动 -->
    <mvc:annotation-driven />
    <!-- 2.静态资源默认servlet配置-->
    <mvc:default-servlet-handler/>

    <!-- 3.配置jsp 显示ViewResolver视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    </bean>

    <!-- 4.扫描web相关的bean -->
    <context:component-scan base-package="com.li.controller" />

</beans>
```

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>
    <import resource="spring-mvc.xml"/>
    <import resource="spring-service.xml"/>

</beans>
```

**基础框架配置完成**



# Controller 和 视图层编写

## 增删改查控制层代码

```java
@Controller
@RequestMapping("/book")
public class BookController {
    //controller 调 service层
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    //    查询全部书籍并返回一个书籍展示面
    @RequestMapping("/allBook")
    public String list(Model model) {
        List<Books> list = bookService.queryAllBook();
        model.addAttribute("list", list);
        return "allBook";
    }

    //跳转到增加书籍页面
    @RequestMapping("/toAddBook")
    public String toAddPaper(){

        return "addBook";
    }
    //添加书籍页面的请求
    @RequestMapping("/addBook")
    public String addBook(Books books){
        System.out.println("addBook=>" + books);
        bookService.addBook(books);
        return "redirect:/book/allBook"; //重定向到@RequestMapping（"allBook")  请求；
    }

    //跳转到修改页面
    @RequestMapping("/toUpdate")
    public String toUpdatePaper(int id,Model model){
        Books books = bookService.queryBookById(id);
        model.addAttribute("QBook",books);
        return "updateBook";
    }

    //修改书籍
    @RequestMapping("/updateBook")
    public String updateBook(Books books){
        System.out.println("updateBook=>" + books);
        bookService.updateBook(books);
        return "redirect:/book/allBook";
    }

    //删除书籍
    @RequestMapping("/deleteBook/{bookId}")
    public String deleteBook(@PathVariable("bookId") int id){
        bookService.deleteBookById(id);
        return "redirect:/book/allBook";
    }
    
     //查询书籍
    @RequestMapping("/queryBook")
    public String queryBook(String queryBookName, Model model){
        Books books = bookService.queryBookByName(queryBookName);

        System.err.println("book=>" + books);
        List<Books> list = new ArrayList<>();
        list.add(books);

        if (books == null){
            list = bookService.queryAllBook();
            model.addAttribute("error","未查到");
        }

        model.addAttribute("list",list);
        return "allBook";
    }
}
```



## JSP页面编写

#### allBook.jsp

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<body>
<head>
    <title>书籍展示</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

<<!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>

<div class="container">

    <div class="row clearfix">
        <div class="col-md-l2 column">
            <div class="page-header">
                <h1>
                    <small>书籍列表-------显示所有书籍</small>
                </h1>
            </div>
        </div>
        <div class="row">
            <div class="col-md-4 column">
                <%--                 toAddBook   --%>
                <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增书籍</a>
                <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/allBook">显示全部书籍</a>
            </div>

            <div class="col-md-8column"></div>
                <%--                 查询书籍   --%>
                <form action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float: right">
                    <span style="color: #ff0000;font-weight: bold">${error}</span>
                    <input type="text" name="queryBookName" class="from-control" placeholder="请输入要查询的书籍名称">
                    <input type="submit" value="查询" class="btn btn-primary">
                </form>
            </div>
        </div>

        <div class="row clearfix">
            <div class="col-md-l2 column">
                <table class="table table-hover table-striped">
                    <thead>
                    <tr>
                        <th>书籍编号</th>
                        <th>书籍名称</th>
                        <th>书籍数量</th>
                        <th>书籍详情</th>
                        <th>操作</th>
                    </tr>
                    </thead>
                    <%--书籍从数据库查找出来，从这个list中遍历出来：forEach--%>
                    <tbody>
                    <c:forEach var="book" items="${list}">
                        <tr>
                            <td>${book.bookID}</td>
                            <td>${book.bookName}</td>
                            <td>${book.bookCounts}</td>
                            <td>${book.detail}</td>
                            <td>
                                <a href="${pageContext.request.contextPath}/book/toUpdate?id=${book.bookID}">修改</a>
                                &nbsp; &nbsp;
                                <a href="${pageContext.request.contextPath}/book/deleteBook/${book.bookID}">删除</a>
                            </td>
                        </tr>
                    </c:forEach>

                    </tbody>
                </table>
            </div>
        </div>
    </div>

</body>
</html>

```



#### addBook.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>新增书籍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <<!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container">
    <div class="row clearfix">
        <div class="col-md-l2 column">
            <div class="page-header">
                <h1>
                    <small>新增书籍</small>
                </h1>

            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/addBook" method="post">
        <div class="from-group">
            <label>书籍名称：</label>
            <input type="text" name="bookName" class="form-control" required>
        </div>

        <div class="from-group">
            <label>书籍数量：</label>
            <input type="text" name="bookCounts" class="form-control" required>
        </div>

        <div class="from-group">
            <label>书籍描述：</label>
            <input type="text" name="detail" class="form-control" required>
        </div>

        <div class="from-group">
            <input type="submit" class="form-control" value="添加">
        </div>
    </form>
</div>

</body>
</html>
```



#### updateBook.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>修改书籍</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <<!-- 引入 Bootstrap -->
    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container">

    <div class="row clearfix">
        <div class="col-md-l2 column">
            <div class="page-header">
                <h1>
                    <small>修改书籍</small>
                </h1>
            </div>
        </div>
    </div>

    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
<%--隐藏域，用于传递书籍ID，不然传的ID默认值为0  无法完成更改--%>
        <input type="hidden" name="bookID" value="${QBook.bookID}">

        <div class="from-group">
            <label>书籍名称：</label>
            <input type="text" name="bookName" class="form-control" value="${QBook.bookName}" required>
        </div>

        <div class="from-group">
            <label>书籍数量：</label>
            <input type="text" name="bookCounts" class="form-control" value="${QBook.bookCounts}"  required>
        </div>

        <div class="from-group">
            <label>书籍描述：</label>
            <input type="text" name="detail" class="form-control" value="${QBook.detail}"  required>
        </div>

        <div class="from-group">
            <input type="submit" class="form-control" value="修改">
        </div>
    </form>
</div>

</body>
</html>
```



#### index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>首页</title>
    <style>
      a{
        text-decoration:none;
        color: black;
        font-size: 18px;
      }
      h3{
        width: 180px;
        height: 38px;
        margin: 100px auto;
        text-align: center;
        line-height: 38px;
        background: deepskyblue;
        border-radius: 5px;
      }
    </style>

  </head>
  <body>

  <h3>
    <a href="${pageContext.request.contextPath}/book/allBook">进入书籍页面</a>
  </h3>

  </body>
</html>
```



![image-20210501121859172](https://gitee.com/zy0098/image_repo/raw/master/20210501122315.png)![image-20210501121943242](https://gitee.com/zy0098/image_repo/raw/master/20210501121943.png)



# Ajax技术

- **AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。**
- AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
- **Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术。**
- 在 2005 年，Google 通过其 Google Suggest 使 AJAX 变得流行起来。Google Suggest能够自动帮你完成搜索单词。
- Google Suggest 使用 AJAX 创造出动态性极强的 web 界面：当您在谷歌的搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表。
- 就和国内百度的搜索框一样!

- 传统的网页(即不用ajax技术的网页)，想要更新内容或者提交一个表单，都需要重新加载整个网页。
- **使用ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新。**
- 使用Ajax，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面。



#### **作用**

- 注册时，输入用户名自动检测用户是否已经存在。
- 登陆时，提示用户名密码错误
- 删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面DOM中将数据行也删除。
- ....等等

**三要素**

URL  localhost：8080/a1

data:{key:vale}

callback  funcation(data)



至少应学的知识：  HTML（精通） + css（略懂） + js（静通）   

js：

- 函数： 闭包（）｛｝

- Dom

  - id,name,tag
  - create, remove

- Bom

  - window
  - document

  ​		

ES6: impotrt  require

Java全栈工程师  **后台开发 **		前端：html  css  JavaScript  js   jQuery		运维：项目发布  服务器如何运行一个项目  Linux



#### 注册提示效果,异步刷新界面

##### **Controller.java**

```java
@RestController
@RequestMapping("/a3")
public String a3(String name,String pwd){
    String msg = "";
    if (name != null){
        //admin,这些都应该在数据库中查
        if ("admin".equals(name)){
            msg = "ok";
        }else {
            msg = "用户名有误";
        }
    }
    if (pwd != null){
        //123,这些都应该在数据库中查
        if ("123".equals(pwd)){
            msg = "ok";
        }else {
            msg = "密码有误";
        }
    }
    return msg;
}
```

##### login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>

    <script src="${pageContext.request.contextPath}/statics/js/jquery-3.4.1.js"></script>

    <script>
        function a1() {
           $.get({
               url:"${pageContext.request.contextPath}/a3",
               data:{"name":$("#name").val()},
               success: function (data){
                   if(data.toString()==='ok'){
                       $("#userInfo").css("color","green");
                   }else{
                       $("#userInfo").css("color","red");
                   }
                   $("#userInfo").html(data);
               }
           })
        }
        function a2() {
            $.post({
                url:"${pageContext.request.contextPath}/a3",
                data:{"pwd":$("#pwd").val()},
                success: function (data){
                    if(data.toString()==='ok'){
                        $("#pwdInfo").css("color","green");
                    }else {
                        $("#pwdInfo").css("color","red");
                    }
                    $("#pwdInfo").html(data);
                }
            })
        }
    </script>
</head>
<body>

<p>
    用户名：<input type="text" id="name" onblur="a1()">
    <span id="userInfo"></span>
</p>

<p>
    密码：<input type="text" id="pwd" onblur="a2()">
    <span id="pwdInfo"></span>
</p>

</body>
</html>
```

实现了异步刷新功能，使得用户体验得到提升

![image-20210502204721893](https://gitee.com/zy0098/image_repo/raw/master/20210502204851.png)![image-20210502204756802](https://gitee.com/zy0098/image_repo/raw/master/20210502204902.png)



# 拦截器

**过滤器与拦截器的区别：**拦截器是AOP思想的具体应用。

**过滤器**

- servlet规范中的一部分，任何java web工程都可以使用
- 在url-pattern中配置了/*之后，可以对所有要访问的资源进行拦截

**拦截器** 

- 拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
- 拦截器只会拦截访问的控制器方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的



