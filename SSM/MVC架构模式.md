# MVC架构模式

M（Model：数据业务处理）
V（View 展示层JSP）
C（Controller控制M和V）接收用户请求，接收数据，调度M处理业务，调度V展示数据

-----

## 三层架构

Model中包含Service**业务逻辑层**和Dao**持久化层**
Model将处理结果返回给司令官Controller
司令官调用视图组件进行展示View（**表示层**）

![image-20250312054139566](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160045549.png)	

**事务**一定是在业务逻辑层Service中控制的，一般是一个业务方法对应一个完整的事务

------

## 一个项目的整体逻辑

### 操作数据库类DAO，只负责CRUD，没有业务逻辑

![image-20250312052558586](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046703.png)	

![image-20250312052603944](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046197.png)	

### 账户信息JavaBean类(面向对象思想,给DAO传递账户信息的对象就行)

![image-20250312052642067](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046477.png)	

### 业务类Service，纯业务代码

![image-20250312052737698](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046844.png)	<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046094.png" alt="image-20250312052748526" style="zoom: 50%;" />	

### Servlet司令官

JSP提交表单
![image-20250312052909613](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046302.png)	

servlet中的doPost或者doGet方法调度执行
![image-20250312052945444](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046530.png)	

重定向jsp展示处理结果
![image-20250312053031866](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046944.png)	

展示数据页面（JSP）
![image-20250312053001870](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046730.png)	

![image-20250312053007034](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046864.png)	

-----------

## 通过接口来做到类与类之间的联系

![image-20250312053702429](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160046985.png)	

### Dao和Service的接口,写好对应方法

![image-20250312053747289](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160047214.png)	

![image-20250312053750341](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160047720.png)	

### Dao实现类

![image-20250312053759013](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160047607.png)	

### Service实现类，需要通过接口的多态来new一个Dao接口的实现类

![image-20250312053839502](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160047482.png)	

### Servlet司令官不需要接口，直接通过接口的多态来new一个Service的实现类

![image-20250312053929317](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503160047020.png)	
