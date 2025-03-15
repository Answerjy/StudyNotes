# SpringIOC

## Spring简介

​	 Spring是一个开源框架，它由[Rod Johnson](https://baike.baidu.com/item/Rod Johnson)创建。它是为了解决企业应用开发的复杂性而创建的。 

​	 目前是JavaEE开发的灵魂框架。他可以简化JavaEE开发，可以非常方便**整合其他框架**，**无侵入的进行功能增强**。

​	 Spring的核心就是 控制反转(IoC)和面向切面(AOP) 。

## IOC控制反转

控制反转，之前对象的控制权在类手上，现在反转后到了Spring手上。

## 具体步骤

### 导入依赖

![image-20250312171403212](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312171403212.png)	

### 编写配置文件

![image-20250312171417878](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121714905.png)	
![image-20250312171432736](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121714774.png)
![image-20250312171444137](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121714162.png)

### 创建容器对象获取实现类对象完成测试

![image-20250312171456388](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121714416.png)	

---

## Bean的属性

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121715799.png)	
![image-20250312171558936](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121715959.png)

### scope

![image-20250312171654050](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121716076.png)	

**单例模式下**
执行第一句话创建容器的时候就会创建实现类对象
之后执行getBean方法不会创建

**多例模式下**
执行第一句话不会创建实现类对象
之后每执行一次getBean方法,创建一次对象

-------

## spring源码分析

![image-20250312172129966](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503121721016.png)	

测试方法中创建了**ioc容器的对象**,传入了beans.xml的path参数
调用getBean方法传入接口名,获得接口实现类对象,来调用实现类接口

---------

### classPathXmlApplicationContext容器源码

![image-20250312172355751](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172355751.png)	

创建了一个HashMap集合来存放对象

----------

### load方法

![image-20250312172450040](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172450040.png)	

document内容

![image-20250312172526104](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172526104.png)	

获取标签中的calss和id值

![image-20250312172617298](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172617298.png)	

通过反射机制创建对象

![image-20250312172625855](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172625855.png)	

把**id作为key**,创建出来的**对象作为value**存进最开始的beanMap集合中

![image-20250312172651472](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172651472.png)	

到这里**第一句创建对象就结束了**
第二句getBean传入id参数
就是从beanMap中根据id获取对象

![image-20250312172707985](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172707985.png)	

这里就完成了对象创建的解耦合
可以直接修改xml文件里的impl名

这里就可以直接newIOC容器传入beans.xml文件和id就可以获取到实现类对象了

![image-20250312172733366](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250312172733366.png)

----

## DI依赖注入

一些实现类比如service的实现类里面一般有dao的属性值好来调用crud

![image-20250313103921494](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131039561.png)	

比如这里service的实现类里面有dao的属性值
但是我们通过ioc创建对象的时候,没有给这些属性值赋值,**默认为null**
当需要用到userDao来调用方法的时候
会报空指针异常错误,就**需要通过DI依赖注入的方式来给这些属性值初始化**

-----

### Set方法注入

我们平时给属性值赋值的时候一般有两种方法

- 空参创建出对象,来调用set方法赋值
- 直接有参构造创建对象

可以在bean标签内用property标签来给属性值赋值

![image-20250313104125328](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131041370.png)	

这里其实就是调用了实现类的set方法,**如果实现类没有set方法,则报错**

---------

### ref引用数据类型赋值

假设我们在student里面创建一个dog类型的属性
可以在ref标签中写上spring容器中bean的id来注入

![image-20250313104237505](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131042535.png)	

---

### 有参方法注入

![image-20250313104253304](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131042349.png)	

<constructor-arg>

![image-20250313104325143](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131043176.png)	

---

### 复杂类型注入

实体类如下

![image-20250313104535339](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131045368.png)	
![image-20250313104515221](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131045252.png)

配置如下

Phone注入
![image-20250313104555758](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131045788.png)

List<String>集合注入
![image-20250313104719511](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131047540.png)

List<Phone>集合注入
![image-20250313104727325](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131047354.png)

Set<String>注入
![image-20250313104734098](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131047127.png)

Map<String,Phone>注入
![image-20250313104742963](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131047992.png)

数组int[]注入
![image-20250313104754914](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131047948.png)

properties类型注入
![image-20250313104800745](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131048774.png)

**小技巧:**
先写<property name>标签,空格看有没有可以写的属性,没有就结束标签,继续空格看有没有,有的话就写在标签内,没有就写在标签外

-----

### SPEL表达式

![image-20250313105852139](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131058173.png)	

可以进行简单的计算value="#{10+10}"
ref="car" 等价于value="#{car}"

--------

### Lombok插件

引入依赖
![image-20250313105953574](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131059602.png)

安装插件
![image-20250313110009215](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131100258.png)

使用方法
![image-20250313110020582](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131100616.png)

在JavaBean中使用
@Data就是编译时自动生成get,set,toString,Hashcode,equals等方法
@NoArgsConstructor 空参构造
@AllArgsConstructor 带参构造

----

## bean读取配置文件注入

**举例**

### DruidDataSource数据库连接池对象

![image-20250313111325126](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131113163.png)	

引入依赖
![image-20250313111338275](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131113314.png)

正常连接步骤,new连接池对象,set设置连接参数
![image-20250313111359862](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131113899.png)

可以丢给xml文件的bean来帮我们new对象并且注入值

---

JDBC配置文件
![image-20250313111419147](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131114187.png)

![image-20250313111423087](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131114120.png)	

**因为我们编译的时候,resource文件下的配置文件都会自动放到target目录的classes目录下**

![image-20250313111454922](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250313111454922.png)	

**完整步骤**

![image-20250313111550263](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131115322.png)	

---------

### 引入其他xml配置文件

当我们项目比较大的时候,会有很多个xml文件
所以需要有一个主的xml文件来**引入其他的xml文件**

比如上文的数据库连接的xml文件就可以单独放到一个jdbc.xml文件中
然后在主配置文件中引入这个jdbc.xml文件

![image-20250313111750030](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131117069.png)	

------

## 低频知识点

### bean的配置

#### name属性

也可以通过name来给bean取名字,name可以有多个
![image-20250313112044540](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131120575.png)

#### lazy-init

当我们是默认是单例模式的情况下,**创建容器的时候就会创建实现类对象**,之后执行getBean方法不会创建
使用lazy-init="true"的时候,**创建容器的时候不会创建实现类对象**,之后执行getBean方法创建
![image-20250313112112521](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131121557.png)

#### init-method

创建实现类对象的时候,spring会帮我们自动调用某一个方法,但是**必须是空参的!**
![image-20250313112141033](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131121069.png)

#### destroy-method

当对象**销毁前**的时候,自动调用方法,也**必须是空参的**
什么时候对象会销毁呢**,单例模式下**,只有**容器销毁的时候,对象才会销毁**
我们也可以调用.close()来关闭容器

![image-20250313112214682](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131122715.png)	

![image-20250313112228459](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131122502.png)	

-----

### 工厂类

这是一个car的类,里面有三个引用类型的属性,每个属性都有一个测试方法来调试

![image-20250313112351653](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131123704.png)	

当我们需要得到一辆车的时候,需要new Car,然后set属性,最后还要调试

![image-20250313112358442](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131123491.png)	

我们可以把这段代码封装到一个工厂类的getCar方法中

![image-20250313112408905](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131124954.png)	

在主方法中就可以直接创建这个工厂类,来**调用getCar方法**,获得一辆车

![image-20250313112421218](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250313112421218.png)	

这属于**实例工厂**,因为方法不是静态的,需要创建对象,才能getCar

我们可以**把getCar方法设置成静态方法**,就可以直接用类名来调用,这属于静态工厂

![image-20250313112447029](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131124065.png)	

------

#### spring使用工厂类

##### **实例工厂创建对象**

![image-20250313112527811](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131125849.png)	

我们可以让spring来执行上面这些代码

配置文件中先设置工厂类
再设置一个bean,但是这个**bean没有class**
**factory-bean:指定使用哪个工厂对象**
**factory-method:指定使用哪个工厂方法**
![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131127489.png)

测试类中
![image-20250313112732039](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131127077.png)	

这里**getBean使用的id是car**

-------

##### **静态工厂创建对象**

![image-20250313112807948](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131128988.png)	

不需要创建工厂对象了
配置文件中class指定工厂类的全类名,然后factory-method指定使用哪个工厂方法,要有一个id属性

![image-20250313112831047](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131128085.png)	

测试类中

![image-20250313112840750](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131128784.png)	

-----

## 注解开发

@**component**
![image-20250313113047557](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131130591.png)

等价于<bean...........id="student">

前提在配置文件中添加component-scan

![image-20250313113108260](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131131295.png)	

![image-20250313113201957](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131132998.png)	

### DI注解

简单属性注入**@value("")**
![image-20250313113225800](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131132837.png)
支持SPEL写法#{}

--------

@**Autowired**
引用数据类型注入(就是从spring容器中找满足这个类型的注入)
![image-20250313113416932](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131134971.png)

前提: 本类和引用类都要被注解修饰(放到spring容器里面才能被找到)
![image-20250313113508211](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131135261.png)

------

**required**属性代表这个属性是否是必须的，默认值为true。

如果是true的话Spring容器中如果找不到相同类型的对象完成属性注入就会出现异常。
![image-20250313113542838](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131135871.png)

如果改为false,就是找不到也无所谓,不报错,但是不会赋值,就是null

------

@**Qualifier**
当一个包下面有多个**同类型的对象**时候,@Autowired找不到,就需要@Qualifier来**指定某一个对象的具体id**
![image-20250313113702056](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131137099.png)

需要和@Autowired连用

------

### 替换xml文件

自定义创建一个ApplicationConfig配置类
![image-20250313113750066](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131137104.png)

@**Configuration**

标注在类上，表示当前类是一个配置类。我们可以用注解类来完全替换掉xml配置文件。
注意：如果使用配置类替换了xml配置，spring容器要使用：AnnotationConfigApplicationContext

测试类
![image-20250313113835474](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131138515.png)

@**ComponentScan**

可以用来代替context:component-scan标签来配置组件扫描。
basePackages属性来指定要扫描的包。

-----

### Bean注解

和@component@Service@Repository一样
都是代替<bean>标签放到spring容器中的,但是@Bean主要是用来**导入第三方类的**
比如DruidDataSource类

**xml文件中**
![image-20250313114035262](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131140328.png)

**ApplicationConfig配置类导入**

使用：定义一个方法，在方法中创建对应的对象并且作为返回值返回。然后在**方法上加上@Bean注解**，注解的value属性来设置bean的名称。
![image-20250313114125759](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131141802.png)

-----

如何选择？

![image-20250313114221693](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503131142744.png)	

## 
