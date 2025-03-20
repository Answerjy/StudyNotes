# SSM整合

## 步骤

①Spring整合上Mybatis

​	通过Service层Dao层都注入Spring容器中

②引入配置SpringMVC

​	把Controller层注入SpringMVC容器中

③让web项目启动时**自动读取Spring配置文件来创建Spring容器**

​	可以使用**ContextLoaderListener**来实现Spring容器的创建。

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202130468.png" alt="image-20250320213054386" style="zoom:67%;" />	

- 为什么要用两个容器？

  因为Controller如果不放在MVC容器中会没有效果，无法处理请求。而Service如果不放在Spring容器中，声明式事务也无法使用。

  

- SpringMVC容器中的Controller需要依赖Service，怎么从Spring容器中获取到所依赖的Service对象？

  **Spring容器相当于是父容器，MVC容器相当于是子容器**。子容器除了可以使用自己容器中的对象外还可以使用父容器中的对象。

## 模拟SpirnMVC子容器源码

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202134537.png" alt="image-20250320213441475" style="zoom:67%;" />	

**子容器有父容器的地址**，父容器没有子容器的地址

在ContextLoaderListener中，会在创建好spring容器后把容器存入servletContext域。这样在DispatcherServlet启动时，创建完SpringMVC容器后会从servletContext域中获取到Spring容器对象，设置其为父容器,这样子容器就能获取到父容器中的bean了。

------

## 所有配置代码

### mysql初始化代码，版本为8.0

```mysql
CREATE DATABASE /*!32312 IF NOT EXISTS*/`mybatis_db` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `mybatis_db`;
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `address` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;
insert  into `user`(`id`,`username`,`age`,`address`) values (1,'UZI',19,'上海'),(2,'PDD',25,'上海');
```

### 文件格式

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202138286.png)	

### 依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.sangeng</groupId>
  <artifactId>SSM</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>SSM Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>6.2.0</version>
    </dependency>
    <!--AOP相关依赖-->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.7</version>
    </dependency>
    <!-- spring-jdbc -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>6.2.0</version>
    </dependency>
    <!-- mybatis整合到Spring的整合包 -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>3.0.3</version>
    </dependency>
    <!--mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.10</version>
    </dependency>
    <!--log4j依赖，打印mybatis日志-->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
    <!--分页查询，pagehelper-->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>4.0.0</version>
    </dependency>

    <!--mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.33</version>
    </dependency>
    <!-- druid数据源 -->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.2.8</version>
    </dependency>


    <!-- spring整合junit的依赖 -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>6.2.0</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.30</version>
      <scope>provided</scope>
    </dependency>


    <!-- servlet依赖 -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
    <!--jsp依赖 -->
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.1</version>
      <scope>provided</scope>
    </dependency>
    <!--springmvc的依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.9.RELEASE</version>
    </dependency>

    <!-- jackson，帮助进行json转换-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>

    <!--commons文件上传，如果需要文件上传功能，需要添加本依赖-->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
    </dependency>
  </dependencies>


  <build>
    <finalName>SSM</finalName>
<!--    tomcat配置-->
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path>
          <uriEncoding>utf-8</uriEncoding>
          <!--          get请求乱码问题-->
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

```

###  applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--spring组件扫描，排除controller-->
    <context:component-scan base-package="com.sangeng">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:exclude-filter>
    </context:component-scan>

    <!--读取properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    <!--创建连接池注入容器-->
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClassName" value="${jdbc.driver}"></property>
    </bean>
    <!--spring整合mybatis后控制的创建获取SqlSessionFactory的对象-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactory">
        <!--配置连接池-->
        <property name="dataSource" ref="dataSource"></property>
        <!--配置mybatis配置文件的路径-->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>

    <!--mapper扫描配置，扫描到的mapper对象会被注入Spring容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" id="mapperScannerConfigurer">
        <property name="basePackage" value="com.sangeng.dao"></property>
    </bean>

    <!--开启aop注解支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>

    <!--声明式事务相关配置-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>

</beans>
```

### jdbc.properties

```properties
jdbc.url=jdbc:mysql://localhost:3306/mybatis_db?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=123456
```

### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--指定使用log4j打印Mybatis日志-->
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    <!--配置别名包,不需要使用全类名了-->
    <typeAliases>
        <package name="com.sangeng.domain"></package>
    </typeAliases>
    <plugins>
        <!-- 注意：分页助手的插件，配置在通用mapper之前 -->
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 指定方言 -->
            <property name="dialect" value="mysql"/>
        </plugin>
    </plugins>
</configuration>
```

### log4j.properties

```properties
### direct log messages to stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### direct messages to file mylog.log ###
log4j.appender.file=org.apache.log4j.FileAppender
log4j.appender.file.File=c:/mylog.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### set log levels - for more verbose logging change 'info' to 'debug' ###

log4j.rootLogger=debug, stdout

```

### spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--
         SpringMVC只扫描controller包即可
     -->
    <context:component-scan base-package="com.sangeng.controller"/>
    <!-- 解决静态资源访问问题，如果不加mvc:annotation-driven会导致无法访问handler-->
    <mvc:default-servlet-handler/>
    <!--解决响应乱码-->
    <mvc:annotation-driven>
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="utf-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>


    <!--配置视图解析器  前后端不分离项目使用-->
    <!--    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
            &lt;!&ndash;要求拼接的前缀&ndash;&gt;
            <property name="prefix" value="/WEB-INF/page/"></property>
            &lt;!&ndash;要拼接的后缀&ndash;&gt;
            <property name="suffix" value=".jsp"></property>
        </bean>-->

    <!--配置拦截器-->
        <mvc:interceptors>

            <mvc:interceptor>

                <mvc:mapping path="/**"/>
                <!--配置排除拦截的路径-->
<!--                <mvc:exclude-mapping path="/"/>-->
                <!--配置拦截器对象注入容器-->
                <bean class="com.sangeng.interceptor.MyHandlerInterceptor"> </bean>
            </mvc:interceptor>
        </mvc:interceptors>

    <!--
          文件上传解析器
          注意：id 必须为 multipartResolver
          如果需要上传文件时可以放开相应配置
      -->
<!--    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">-->
<!--    &lt;!&ndash; 设置默认字符编码 &ndash;&gt;-->
<!--    <property name="defaultEncoding" value="utf-8"/>-->
<!--    &lt;!&ndash; 一次请求上传的文件的总大小的最大值，单位是字节&ndash;&gt;-->
<!--    <property name="maxUploadSize" value="#{1024*1024*100}"/>-->
<!--    &lt;!&ndash; 每个上传文件大小的最大值，单位是字节&ndash;&gt;-->
<!--    <property name="maxUploadSizePerFile" value="#{1024*1024*50}"/>-->
<!--    </bean>-->
</beans>
```

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app PUBLIC
        "-//Java Community Process//DTD Web Application 3.0//EN"
        "http://java.sun.com/dtd/web-app_3_0.dtd">

<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                             http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">

  <display-name>Archetype Created Web Application</display-name>

  <!--配置spring的配置文件路径-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>

  <!--配置监听器，可以再应用被部署的时候创建spring容器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>


  <servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--
        为DispatcherServlet提供初始化参数的
        设置springmvc配置文件的路径
            name是固定的，必须是contextConfigLocation
            value指的是SpringMVC配置文件的位置
     -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
    <!--
        指定项目启动就初始化DispatcherServlet
     -->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <!--
         /           表示当前servlet映射除jsp之外的所有请求（包含静态资源）
         *.do        表示.do结尾的请求路径才能被SpringMVC处理(老项目会出现)
         /*          表示当前servlet映射所有请求（包含静态资源,jsp），不应该使用其配置DispatcherServlet
     -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>


  <!--乱码处理过滤器，由SpringMVC提供-->
  <!-- 处理post请求乱码 -->
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <!-- name固定不变，value值根据需要设置 -->
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <!-- 所有请求都设置utf-8的编码 -->
    <url-pattern>/*</url-pattern>
  </filter-mapping>


</web-app>

```

-------

## 编写Controller,Service，Dao

书写步骤：controller类，service接口，service实现类，dao接口，dao映射文件，最后写sql语句

### 响应格式统一

我们要保证一个项目中所有接口返回的数据格式的统一。这样无论是前端还是移动端开发获取到我们的数据后都能更方便的进行统一处理。

所以我们定义以下结果封装类ResponseResult放入common包

![image-20250320215207175](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202152238.png)	

```java
package com.sangeng.common;

import com.fasterxml.jackson.annotation.JsonInclude;


//某个属性的值不为null才会转换成json
@JsonInclude(JsonInclude.Include.NON_NULL)
//任何接口都会返回一个ResponseResult对象，统一格式
public class ResponseResult<T> {

    private Integer code; //状态码

    private String msg; //操作成功或失败的提示信息

    private T data; //返回的数据放到响应体中

    //没有空参构造

    public ResponseResult(Integer code, String msg) {
        //增删改，没有查到的数据
        this.code = code;
        this.msg = msg;
    }

    public ResponseResult(Integer code, String msg, T data) {
        //附带查到的数据
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

### 增删改查

根据id查找用户，查找所有用户，增加用户，删除用户，修改用户

#### Controller类

```java
package com.sangeng.controller;

import com.sangeng.common.ResponseResult;
import com.sangeng.domain.User;
import com.sangeng.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    //根据id查询用户
    @GetMapping("/user/{id}")
    public ResponseResult findById(@PathVariable("id") Integer id) {//从请求路径获取id
        User user = userService.findById(id);
        if(user == null) {
            return new ResponseResult(500,"没有该用户");
        }
        return new ResponseResult(200,"操作成功",user);//会把这个转换成json，这里的200其实一般定义成枚举
    }

    //查询所有用户
    @GetMapping("/user")
    public ResponseResult findAll() {
        List<User> list = userService.findAll();
        return new ResponseResult(200,"操作成功",list);
    }

    //增加用户
    @PostMapping("/user")
    public ResponseResult insertUser(@RequestBody User user) {//从请求体中获取user（json格式）
        userService.insertUser(user);
        return new ResponseResult(200,"操作成功");
    }


    //删除用户
    @DeleteMapping("/user/{id}")
    public ResponseResult deleteUser(@PathVariable("id") Integer id) {
        userService.deleteUser(id);
        return new ResponseResult(200,"操作成功");
    }

    //修改用户
    @PutMapping("/user")
    public ResponseResult updateUser(@RequestBody User user){
        userService.updateUser(user);
        return new ResponseResult(200,"操作成功");
    }
    
}
```

#### user类

```java
package com.sangeng.domain;


import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Integer id;
    private String username;
    private Integer age;
    private String address;
}

```

#### service接口

```java
package com.sangeng.service;

import com.sangeng.domain.User;

import java.util.List;

public interface UserService {

    User findById(Integer id);

    List<User> findAll();

    void insertUser(User user);

    void deleteUser(Integer id);

    void updateUser(User user);
}
```

#### service实现类

```java
package com.sangeng.service.impl;

import com.sangeng.dao.UserDao;
import com.sangeng.domain.User;
import com.sangeng.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao;

    @Override
    public User findById(Integer id) {
        return userDao.findById(id);
    }

    @Override
    public List<User> findAll() {
        return userDao.findAll();
    }

    @Override
    public void insertUser(User user) {
        userDao.insertUser(user);
    }

    @Override
    public void deleteUser(Integer id) {
        userDao.deleteUser(id);
    }

    @Override
    public void updateUser(User user) {
        userDao.updateUser(user);
    }
}
```

#### dao接口

```java
package com.sangeng.dao;

import com.sangeng.domain.User;

import java.util.List;

public interface UserDao {

    User findById(Integer id);

    List<User> findAll();

    void insertUser(User user);

    void deleteUser(Integer id);

    void updateUser(User user);
}
```

#### dao映射xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sangeng.dao.UserDao">
    <insert id="insertUser">
        insert into user values(null,#{username},#{age},#{address})
    </insert>
    <update id="updateUser">
        update user set username = #{username},age = #{age},address = #{address} where id = #{id}
    </update>
    <delete id="deleteUser">
        delete from user where id = #{id}
    </delete>
    <select id="findById" resultType="com.sangeng.domain.User">
        select * from user where id = #{id}
    </select>
    <select id="findAll" resultType="com.sangeng.domain.User">
        select * from user
    </select>
</mapper>
```

-------

### 分页查询用户

分页查询的结果除了要包含查到的用户数据外还要有当前页数，每页条数，总记录数这些分页数据。

所需依赖

```xml
    <!--分页查询，pagehelper-->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>4.0.0</version>
    </dependency>
```

mybatis-config.xml

```xml
    <plugins>
        <!-- 注意：分页助手的插件，配置在通用mapper之前 -->
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 指定方言 -->
            <property name="dialect" value="mysql"/>
        </plugin>
    </plugins>
```

需要进行分页数据的封装类PageResult

```java
package com.sangeng.common;

import java.util.List;

public class PageResult<T> {
    private Integer currentPage;//当前页数
    private Integer pageSize;//每页条数
    private Integer total;//总记录数

    private List<T> data;

    public PageResult(Integer currentPage, Integer pageSize, Integer total, List<T> data) {
        this.currentPage = currentPage;
        this.pageSize = pageSize;
        this.total = total;
        this.data = data;
    }

    public Integer getCurrentPage() {
        return currentPage;
    }

    public void setCurrentPage(Integer currentPage) {
        this.currentPage = currentPage;
    }

    public Integer getPageSize() {
        return pageSize;
    }

    public void setPageSize(Integer pageSize) {
        this.pageSize = pageSize;
    }

    public Integer getTotal() {
        return total;
    }

    public void setTotal(Integer total) {
        this.total = total;
    }

    public List<T> getData() {
        return data;
    }

    public void setData(List<T> data) {
        this.data = data;
    }
}
```

Controller方法

```java
    //分页查询
    @GetMapping("/user/{pageSize}/{pageNum}")//每页显示条数,当前页数
    public ResponseResult findByPage(@PathVariable("pageSize") Integer pageSize,@PathVariable("pageNum")Integer pageNum) {
        PageResult pageResult = userService.findByPage(pageSize,pageNum);
        return new ResponseResult(200,"操作成功",pageResult);
    }
```

Service实现类

```java
    @Override
    public PageResult findByPage(Integer pageSize, Integer pageNum) {
        PageHelper.startPage(pageNum, pageSize);
        List<User> list = userDao.findAll();
        PageInfo pageInfo = new PageInfo(list);
        PageResult pageResult = new PageResult(pageInfo.getPageNum(),pageInfo.getPageSize(),(int)pageInfo.getTotal(),list);
        return pageResult;
    }
```

执行（每页显示1条，查询第2页）

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202232246.png)	

查询结果

![image-20250320223331026](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202233139.png)	

------

## 异常统一处理

我们可以使用@ControllerAdvice实现对异常的统一处理。让异常出现时也能返回响应一个JSON。

自定义MyControllerAdvice类，接收所有异常

```java
package com.sangeng.exception;

import com.sangeng.common.ResponseResult;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

@ControllerAdvice
public class MyControllerAdvice {

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public ResponseResult handlerException(Exception e){
        return new ResponseResult(500, e.getMessage());
    }
}

```

遇到异常会把500和异常信息放到响应体中

![image-20250320223937235](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202239349.png)	

除0异常

--------

## 拦截器

自定义MyHandlerInterceptor类

```java
package com.sangeng.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyHandlerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

spring-mvc.xml配置

```xml
    <!--配置拦截器-->
        <mvc:interceptors>

            <mvc:interceptor>
                <!--
                -->
                <mvc:mapping path="/**"/>
                <!--配置排除拦截的路径-->
                <!--<mvc:exclude-mapping path="/"/>-->
                <!--配置拦截器对象注入容器-->
                <bean class="com.sangeng.interceptor.SGHandlerInterceptor"></bean>
            </mvc:interceptor>
        </mvc:interceptors>
```

----

## 声明式事务@Transactional

只能对service方法使用

```java
    @Transactional
    @Override
    public void test() {
        userDao.insertUser(new User(null,"test1",11,"aa"));
        System.out.println(1/0);//除0异常
        userDao.insertUser(new User(null,"test2",12,"bb"));
    }
```

-----

## AOP

自定义切面类

```java
package com.sangeng.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyAspect {

    @Pointcut("execution(* com.sangeng.service..*.*(..))")
    public void pt(){
    }

    @Before("pt()")
    public void before(){
        System.out.println("before");
    }
}
```

### 注意

**只能对service方法进行增强**，切点如果改成controller包下，AOP无效

不能对Controller进行增强，因为**切面类只会被放入父容器spring**，

![image-20250320224824222](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202248340.png)	这个是放入Spring容器中，被Spring容器扫描

而我们的Controller是在子容器中的。**父容器不能访问子容器**（子容器有父容器的地址，父容器没有子容器的地址）

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503202247879.png" alt="image-20250320224734744" style="zoom:67%;" />	

我们如果需要对Controller进行增强，使用**拦截器**也会更加的好用。

**拦截器其实就是AOP的封装**，注意不要掉入陷阱

