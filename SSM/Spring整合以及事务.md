# Spring整合以及事务

## Spring整合

### Spring整合Junit

导入依赖

![image-20250315103214988](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151032031.png)	

创建测试类

![image-20250315103222602](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151032633.png)	

也可以使用这种方法找到xml文件位置

![image-20250315103252646](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151032673.png)	

------

### Spring整合Mybatis

导入依赖

![image-20250315103318430](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151033473.png)	

JDBC配置文件

![image-20250315103326401](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151033427.png)	

spring核心xml文件配置

![image-20250315103340376](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151033415.png)	

mybatis核心配置文件

![image-20250315103353831](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151033852.png)	

dao接口和实现类

![image-20250315103425496](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151034518.png)	
![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151034265.png)	

实现类对应mapper文件

![image-20250315103449921](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250315103449921.png)	

-----

### sqlSessionFactoryBean

![image-20250315103532804](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151035836.png)	

基础的MyBatis中获取sqlSessionFactory对象

![image-20250315103539656](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151035680.png)	

Spring的xml文件中配置sqlSessionFactoryBean

![image-20250315103549610](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151035639.png)	

实际上就是new了一个sqlSessionFactoryBean对象
这里是通过getObject()来获取sqlSessionFactory对象

源码：
![image-20250315103646697](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151036732.png)	

----

### Srping解耦合

三层架构

![image-20250315103716410](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151037438.png)	

service实现类需要依赖dao实现类
没有spring的时候需要new dao接口的实现类

![image-20250315103735956](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151037993.png)	

servlet没有实现类,要依赖service实现类
new了service的实现类

![image-20250315103757217](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151037253.png)	

**spring改进测试类**

![image-20250315103812420](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151038446.png)	

-----

## Spring事务

### 注解实现事物

**spring配置文件**

![image-20250315104945069](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151049106.png)	

**@Transactional添加事务**

可以添加在方法和类上,添加到类就说明这个类所有的方法都支持事务
一般都在service层添加事务

![image-20250315104958368](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151049394.png)	

**事务的底层逻辑就是通过AOP增强解耦合**

所以要加上AOP依赖

![image-20250315105028019](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151050045.png)	

-----

### xml实现事务（可跳过）

![image-20250315105015069](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151050107.png)	

-----

### 传播行为**propagation**（重点）

当**事务方法嵌套调用**时，需要控制是否开启新事务，可以使用事务传播行为来控制

![image-20250315105200990](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250315105200990.png)	

![image-20250315105208039](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151052066.png)	

**@Transactional本质**

![image-20250315105222066](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151052105.png)	

我们需要**日志出现异常的时候不需要回滚转账事务**
就需要控制事务的传播行为

![image-20250315105253457](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151052489.png)	

默认情况下，外层test方法执行的时候有一个外层事务包裹，内层的转账和打印日志两个事务就加入这个外层的事务了，**本身不会有单独的事务包裹**

我们可以给转账事务设置REQUIRES_NEW
这时候这个转账事务就会在外层test事务的包裹下还会新建一个自身单独的事务
转账完成后会自动提交

![image-20250315111244423](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151112455.png)	

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151113737.png" alt="image-20250315111303693" style="zoom:67%;" />	

**实际上就是new了一个新的连接对象，log还是和外层用一个连接对象**

-----

### 隔离级别isolation

![image-20250315111407323](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151114351.png)	

![image-20250315111415811](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151114840.png)	

------------

### 只读readOnly

![image-20250315111506116](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503151115146.png)	



