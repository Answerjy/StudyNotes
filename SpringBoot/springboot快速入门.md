# SpringBoot

目前SSM缺点：

- 还需要写很多的配置才能进行正常的使用。
- 实现一个功能需要引入很多的依赖，尤其是要自己去维护依赖的版本，特别容易出现依赖冲突等问题。

SpringBoot就能很好的解决上述问题。

SpringBoot只对spring做了封装，没有对mybatis和springMVC进行封装

新增spring没有的机制：

- 内嵌web容器，不再需要部署到web容器中


- 提供准备好的特性，如指标、健康检查和外部化配置 

最大特点：**自动配置**、**起步依赖**

-------

## 快速入门

### 创建项目

![image-20250327102300611](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271023666.png)	

### 添加依赖

![image-20250327102435070](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271024104.png)	

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.sangeng</groupId>
    <artifactId>SpringBoot_web</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>SpringBoot_web</name>
    <description>SpringBoot_web</description>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
<!--        连接redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
<!--        web项目-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
<!--        start-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.4</version>
        </dependency>
<!--        连接mysql-->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
<!--        lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>
<!--        测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
<!--        mybatis整合-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter-test</artifactId>
            <version>3.0.4</version>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
<!--            springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

### 整体结构

![image-20250327102639646](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271026687.png)	

### 运行测试

1.直接**运行启动类的main方法**即可

2.打包运行

maven依赖

```xml
    <build>
        <plugins>
<!--            springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

maven打包

![image-20250327102818334](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271028376.png)	

![image-20250327102837049](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271028085.png)	

运行jar包
在jar包所在目录执行命令 

```
java -jar jar包名称
```

![image-20250327102939147](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271029179.png)	

浏览器测试

![image-20250327103015060](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271030098.png)	

----

## YML配置

YAML不是一种标记语言，通常以.yml为后缀的文件，是一种直观的能够被电脑识别的数据序列化格式，并且容易被人类阅读，容易和脚本语言交互的，可以被支持YAML库的不同的编程语言程序导入，一种**专门用来写配置文件的语言**。

YAML试图用一种比XML更敏捷的方式，来完成XML所完成的任务。

例如： 

~~~~yml
student:
    name: sangeng
    age: 15
~~~~

~~~~xml
<student>
    <name>sangeng</name>
    <age>15</age>
</student>
~~~~

### YML语法

- k: v 表示键值对关系，**冒号后面必须有一个空格**

- 使用空格的缩进表示层级关系，空格数目不重要，**只要是左对齐的一列数据，都是同一个层级的**

- 大小写敏感

- 缩进时**不允许使用Tab键，只允许使用空格**。(idea会自动把tab转化成空格)

- java中对于驼峰命名法，可用原名或使用-代替驼峰，如java中的lastName属性,在yml中使用lastName或 last-name都可正确映射。

- yml中注释前面要加#


### 键值关系

k: v：字面量直接写；

字符串默认不用加上单引号或者双绰号；

"": 双引号；转意字符能够起作用

name:   "sangeng \n caotang"：输出；sangeng 换行  caotang

''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据

~~~~yml
name1: sangeng 
name2: 'sangeng  \n caotang'
name3: "sangeng  \n caotang"
age: 15
flag: true
~~~~

#### 日期

只能解析/

~~~~yml
date: 2019/01/01
~~~~

#### 对象(属性和值)、Map(键值对)

多行写法：

在下一行来写对象的属性和值的关系，注意缩进 

~~~~yml
student:
  name: zhangsan
  age: 20
~~~~

行内写法：

~~~~yml
student: {name: zhangsan,age: 20}
~~~~

#### 数组、list、set

用- 值表示数组中的一个元素 

多行写法：

~~~~yml
pets:
  - dog
  - pig
  - cat
~~~~

行内写法：

~~~~yml
pets: [dog,pig,cat]
~~~~

#### 对象数组、对象list、对象set

~~~~yml
students:
 - name: zhangsan
   age: 22
 - name: lisi
   age: 20
 - {name: wangwu,age: 18}
~~~~

#### 占位符赋值

可以使用 **${key:defaultValue}** 的方式来赋值，若key不存在，则会使用defaultValue来赋值。

例如

~~~~yml
server:
  port: ${myPort:88}

myPort: 80   
~~~~

## SpringBoot读取YML

### @Value注解

注意使用此注解只能获取简单类型的值（8种基本数据类型及其包装类，String,Date）

~~~~yml
student:
  lastName: sangeng
~~~~

~~~~java
@RestController
public class TestController {
    @Value("${student.lastName}")
    private String lastName;
    @RequestMapping("/test")
    public String test(){
        System.out.println(lastName);
        return "hi";
    }
    
}
~~~~

**注意：加了@Value的类必须是交由Spring容器管理的**

### @ConfigurationProperties

yml配置

~~~~yml
student:
  lastName: sangeng
  age: 17
student2:
  lastName: sangeng2
  age: 15
~~~~

在类上添加注解@Component  和@ConfigurationProperties(prefix = "配置前缀")

~~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
    private String lastName;
    private Integer age;
}
~~~~

从spring容器中获取Student对象

~~~~java
@RestController
public class TestController {

    @Autowired
    private Student student;
    @RequestMapping("/test")
    public String test(){
        System.out.println(student);
        return "hi";
    }
}

~~~~

**注意事项：要求对应的属性要有set/get方法，并且key要和成员变量名一致才可以对应的上。**

----

## 连接mysql和redis数据库

### yml配置

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/springboot_db?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
  data:
    redis:
      host: 192.168.88.128
      port: 6379
      password: 123456
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0
          max-wait: 1000ms

mybatis:
  mapper-locations: classpath:mapper/*Mapper.xml       # mapper映射文件路径
  type-aliases-package: com.sangeng.domain             # 配置哪个包下的类有默认的别名
```

### 实体类

```java
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

### Mapper接口

```java
@Repository
@Mapper
public interface UserMapper {
    List<User> findAll();
}
```

注意在接口上加上@Mapper 和@Repository 注解

@Repository是spring的注解，是标记这是一个Dao接口类，和@Component等注解一样，都是把对象注入到spring容器中统一管理，语义化注解

@Mapper是Mybatis提供的注解，用于将一个接口标记为 MyBatis 的 Mapper 接口。被该注解标注后，MyBatis 会为这个接口自动生成代理实现类，开发者可以直接通过调用接口的方法来执行 SQL 语句。

### Mapper接口对应xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sangeng.mapper.UserMapper">
    <select id="findAll" resultType="com.sangeng.domain.User">
        select * from user
    </select>
</mapper>
```

存放目录

![image-20250327104725913](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271047971.png)	

-----

### Redis

注入RedisTemplate使用

```java
@Autowired
private StringRedisTemplate redisTemplate;

@Test
public void testRedis(){
     redisTemplate.opsForValue().set("name","张三");
}
```

-------

## 跨域请求

**浏览器**出于安全的考虑，使用 XMLHttpRequest对象发起 HTTP请求时必须遵守**同源策略**，否则就是跨域的HTTP请求，默认情况下是被禁止的。 同源策略要求源相同才能正常进行通信，即**协议、域名、端口号都完全一致**。 

### CORS解决跨域

CORS是一个W3C标准，全称是”跨域资源共享”（Cross-origin resource sharing），允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。它通过服务器增加一个特殊的Header[Access-Control-Allow-Origin]来告诉客户端跨域的限制，如果浏览器支持CORS、并且判断Origin通过的话，就会允许XMLHttpRequest发起跨域请求。

### SpringBoot使用CORS解决跨域

#### 1.使用@CrossOrigin

可以在支持跨域的方法上或者是Controller上加上@CrossOrigin注解

~~~~java
@RestController
@RequestMapping("/user")
@CrossOrigin
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/findAll")
    public ResponseResult findAll(){
        //调用service查询数据 ，进行返回
        List<User> users = userServcie.findAll();

        return new ResponseResult(200,users);
    }
}

~~~~

#### 2.使用 WebMvcConfigurer 的 addCorsMappings 方法配置CorsInterceptor（常用）

~~~~java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许时间
                .maxAge(3600);
    }
}
~~~~

**跨域允许时间**就是有些请求，比如put，会发送一个询问，问浏览器是否允许我跨域请求，每一次都询问会浪费时间，性能也会受影响
3600s内再次发送就不会询问

存放目录

![image-20250327105520607](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271055675.png)	

跨域请求会有一个**请求头**在这个页面所在的域

![image-20250326174445454](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271052725.png)	

服务器如果配置了cors，在**响应头**中会有以下信息
如果没有，就表示不允许跨域，请求失败

![image-20250326174431928](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271052949.png)	

----

## AOP

AOP详细知识学习见：https://blog.csdn.net/2302_79449634/article/details/146231880

在SpringBoot中默认是开启AOP功能的。如果不想开启AOP功能可以使用如下配置设置为false

~~~~yml
spring:
  aop:
    auto: false
~~~~

需要添加依赖

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
~~~~

-------

## 静态资源访问

由于SpringBoot的项目是打成jar包的所以没有之前web项目的那些web资源目录(webapps)。

那么我们的静态资源要放到哪里呢？

从SpringBoot官方文档中我们可以知道，我们可以把静态资源放到resources/static 或者 resources/public 或者 resources/resources

![image-20250327110359258](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271103338.png)	

### 修改静态资源访问路径

SpringBoot默认的静态资源路径匹配为/** 

例如想让访问静态资源的url必须前缀有/res。例如/res/index.html 才能访问到static目录中的。我们可以修改如下：

在application.yml中

~~~~yml
spring:
  mvc:
    static-path-pattern: /res/** #修改静态资源访问路径
~~~~

### 修改静态资源存放目录

我们可以修改 spring.web.resources.static-locations 这个配置来修改静态资源的存放目录。

例如:

~~~~yml
spring:
  web:
    resources:
      static-locations:
        - classpath:/sgstatic/ 
        - classpath:/static/
~~~~

-----

## 获取请求数据和返回到响应体

SpringBoot的web启动器已经默认导入了jackson的依赖，不需要再额外导入依赖了

具体详细知识以及API见：

https://blog.csdn.net/2302_79449634/article/details/146288427

https://blog.csdn.net/2302_79449634/article/details/146343214

----

## YML环境切换

在实际开发环境中，我们存在开发环境的配置，部署环境的配置，测试环境的配置等等，里面的配置信息很多时，例如：端口、上下文路径、数据库配置等等，若每次切换环境时，我们都需要进行修改这些配置信息时，会比较麻烦，profile的出现就是为了解决这个问题。它可以让我们针对不同的环境进行不同的配置，然后可以通过激活、指定参数等方式快速切换环境。

### 创建profile配置文件

我们可以用**application-xxx.yml**的命名方式 创建配置文件，其中xxx可以根据自己的需求来定义。

例如

我们需要一个**测试环境**的配置文件，则可以命名为：**application-test.yml**

需要一个**生产环境**的配置文件，可以命名为：**application-prod.yml**

我们可以不同环境下**不同**的配置放到对应的profile文件中进行配置。然后把不同环境下都**相同**的配置放到application.yml文件中配置。

![image-20250327112223411](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503271122510.png)	

application-test.yml

```yaml
server:
  port: 82
```

application-test.yml

主配置文件中激活test环境（这里演示改变端口号）

```yaml
  profiles:
    active: test
```

### 激活环境

我们可以再**application.yml**文件中使用**spring.profiles.active**属性来配置激活哪个环境。 

也可以使用虚拟机参数来指定激活环境。例如 ： **-Dspring.profiles.active=test**

也可以使用命令行参数来激活环境。例如： **--spring.profiles.active =test**

------

## 日志

~~~~yml
debug: true #开启日志
logging:
  level:
    com.sangeng: debug #设置日志级别
~~~~

