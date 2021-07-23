# SSM图书管理整合



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
    
     <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
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
    
	<import resource="classpath:spring-dao.xml"/>
    <import resource="classpath:spring-mvc.xml"/>
    <import resource="classpath:spring-service.xml"/>
    
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

**jdbc.properties**

```xml
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssmbuild?useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=123
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



![image-20210501121859172](https://gitee.com/zy0098/image_repo/raw/master/20210501121859.png)![image-20210501121943242](https://gitee.com/zy0098/image_repo/raw/master/20210501121943.png)