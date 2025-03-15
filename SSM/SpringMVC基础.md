# SpringMVC

是一种轻量级的、基于MVC的Web层应用框架。它能让我们对请求数据的出来，响应数据的处理，页面的跳转等等常见的web操作变得更加简单方便。

## 基础配置

**pom.xml依赖**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.sangeng</groupId>
  <artifactId>springMVC-01</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springMVC-01 Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
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

  </dependencies>

  <build>
    <finalName>springMVC-01</finalName>
    <plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.1</version>
        <configuration>
          <port>80</port>
          <path>/</path>
        </configuration>
      </plugin>
    </plugins>
  </build>

</project>
```

**web.xml配置**

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

  <servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>

    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>

    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>

      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>

    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```

**spring-mvc.xml文件配置**

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

</beans>
```

**基本框架**

![image-20250315201217786](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152012859.png)	

TestController类代码

![image-20250315201229782](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152012819.png)	

输入http://localhost:80/ 正常跳转到index.jsp文件
输入http://localhost:80/hello 跳转到success.jsp文件
输入http://localhost/hello/?name=wxt 可以通过hello方法的参数String name获取到name的值,并且打印

----------

## DispatcherServlet原理

![image-20250315222033518](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152220675.png)

在SpringMVC中，DispatcherServlet (前端控制器)负责分发对应的handler处理
不管你handler方法返回的是什么String啊User对象啊，**都会封装成一个ModelAndView（请求域的数据和视图）**

![image-20250315202817329](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152028370.png)	

配置了<url-pattern>/<url-pattern>之后，DispatcherServlet会处理**除了jsp之外的所有请求，包括静态资源请求**，但默认情况下，Spring MVC只能处理找到对应的处理方法，没有内置的静态资源处理，访问.css、.js、.html等静态资源通常会返回404错误。

 **JSP的特殊情况**：直接访问.jsp文件时，容器会交给JspServlet处理，所以不会被DispatcherServlet拦截，可以正常访问到.jsp文件。

访问.css、.js、.html等静态资源的时候需要在spring-mvc.xml下配置静态资源处理

![image-20250315222512872](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152225918.png)	

-----

## RequsetMapping注解

### 指定请求路径

![image-20250315230242087](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152302125.png)	

---

### 指定请求方式

![image-20250315230307406](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152303450.png)	

两个参数的时候value不能省略,method是枚举.POST
只能捕获post请求的路径

![image-20250315230348554](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152303593.png)	

![image-20250315230402096](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152304142.png)	

---------

### 指定请求参数params

**必须有参数code**

![image-20250315230506562](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152305597.png)	

![image-20250315230512094](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152305140.png)	

**必须没有参数code**

![image-20250315231002147](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152310197.png)	

**参数必须是某个值**

![image-20250315231009970](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250315231009970.png)	

**参数必须不是某个值**

![image-20250315231022370](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152310421.png)	

------

### 指定请求头

在实际生活中，安卓手机和苹果手机,打开app给到不同的价格
如何识别安卓和苹果呢，我们可以通过请求头来区分

手机和网页点击按钮都是发送http请求

![image-20250315231155899](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152311959.png)	

**必须有请求头deviceType**

![image-20250315231300101](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152313148.png)	

**必须没有请求头**

![image-20250315231308908](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152313948.png)	

**请求头必须是ios**

![image-20250315231321756](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152313806.png)	

**请求头必须不是ios**

![image-20250315231439984](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152314030.png)	

**postman测试**

![image-20250315231352921](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152313968.png)	

---------

### 限制请求头Contnet-Type的值

Content-Type表示当前请求的数据类型,是文本还是html等
**consumes**就是限制Content-Type的值

**上传文件,Content-Type请求头必须是multipart/from-data**

![image-20250315231649252](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152316302.png)	

![image-20250315231653769](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152316811.png)	

**postman测试**

![image-20250315231701093](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152317134.png)	

--------

## 获取请求数据

### RestFul风格

RestFul是一种网络应用程序的设计风格和开发方式，现在很多互联网企业的**网络接口**定义都会符合其风格。

网络接口就是我们定义的这些映射方法

![image-20250315234331410](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152343452.png)	

![image-20250315234352315](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152343356.png)	

区分之前的user/?username=1
这种格式叫做**QueryString格式参数**

------

### 获取路径参数@PathVariable

![image-20250315234501995](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152345046.png)	

需要占位符{id}

![image-20250315234541783](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152345825.png)	

----------

获取多个路径参数

![image-20250315234613069](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152346108.png)	

![image-20250315234616709](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152346751.png)	

--------

### Json格式

![image-20250315234700934](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152347983.png)	

{}对象 "属性名":"属性值",
[]数组

-----

### 获取请求体中的json参数@RequestBody

获取json数据自动进行解析

需要配置

![image-20250315234730956](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152347997.png)	

![image-20250315234734730](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152347775.png)	

**@RequestBody**

需要封装成User对象

![image-20250315234805669](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152348710.png)	

![image-20250315234812379](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152348425.png)	

![image-20250315234920919](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152349967.png)	

控制台输出

![image-20250315234928014](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152349053.png)	

-----------

封装成map集合

![image-20250315234953947](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152349989.png)	

![image-20250315234958039](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152349080.png)	

-------

传递List集合

![image-20250315235046992](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152350038.png)	

![image-20250315235052018](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152350063.png)	

------

**注意事项**

如果需要使用@RequestBody来获取请求体中的Json并且进行转换，要求**请求头Content-Type的值必须为application/json**

![image-20250315235217673](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152352719.png)	

-----------

### 获取QueryString格式参数@RequestParam

请求路径中user/?username=1
这种格式叫做**QueryString格式参数**

![image-20250315235355877](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152353930.png)	

**方法参数名和请求参数名保持一致时可以省略注解**

![image-20250315235500878](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152355931.png)	

结果

![image-20250315235512293](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152355337.png)	

![image-20250315235540245](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152355289.png)	

-----------------

**封装成对象(不能加RequestParam)**

![image-20250315235710765](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152357815.png)	

![image-20250315235719158](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152357222.png)	

结果

![image-20250315235733407](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152357450.png)	

--------

### 相关注解的其他属性

#### 通用属性required

![image-20250315235935714](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503152359758.png)	

默认值为true，如果没有请求某个参数,就会报错,必须传参
设置为false就是可传可不传

![image-20250316000046605](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160000651.png)	

#### @RequestParam特有属性defaultValue

![image-20250316000105887](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160001928.png)	

如果没有传递对应的参数，我们可以用defaultValue属性设置默认值。

![image-20250316000132449](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250316000132449.png)	