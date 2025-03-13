# SpringAOP

## 概念

![image-20250313140655748](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131406781.png)	

## 快速入门

需求

![image-20250313140746461](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131407486.png)	

导入依赖

![image-20250313140802751](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131408778.png)	

xml文件开启aop支持

![image-20250313140825703](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131408729.png)	

**自定义aspect切面类**

在类上加上@Component和@Aspect
使用@Pointcut注解来指定要被增强的方法
使用@Before注解来给我们的增强代码所在的方法进行标识，并且指定了增强代码是在被增强方法执行之前执行的。

![image-20250313141350654](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250313141350654.png)	

这里的@Pointcut注解就定义了一个切点，表示在这些包下所有的方法增强
需要定义一个没有方法体的方法作为切入点表达式的一个标识
如果有很多个切点，就需要对应写多个无方法体的方法来做标识

结构

![image-20250313141404849](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131414872.png)	

service类代码加@Service注解

![image-20250313141416639](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131414664.png)	
![image-20250313141421238](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131414261.png)

测试类只调用deleteAll方法

![image-20250313141429629](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131414656.png)	

结果输出

![image-20250313141438825](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131414847.png)	

-----

## 专业名词

![image-20250313141538636](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131415665.png)	

![image-20250313141646212](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131416237.png)	

**代理类**

有了AOP增强的对象,通过.getBean获取的对象不是之前的对象,而是**代理对象** 
那么没有AOP增强的对象通过.getBean获取的对象是之前的对象

----------

## 切点

### 切点表达式

![image-20250313142453221](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131424252.png)	

![image-20250313142457652](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131424676.png)	

返回值类型,包名,类名,方法名,参数

![image-20250313142544924](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131425954.png)	

-----

### 切点函数

@**annotation**

可以自定义哪些方法需要增强

1.自定义注解@InvokeLog（名称任意）

![image-20250313143110763](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131431794.png)	

2.给需要增强的方法上增加我们的注解

![image-20250313143240912](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131432944.png)	

3.在切面类使用@annotation(自定义注解全类名)

![image-20250313143301383](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131433407.png)	

-------

## 通知

### 通知分类

![image-20250313143600452](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131436480.png)	

伪代码用于理解执行时机

![image-20250313143649786](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131436818.png)	

**环绕通知@Around**

方法参数获取ProceedingJoinPont对象
通过对象调用proceed()执行**目标方法**
然后try-catch
在任意位置写增强代码 

![image-20250313143954587](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131439615.png)	

通过给通知方法传递ProceedingJoinPont对象参数
然后在try-catch中用这个对象调用proceed()方法，也就是增强目标方法
就可以把增强的方法写在1234任意位置

--------

### 获取被增强方法的信息

只有获取了被增强方法的信息才能有效的增强方法,而不是简单的输出语句

我们可以在**除了环绕通知外的所有通知方法**中增加一个JoinPoint类型的参数。
这个参数封装了被增强方法的相关信息。
我们可以通过这个参数获取到除了异常对象和返回值之外的所有信息。

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131450256.png)	

一般通过.getSignature()获取签名封装对象后再去.get其他方法

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131453524.png" alt="image-20250313145359483" style="zoom:67%;" />	

-----

**获取增强方法的返回值和异常对象**

需要在方法参数上增加一个对应类型的参数
并且使用注解的属性进行配置。这样Spring会把你想获取的数据赋值给对应的方法参数。

![image-20250313145437702](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131454735.png)	

返回值在@AfterReturning里使用,异常对象在@AfterThrowing里使用
因为注解有多个参数,所以value不能省略了

**在通知方法中就可以通过传递参数用ret. 和t.去使用获取到的东西了**

--------

### 环绕通知获取信息

![image-20250313150554592](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131505632.png)	

该参数的proceed()方法被调用相当于被增强方法被执行
调用后的返回值就相当于被增强方法的返回值。

**注意事项**

如果有返回值,在通知方法也要有返回值
return的是通知方法的返回值,不是增强方法的返回值,只是获取到了增强方法的返回值通过通知方法返回
可以进行篡改

![image-20250313150733178](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131507223.png)	

## 注解方式配置AOP完整流程

结构

![image-20250313150802279](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131508308.png)	

依赖

![image-20250313150820020](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131508055.png)	

xml文件

![image-20250313150827322](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131508351.png)	

自定义注解

![image-20250313150835632](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131508659.png)	

切面类

![image-20250313150852915](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131508951.png)	

实现类

![image-20250313150913473](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131509509.png)	

工具类略

测试类

![image-20250313150926862](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131509896.png)	

### XML配置AOP（可跳过）

定义切面类
没有@Aspect@Pointcut@Around等注解,
但是要放到spring容器中,要加@Component注解

目标类和切面类都要放到容器中

![image-20250313151121278](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131511314.png)	

依赖

![image-20250313151139740](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131511774.png)	

xml文件配置

![image-20250313151147224](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131511264.png)	

--------

### 多切面顺序问题

在实际项目中我们可能会存在配置了多个切面类的情况。这种情况下我们很可能需要控制切面的顺序。
我们在默认情况下Spring有它自己的排序规则。（按照类名排序）

如果是注解方式配置的AOP可以在切面类上加@Order注解来控制顺序。@Order中的属性越小优先级越高。
如果是XML方式配置的AOP,可以通过调整配置顺序来控制。

下面这种配置方式就会先使用CryptAspect里面的增强，在使用APrintLogAspect里的增强

![image-20250313151220505](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131512538.png)	

