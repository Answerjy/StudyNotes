# Java基础语法

## CMD命令

cls 清屏

cd 目录进入文件

cd.. 退回

dir 查看当前目录所有文件

E：进入E盘

exit 退出

<u>环境变量就是不用去专门的盘符去找，直接去环境变量里找到文件</u>

-----

## 语言优势

编译型语言c： 整体翻译

解释型语言python： 不用翻译直接把源文件交给操作系统

混合型语言java： 翻译成.class文件后交给**虚拟机**处理

<u>跨平台原理：针对不同的操作系统安装不同的虚拟机就可以了</u>

------

## JDK和JRE

JDK： JVM  核心类库  开发工具（包含运行工具）

JRE没有开发工具，只需要运行就可以了

----

## 计算机存储

数字是二进制保存，字母和汉字是asc码表保存的

二进制：0b开头

十进制：无前缀

八进制：0开头

十六进制（0-9和a-f组成）：0x开头

1字节 = 8比特

1kb = 1024字节

1mb = 1024kb

1gb = 1024mb

1tb = 1024gb

因为计算机采用的二进制计数，对于储存容量来说用2的幂次来划分更加自然和方便

2^10次方 = 1024

----

## 数据类型

byte 1字节

short 2字节

int 4字节

long 8字节

float 4字节

double 8字节

char 2字节

boolean 虚拟机没有明确规范，但是通常用1字节

**内存单位是字节，位是比特，所以float是32位，double是64位**

![image-20250227083444072](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012339754.png)	

![image-20250227083454538](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012339665.png)	

------

## 标识符规则

方法变量：小驼峰

类名：大驼峰

数字，字母，_ $组成

不能由数字开头

------

## 键盘录入

```java
Scanner sc = new Scanner(System.in);
int a = sc.nextInt();
```

nextInt(); 接收整数

nextDouble(); 接受小数

next(); 接收字符串，没有空格

nextLine(); 接收字符串，带空格

-----

## 小数运算不准确

![image-20250227084152787](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012340073.png)	

整数可以用二进制表示，对于小数二进制很大，无法用有限的二进制来表示小数，只能截取一部分，所以会有误差

float是4字节32位，double是8字节64位，所以多出来部分会截取

-----

## 隐式转换和强制转换

**隐式转换**（小 -> 大）

![image-20250227085051702](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012340693.png)	

![image-20250227085102315](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012340636.png)	![image-20250227085129753](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012340360.png)	

**强制转换**

![image-20250227085155675](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341498.png)	

数据过大会出现错误

--------

## 字符计算

![image-20250227085245047](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341414.png)	

记忆 大小写相差32，小写字母大

0——9 对应48——57

asc码表一共0——127，128个字符

**2^7 = 128**

---

## 方法

![image-20250227162757337](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341131.png)	

### 方法重载

![image-20250227162839608](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341692.png)	

方法会进入栈内存，从main方法开始，执行完毕出栈，**先进后出**

**引用数据类型**保存在堆内存，栈内存指向堆内存地址

### 方法值的传递

传递基本数据类型传递的是真实值，形参改变不会影响实际参数

传递引用数据类型传递的是地址值，形参改变会影响实际参数

-----

## this

代表方法调用者的地址值

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341535.png" alt="image-20250227164047396" style="zoom:50%;" />	

![image-20250227164118686](C:\Users\Answer\AppData\Roaming\Typora\typora-user-images\image-20250227164118686.png)	

### 成员变量默认初始值

整数类型：0

浮点类型：0.0

字符类型：\u0000 空字符，对应ASCII码值为0

布尔类型：false

引用数据类型：null

-------

## 构造方法

**创建对象的时候给成员变量初始化的**

没有构造方法虚拟机会自动创建一个空参构造

如果有带参构造，就不会创建空参构造，用空参构造会报错

![image-20250227164745808](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341839.png)	

## 对象的内存关系

类加载：.class文件在**方法区**加载，static，final修饰的成员变量是属于类的，所以存放在方法区

创建对象：在**堆内存**中开辟空间存放对象的成员变量，**成员方法在方法区中**

堆内存保存的是成员变量的具体数据，成员方法是保存在方法区的，**但是成员变量的信息也会保存在方法区里**

实例：单独的对象（堆内存）

静态：属于类，整体（方法区）

--------

## 局部变量和成员变量

局部变量是在栈内存中

成员变量是在堆内存中

生命周期：局部变量随着方法消失而消失，成员变量随着对象的消失而消失

作用域：局部变量是整个方法，成员变量是整个类

main方法中定义的int a = 10；属于局部变量

创建对象的时候，需要定义这个类的局部变量去**指向**new出来的对象

![image-20250227170921148](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341910.png)	

![image-20250227171217799](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341853.png)	此时没有被局部变量指向，但是堆内存中还是会开辟一定空间创建对象

但是后续JVM会执行垃圾回收，在合适的时机回收该对象占用的堆内存空间

------

# 面向对象进阶

## static静态变量

属于整个类，随着类加载而加载，可以直接用**类名.**来调用

静态变量随着类加载而加载，优先于对象的创建

**静态变量会随着类的加载而加载到堆内存的静态区中**

工具类方法一般定义为静态方法
![image-20250301121543568](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341012.png)	

![image-20250301121620062](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341585.png)	

**静态方法会去静态区找静态变量**，此时没有new关键字，所以堆内存中没有开辟对象的储存空间，无法找到

当new了一个对象后，堆内存这块对象地址可以访问到静态区的静态变量的内容，所以非静态可以访问静态

### 静态区

事实上没有静态区这个概念，在jvm的虚拟内存中，有栈内存，堆内存，方法区，这三个

栈内存主要存放局部变量和方法调用的上下文，堆内存用于存储对象实例，方法区用于存储类的相关信息

实例对象new的时候会申请在堆内存开辟空间来存储实例变量（成员变量）

当 Java 虚拟机加载一个类时，会把这个类的**所有信息**，包括静态方法的字节码指令，都加载到方法区中

静态变量随着类的加载而加载到**方法区**中，这个方法区存放一些公开的数据，包括成员方法，不管他是实例还是静态，都储存在方法区

然后一部分成员变量的信息也会储存在方法区，JDK8以后，引入了元空间，和方法区类似，但使用的是本地内存，解决了内存溢出等问题

-------

## 修饰符

### 访问控制修饰符

public（任何地方访问）
private（本类中访问）
~~protected（同一个包内访问，不同包的子类访问）~~
默认（同一个包内访问）

![image-20250301145518139](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012341331.png)	·

### 非访问控制修饰符

static 属于类本身，不属于实例
**final** 修饰类不能被继承，修饰方法不能被重写，修饰变量不能被修改，为**常量**（单个单词全部大写，多个单词全部大写用_隔开）

![image-20250301145359238](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342324.png)	

~~synchronized 修饰方法 保证同一时刻只有一个线程可以访问，实现线程同步~~
abstract 修饰类为抽象类，不能被实例化；修饰方法为抽象方法，没有方法体，必须在子类中实现

-----------

## 继承

## ![image-20250301122335078](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342004.png)	

**虚方法就是非private非static非final**

object类一共有5个虚方法

### 继承的内存图

![image-20250301122712262](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342233.png)	

创建子类对象的时候先加载父类后加载子类.class文件（包含成员变量和成员方法)到方法区

new的时候，**父类和子类的成员变量共用一块地址**

赋值的时候，z.name 先去子类找name，没有的话就去父类找name赋值

--------

### 子类重写父类虚方法

![image-20250301123706152](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342616.png)

重写后，子类中被重写的那个方法就会被覆盖

### 构造方法

父类的构造方法不会被继承

子类初始化之前，一定要调用父类构造方法先完成父类的数据空间的初始化**super();**

**由虚拟机自动调用**，因为子类可能会访问父类中的成员变量，所以会默认调用父类的空参构造初始化成员变量

---------



## 多态

![image-20250301125219791](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342346.png)	

父类引用指向子类对象

Fu f = new zi();

用多态方法创建对象的时：访问成员变量（编译运行都看左）访问成员方法（编译看左，运行在右）

就是访问成员变量，如果父类有这个成员变量，编译成功，反之编译失败，运行的时候显示的也是**父类的成员变量**

当访问成员方法的时候，如果父类没有这个成员方法，编译失败，运行的是**子类的成员方法**（因为重写了子类的方法，会把继承下来的父类方法覆盖了，只有子类方法）

所以多态的弊端就是不能调用子类的**特有方法**（必须父类中有才能编译成功）

多态的优势：方法中，使用父类型作为参数，可以接收所有子类对象

-----------



## 包

包就是文件夹，在使用同一个包中的类的时候和使用java.lang包下的类的时候不需要导包

其他情况都需要导包，如果使用两个包中的同名类的时候，需要用全类名

--------

## 静态代码块

static{}

随着类的加载而加载，只执行一次

使用场景：类加载过程中，对一些数据进行初始化的

--------------

## 抽象

abstract

![image-20250301151733613](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342180.png)	

抽象类不能创建对象,但**可以有构造方法**(创建子类对象的时候给子类赋值)

因为抽象类可以被子类继承，当创建子类对象的时候，虚拟机会自动调用super();这个代码来初始化父类对象，所以抽象类可以有构造方法

当没写构造方法时，虚拟机会自动创建一个空参构造

![image-20250301151759091](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342608.png)	

------

## 接口

interface

接口就是一种规则,是对**行为**的抽象,不能被创建对象
而父类是一种类型

![image-20250301152219139](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342155.png)	

![image-20250301152311531](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012342507.png)	

![image-20250301152336239](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343808.png)	

![image-20250301153332163](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343302.png)	

### 接口的多态

![image-20250301153704906](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343177.png)	

和继承的多态类似

属于一个类，可以出现在方法的参数中

可以传递这个类的所有子类，对于接口来说就是可以传递这个接口的所有实现类

--------



## 内部类

![image-20250301153841648](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343313.png)	

发动机就是内部类，也有品牌，年龄等属性

内部类单独出现没有意义

内部类可以直接访问外部类，包括私有

外部类访问内部类需要创建内部类对象

------------

### 匿名内部类

当我们只需要用到一个抽象类或者接口中的某一个方法的时候，必须要先创建一个子类对象或者实现类对象，然后重写方法去调用

很麻烦，需要创建对象，那我们就可以用匿名内部类来调用方法

![image-20250301154626531](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343733.png)	

这个整体属于一个**对象**，可以直接调用重写的方法，或者被当作参数传递

例如：

![image-20250301154748784](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343526.png)	

![image-20250301154804557](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343346.png)	

使用匿名内部类

1.当作参数传递

![image-20250301154832455](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343987.png)	

2.直接调用重写方法

![image-20250301154851486](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503012343601.png)	
