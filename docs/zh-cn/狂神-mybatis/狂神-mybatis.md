

## 一·简介

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 二·第一个Mybatis程序

### 一 新建数据库

```sql
CREATE DATABASE `mybatis`;

USE `mybatis`;



CREATE TABLE `user`(

 `id` INT(20) NOT NULL PRIMARY KEY,

 `name` VARCHAR(30) DEFAULT NULL,

 `pwd` VARCHAR(30) DEFAULT NULL

 )ENGINE = INNODB DEFAULT CHARSET=utf8;

 

 INSERT INTO `user`(`id`,`name`,`pwd`) VALUES

 (1,'李珍','000'),

 (2,'王德发','123')
```



 



### 二搭建环境

1 新建普通的maven项目

2 删除src目录

3 导入依赖

```xml
 <!--父工程-->
    <groupId>org.example</groupId>
    <artifactId>Mybatis_Day01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!--导入依赖-->
    <dependencies>
        <!--mysql驱动-->
        <!--https://downloads.mysql.com/archives/c-j/ -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!--mybatis-->
        <!--https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
```

4 创建一个模块

##### 4·1编写mybatis核心文件  （数据库链接）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration核心配置文件-->
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--数据库配置  在url的后续信息配置中“=”左右不要加空格-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

##### 4·2编写mybatis配置文件

```java
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            //使用Mybatis第一步：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (IOException e){
            e.printStackTrace();
        }
    }
    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。

    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

##### 4·3编写代码

·实体类

```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

·Dao接口

resultType： 返回一个结果（用的多）	  resultMap： 返回一个集合

```java
public interface UserDao {
    List<User> getUserlist();
}
```

·接口实现类（由原来的UserDaolmpl装变为一个Mapper配置文件）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.li.dao.UserDao">
    <!--select 查询-->
    <select id="getUserList" resultType="com.li.pojo.User">
    select * from mybatis.user
  </select>
</mapper>
```

##### ·4.4测试

注意：Could not find resource mybatis.config.xml

Mapper

junit



maven由于约定大于配置，配置文件无法被导出或生效。解决方案

```xml
<!--在build中配置resources，防止资源导出失败   手动导出资源到resources-->
 <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

常见问题：

1.配置文件没有 注册

2.绑定接口错误

3.方法名不对

4.返回类型不对

5.Maven导出资源问题

## 三.CRUD

#### 1.namespace

中的报名要和Dao/mapper接口名一致

#### 2.select 选择查询

id:就是对应的namespace中的方法名

resultType：Sql语句执行的返回值！parameterType：参数类型!

##### 1.编写接口

```java
//根据id查用户
User getUserById(int id);
```

##### 2.编写对应的mapper中的sql语句

```xml
<!--select 根据id查询-->
<select id="getUserById" parameterType="int" resultType="com.li.pojo.User">
        select * from mybatis.user where id = #{id}
</select>
```

##### 3.测试

```java
@Test
public void getUserById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    User user = mapper.getUserById(1);
    System.out.println(user);

    sqlSession.close();
}
```

#### 3.Insert

```xml
<!--insert一个用户（通过对象) 其中对象中的属性可以直接取出来-->
<insert id="addUser" parameterType="com.li.pojo.User">
    insert into mybatis.user (id, name ,pwd) value (#{id}, #{name}, #{pwd});
</insert>
```

#### 4.update

```xml
<!--update一个用户（通过对象) 其中对象中的属性可以直接取出来-->
<update id="updateUser" parameterType="com.li.pojo.User">
    update mybatis.user set name = #{name}, pwd = #{pwd}  where id = #{id};
</update>
```

#### 5.Delete

```xml
<!--delete一个用户-->
<delete id="deleteUser" parameterType="com.li.pojo.User">
    delete from mybatis.user where id = #{id}
</delete>
```



**·增删改查需要提交事务！**      sqlSession.commit();



#### 6.万能Map

假设，实体类或数据库中的表字段或参数过多，考虑用map传递对象

```java
//万能Map
int addUser2(Map<String,Object> map);
```

```xml
<!--通过Map insert一个用户（通过对象) 其中对象中的属性可以
直接取出来   传递map的Key-->
<insert id="addUser" parameterType="map">
    insert into mybatis.user (id, name ,pwd) value (#{userid}, #{username}, #{password});
</insert>
```



## 四·配置解析

### 1.核心配置文件

- mybatis-config.xml

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

  ```text
  configuration（配置）
  properties（属性）
  settings（设置）
  typeAliases（类型别名）
  typeHandlers（类型处理器）
  objectFactory（对象工厂）
  plugins（插件）
  environments（环境配置）
  environment（环境变量）
  transactionManager（事务管理器）
  dataSource（数据源）
  databaseIdProvider（数据库厂商标识）
  mappers（映射器）
  ```

### 2.环境配置（environments）

mybatis可以配置成多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

学会使用配置多套运行环境

Mybatis默认的事务管理器是JDBC， 连接池：POOLED

### 3.属性（properties）

可以通过properties属性来实现引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置

编写一个配置文件

db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8
username=root
password=123
```

在核心配置文件中引入

```properties
<!--引入外部文件-->
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="123"/>
</properties>
```

- 可以直接引入外部文件

- 可以在其中增加一些属性配置

- 如果两个文件有同一字段，**优先使用外部配置文件**

  个人犯的错误：在配置完properties文件后测试发现并不能导出数据库信息，查询后发现在db.properties文件中的url地址是不需要加 `“”` 及其 `amp`字符，且每个属性应该用 `&` 连接，而不应该用 `；`

  需要注意的就是&amp转义字符的使用

  ***xml中url的设置***

  ```xml
  url="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"
  ```



### 4.类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

```xml
<!--给实体类起别名-->
<typeAliases>
    <typeAlias type="com.li.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，MyBatis会在包名下搜索需要的JavaBean：

扫描实体类的包，默认别名就为这个类的  类名，首字母小写

```xml
<!--给实体类起别名-->
<typeAliases>
    <typeAlias type="com.li.pojo"/>
</typeAliases>
```

**实体类比较少时推荐使用第一种方式；**

**实体类比较多时使用第二种方式**

第一种可以DIY别名，第二种不行，如要强行给需在实体上增加注解

```java
@Alias("user")
public class User{}
```



### 5.设置（settings）

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

![image-20210405165608254](https://gitee.com/zy0098/image_repo/raw/master/20210420080738.png)

![image-20210405165534724](https://gitee.com/zy0098/image_repo/raw/master/20210422115129.png)



### 6.其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)



### 7.映射器

MapperRegistry: 注册绑定我们的Mapper文件；

方式一：【推荐使用】

```xml
  <mappers>
       <mapper resource="com/li/dao/UserMapper.xml"/>
  </mappers>
```

方式二：使用class文件绑定注册

```xml
 <mappers>
		<mapper class="com.li.dao.UserMapper"/>
 </mappers>
```



注意点：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下

方式三：使用扫描包进行注入绑定

```xml
 <mappers>
	 <package name="com.li.dao"/>
 </mappers>
```

注意点：

- 接口和他的Mapper配置文件必须同名

- 接口和他的Mapper配置文件必须在同一个包下

  

## 五·解决属性名和字段名不一致的问题

### 1.问题

数据库中字段

![](https://gitee.com/zy0098/image_repo/raw/master/20210420080846.PNG)

项目中字段

```java
public class User {
    private int id;
    private String name;
    private String passWord;
}
```

测试出现问题

![](https://gitee.com/zy0098/image_repo/raw/master/20210420081031.PNG)

```xml
     select * from mybatis.user where id = #{id}
//类处理器
	 select id,name,pwd from mybatis.user where id = #{id}
```

解决方法：

- 起别名

```xml
 <!--select 根据id查询-->
    <select id="getUserById" parameterType="int" resultType="com.li.pojo.User">
              select id,name,pwd as passWord from mybatis.user where id = #{id}
    </select>

```

- ### resultMap

  结果集映射

  ```xml
  id name pwd
  id name passWord
  ```

  ```xml
  <!--结果集映射-->
      <resultMap id="UserMap" type="User">
          <!--column数据库中的字段，property实体类中的属性-->
          <result column="id" property="id"/>
          <result column="name" property="name"/>
          <result column="pwd" property="passWord"/>
      </resultMap>
  
      <!--select 根据id查询-->
      <select id="getUserById" parameterType="int" resultMap="UserMap">
                select * from mybatis.user where id = #{id}
      </select>
  ```

- resultMap元素是MyBatis中最重要最强大的元素
- ResultMap的设计思想是，对于简单的语句根本不需要配置显示的结果映射，而对于复杂一点的语句只要描述它们的关系就行了
- ResultMap 最优的地方在于，虽然你已经对它相当了解了，但是根本就不需要显式地用到他们。（哪个字段不一样映射哪个）



## 六·日志

### 6.1日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手！

曾经：sout、debug

现在：日志工厂

![image-20210406185349933](https://gitee.com/zy0098/image_repo/raw/master/20210420080854.png)

- SLF4J
- LOG4J   【掌握】
- JDK_LOGGING
- COMMONS_LOGGING
- STDOUT_LOGGING  【掌握】
- NO_LOGGING



在设置中设定具体使用哪个日志在Mybatis中实现

STDOUT_LOGGING标准日志输出

在mybatis核心配置文件中，配置我们的日志！

![image-20210406185319840](https://gitee.com/zy0098/image_repo/raw/master/20210420080901.png)

### 6.1LOG4J

- 什么是LOG4J？
- LOG4J是Apache的一个开源项目，通过使用LOG4J，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件
- 我们也可以控制每一条日志输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程
- 通过一个配置文件来灵活地进行配置，而不需要修改应用的代码

1.导入LOG4J的包(导入到总项目xml文件配置下)

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/log4j/log4j -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>
```

2.log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/li.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

3.配置log4j为日志的实现

```xml
<settings>
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

4.Log4j的使用！，直接测试运行刚才的查询

![image-20210407142119802](https://gitee.com/zy0098/image_repo/raw/master/20210420081023.png)

包含JDBC池的连接与断开

- 简单使用步骤

1. 在要使用Log4j 的类中，导入包  import org.apache.log4j.Logger;

2. 日志对象的参数为当前类class

   ```java
   static Logger logger = Logger.getLogger(UserMapperTest.class);
   ```

   

3. 日志级别

```java
logger.info("info:进入了testLog4j");
        logger.debug("debug:进入了testLog4J");
        logger.error("error:进入了testLog4J");
```



## 七·分页

目的：在进行数据量较大的操作时减少单次操作量

### 7.1 使用Limit分页

```sql
语法：select * from user limit startIndex，pageSize;
例如：select * from user limit 3; #[0,n]
```

使用Mybatis实现分页，核心SQL

1.接口

```java
//分页
    List<User> getUserByLimit(Map<String,Integer> map);
```

2.Mapper.xml

```xml
<!--//分页-->
    <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
        select * from  mybatis.user limit #{startIndex},#{pageSize}
    </select>
```

3.测试

```java
 @Test
    public void getUserByLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        HashMap<String, Integer> map = new HashMap<String, Integer>();
        map.put("startIndex",1); //从索引1开始查
        map.put("pageSize",2);//输出两个数据量

        List<User> userList =  mapper.getUserByLimit(map);
        for (User user : userList) {
            System.out.println(user);
        }

        sqlSession.close();
    }
```



### 7.2 RowBounds分页

不使用SQL实现  （此方法目前逐渐被淘汰）

1.接口

```java
  //分页2
    List<User> getUserByRowBounds();
```

2.Mapper.xml

```xml
<!--分页2-->
    <select id="getUserByRowBounds" resultMap="UserMap">
        select * from  mybatis.user
    </select>
```

3.测试

```java
  @Test
    public void getUserByRowBounds(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        //RowBounds实现
        RowBounds rowBounds = new RowBounds(1, 2);

        //通过Java代码层面实现分页
        List<User> userList = sqlSession.selectList("com.li.dao.UserMapper.getUserByRowBounds",null,rowBounds);

        for (User user : userList) {
            System.out.println(user);
        }

        sqlSession.close();
    }
```

### 7.3 分页插件

https://pagehelper.github.io/

![image-20210407153456094](https://gitee.com/zy0098/image_repo/raw/master/20210420080935.png)



## 八·使用注解开发

### 8.1 面向接口编程

真正开发中，面向接口编程应用更广泛

**解耦** **可拓展  提高复用性**

分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发更容易，规范性更好

**关于接口的理解**：****

- 从更深层次理解是定义（规范，约束）与实现的分离
- 接口本身反映了系统设计人员对系统的抽象理解



**三个面向对象区别**

- 面向对象指的是我们考虑现实问题时，以对象为单位去定义它的属性及其方法
- 面向过程指的是我们考虑现实问题时，以一个具体的事务过程为单位去考虑它的实现
- **接口设计与非接口设计是针对于复用技术而言的，与面向对象（过程）不属于同一问题下，更多是体现对系统的整体架构**



### 8.2 使用注解开发

1.直接在接口上实现

```java
 @Select("select * from user")
    List<User> getUsers();
```

2.需要在核心配置文件绑定接口

```xml
<!--绑定接口-->
    <mappers>
        <mapper class="com.li.dao.UserMapper"/>
    </mappers>

```

3.测试



本质上是底层的反射机制   动态代理



**Mybatis执行流程**

### 8.3 CURD（注解完成）





## 九.Lombok

使用步骤：

1. 在IDEA中安装Lombok插件！
2. 在项目中导入lombok的jar包

```xml
		<dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
```

3.直接在实体类上加注解

> @Data	无参构造，get，set，soString，hashcode，equals
>
> @AllArgsConstructor
>
> @NoArgsConstructor



## 十.多对一处理

- 多个学生，对应一个老师
- 对于学生这边而言，  **关联** ..  多个学生，关联一个老师  【多对一】
- 对于老师而言， **集合** ， 一个老师，有很多学生 【一对多】

![image-20210408145857161](https://gitee.com/zy0098/image_repo/raw/master/20210420080941.png)

SQL：

```sql
CREATE TABLE `teacher` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAteacherULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT INTO teacher(`id`, `name`) VALUES ('1', '秦老师'); 


CREATE TABLE `student` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  `tid` INT(10) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fktid` (`tid`),
  CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```

#### 测试环境搭建

1. 导入Lombok
2. 新建实体类Teacher，Student
3. 分别建立实体类的接口Mapper
4. 建立Mapper.xml
5. 在核心配置文件绑定注册Mapper接口/文件
6. 测试查询结果

#### 按查询嵌套处理

```xml
<!--思路：
         1. 查询所有学生信息
         2. 根据查询出的学生tid寻找对应的老师
         -->
    <select id="getStudent" resultMap="StudentTeacher">
        select * from mybatis.student
    </select>

    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
        <!-- 复杂属性单独处理对象：association   集合：collection-->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>

    <select id="getTeacher" resultType="Teacher">
        select * from mybatis.teacher where id = #{tid}
    </select>
```

#### 按查询结果嵌套处理

```xml
 <!--按照结果嵌套处理-->
    <select id="getStudent2" resultMap="StudentTeacher2">
        select s.id sid, s.name sname, t.name tname
        from mybatis.student s, mybatis.teacher t
        where s.tid = t.id
    </select>

    <resultMap id="StudentTeacher2" type="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="Teacher">
            <result property="name" column="tname"/>
        </association>
    </resultMap>
```



相比来讲，第二种更容易理解类似于  **Mysql联表查询**

第一种类似于  **Mysql子查询**

## 十一.一对多处理



## 十二.动态SQL

==**什么是动态SQL：动态SQL就是指根据不同的条件生成不同的SQL语句**==

利用动态 SQL 这一特性可以彻底摆脱这种痛苦。

```xml
动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

if
choose (when, otherwise)
trim (where, set)
foreach
```



## 缓存

### 1.简介

1. 什么是缓存 [ Cache ]？
   - 存在内存中的临时数据。
   - 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。
2. 为什么使用缓存？

   - 减少和数据库的交互次数，减少系统开销，提高系统效率。
3. 什么样的数据能使用缓存？

   - 经常查询并且不经常改变的数据。【可以使用缓存】



### 2.MyBatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）

  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。

  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

    

  #### 2.1一级缓存

  一级缓存也叫本地缓存：  SqlSession

  - 与数据库同一次会话期间查询到的数据会放在本地缓存中。
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

  

测试步骤：

1. 开启日志！
2. 测试在一个Sesion中查询两次相同记录

```java
 @Test
    public void queryById() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = mapper.queryById(1);
        System.out.println(user);
        System.out.println("============");
        User user1 = mapper.queryById(1);
        System.out.println(user1);
        System.out.println(user == user1);
        System.out.println(user.equals(user1));
        sqlSession.close();
    }
```



3.查看日志输出

![image-20210412185716983](https://gitee.com/zy0098/image_repo/raw/master/20210420081008.png)



缓存失效的情况：

1. 查询不同的东西
2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存！

```java
mapper.updateUser(new User(2,"zy","afijdg"));
```



![image-20210412185638576](C:\Users\筱宇\AppData\Roaming\Typora\typora-user-images\image-20210412185638576.png)



   3.查询不同的Mapper.xml

   4.手动清理缓存！

```java
sqlSession.clearCache();
```

![](https://gitee.com/zy0098/image_repo/raw/master/20210420080955.png)



小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段！

一级缓存就是一个Map。

#### 2.2二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中；

