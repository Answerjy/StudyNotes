# SpringMVC

## 类型转换器

在Web开发中，尤其是使用Spring MVC时，用户通过表单或API传递的请求参数通常是**字符串类型**，但后端模型可能需要其他类型（如`Date`、`Integer`、`LocalDate`等）。此时，类转换器可以将字符串转换为目标类型。

### 内置类型转换器

Stirng——>Boolean类型转换的转换器
**StringTobooleanConverter转换器源码**

![image-20250318135121118](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181351190.png)	

-----

### 自定义类型转换器

**日期转换器**
前端表单提交date类型是2021-05-12这种**横杠分隔**的

![image-20250318135151990](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181351020.png)	

==内置转换器只能转换2021/05/12这种斜杠风格的==

如果我们传递了2021-05-12这种情况下我们就可以选择**自定义类型转换**

可以将横杠分隔字符串，或者/风格的字符串都转换成LocalDate的转换器

![image-20250318142728021](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181427048.png)	

```java
import org.springframework.core.convert.converter.Converter;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;

public class StringToDateConverter implements Converter<String, LocalDate> {
    @Override
    public LocalDate convert(String source) {
        if (source == null || source.isEmpty()) {
            return null;
        }
        try {
            // 尝试解析 yyyy-MM-dd 格式
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
            //定义一种规则用来解析yyyy-MM-dd这种格式的字符串
            LocalDate localDate = LocalDate.parse(source, formatter);
            //传递这个字符串和规则，返回一个LocalDate类型的时间类对象
            return localDate;
        } catch (DateTimeParseException e) {
            // 如果失败，尝试解析 yyyy/MM/dd 格式
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
            LocalDate localDate = LocalDate.parse(source, formatter);
            return localDate;
        }
    }
}
```

需要配置spring-mvc.xml

![image-20250318135407963](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181354997.png)	

测试网络接口(需要有@RequestParam注解)

```java
    @RequestMapping("/date")
    public String Date(@RequestParam("birthday") LocalDate birthday){
        System.out.println("Date");
        System.out.println(birthday);
        return "forward:/success.jsp";
    }
```

这样我们就可以成功接受并转换两种风格的日期字符串了

![image-20250318143020482](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181430519.png)	

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181430421.png)	

java控制台打印的依旧是-横杠风格的

![image-20250318143103308](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181431334.png)	

---------

### 注解转换

当我们不需要转换两种风格的时候，只需要转换一种-横杠风格的时候，就不需要自定义类型转换器以及xml
可以直接使用**@DateTimeFormat**注解,传递自定义类型的直接转换为日期

**弊端**：只能转换-横杠风格的日期字符串了，遇到/斜杠风格的日期字符串会404

![image-20250318143550485](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181435515.png)	

-------

## 数据放到响应体中转化成json

**@ResponseBody**

1.添加依赖

```xml
    <!-- jackson，帮助进行json转换-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
```

2.配置注解驱动

```xml
<!--    定义注解驱动-->
    <mvc:annotation-driven>
<!--     消息转换器-->
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="utf-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

![image-20250318144341254](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181443289.png)	
定义网络接口

![image-20250318144417173](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181444209.png)	

结果

![image-20250318144435408](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181444439.png)	

-----

查询所有用户

![image-20250318144502787](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181445819.png)	

简化

![image-20250318144512955](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181445990.png)	

---

## 页面跳转

### 转发和重定向

return 字符串跳转路径

![image-20250318144629228](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181446261.png)	

内部是**请求转发**

--------

请求转发是**服务器**内部的
最终浏览器地址是永远是**第一次请求的地址**

重定向是**浏览器**控制的,是发送了多次请求到最终地址
最终浏览器地址是**最后一次请求的地址**

**取数据用转发,其他全是重定向**

forward: 请求转发(默认)

![image-20250318144801877](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181448918.png)	

redirect:重定向

![image-20250318144807643](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181448677.png)	

--------

### 视图解析器

WEB-INF里面的文件是受保护的
jsp一般放到里面

![image-20250318145254153](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181452188.png)	

根据 Servlet 规范，服务器会阻止对WEB - INF目录下资源的直接 HTTP 访问请求。
当客户端发送请求试图访问WEB - INF中的文件时，服务器会返回一个404错误（即使文件实际上存在），而不是将文件内容返回给客户端。

这时候就需要用到视图解析器去访问里面的文件，**属于请求转发**

![image-20250318144958669](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181449704.png)	

本质就是让跳转路径简单
可以设置跳转路径的前缀和后缀,进行拼接

注入bean视图解析器对象

```xml
    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
        <!--要求拼接的前缀-->
        <property name="prefix" value="/WEB-INF/page/"></property>
        <!--要拼接的后缀-->
        <property name="suffix" value=".jsp"></property>
    </bean>
```

![image-20250318145102989](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181451029.png)	

实际上访问的是/WEB-INF/page/test.jsp

--------

当我们**不需要添加前后缀**的时候，只需要用forward或者redirect进行标识，这样就不会进行前后缀拼接了

![image-20250318145413305](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503181454347.png)	

-------------

## 获取原生对象(了解)

我们之前在web阶段我们经常要使用到request对象，response，session对象等。我们也可以通过SpringMVC获取到这些对象。（不过在MVC中我们很少获取这些对象，因为有更简便的方式，避免了我们使用这些原生对象相对繁琐的API。）

我们只需要在方法上添加对应类型的参数即可，但是注意数据类型不要写错了，SpringMVC会把我们需要的对象传给我们的形参。

```java
@Controller
public class RequestResponseController {
    @RequestMapping("/getReqAndRes")
    public String getReqAndRes(HttpServletRequest request, HttpServletResponse response, HttpSession session){
        System.out.println();
        return "test";
    }
}
```

### 获取请求头

在方法中定义一个参数，参数前加上**@RequestHeader**注解，知道要获取的请求头名即可获取对应请求头的值。

想要获取 device-type 这个请求头则可以按照如下方式定义方法。

```java
@Controller
public class RequestResponseController {


    @RequestMapping("/getHeader")
    public String getHeader(@RequestHeader(value = "device-type") String deviceType){
        System.out.println(deviceType);
        return "test";
    }
}
```

### 获取Cookie

在方法中定义一个参数，参数前加上**@CookieValue** 注解，知道要获取的cookie名即可获取对应cookie的值。

想要获取 JSESSIONID 的cookie值。则可以按照如下方式定义方法。

```java
@Controller
public class RequestResponseController {

    @RequestMapping("/getCookie")
    public String getCookie(@CookieValue("JSESSIONID") String sessionId){
        System.out.println(sessionId);
        return "test";
    }
}
```

## JSP开发模式（了解）

如果我们使用JSP进行开发，那我们就需要在域中存数据，然后跳转到对应的JSP页面中，在JSP页面中获取域中的数据然后进行相关处理。

使用如果是类似JSP的开发模式就会涉及到**往域中存数据**和**携带数据跳转页面**的操作。

所以我们来看下如果用SpringMVC进行相关操作。

-------

### 往Requet域存数据并跳转

**Model**

我们可以使用**Model**来往域中存数据。然后使用之前的方式实现页面跳转。

要求访问  **/testRequestScope** 这个路径时能往Request域中存name和title数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在Jsp中获取域中的数据。

```java
@Controller
public class JspController {
    @RequestMapping("/testRquestScope")
    public String testRquestScope(Model model){
        //往请求域存数据
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
```

------

**ModelAndView**

我们可以使用**ModelAndView**来往域中存数据和页面跳转。

要求访问  **/testRequestScope2**  这个路径时能往域中存name和title数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在Jsp中获取域中的数据。

```java
@Controller
public class JspController {
    @RequestMapping("/testRquestScope2")
    public ModelAndView testRquestScope2(ModelAndView modelAndView){
        //往域中添加数据
        modelAndView.addObject("name","三更");
        modelAndView.addObject("title","不知名Java教程UP主");
        //页面跳转
        modelAndView.setViewName("testScope");
        return modelAndView;
    }
}
```

==注意要把modelAndView对象作为方法的返回值返回==

------

### 从Request域中获取数据

我们可以使用**@RequestAttribute** 把他加在方法参数上，可以让SpringMVC帮我们从Request域中获取相关数据。

```java
@Controller
public class JspController {
    @RequestMapping("/testGetAttribute")
    public String testGetAttribute(@RequestAttribute("org.springframework.web.servlet.HandlerMapping.bestMatchingPattern")
                                               String value,HttpServletRequest request){
        System.out.println(value);
        return "testScope";
    }
}
```

----

### 往Session域存数据并跳转

我们可以使用**@SessionAttributes**注解来进行标识，用里面的属性来标识哪些数据要存入Session域。

要求访问  **/testSessionScope **这个路径时能往域中存**name**和**title**数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在jsp中获取**Session域**中的数据。

```java
@Controller
@SessionAttributes({"name"})//表示name这个数据也要存储一份到session域中
public class JspController {
    @RequestMapping("/testSessionScope")
    public String testSessionScope(Model model){
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
```

------

### 获取Session域中数据

我们可以使用**@SessionAttribute**把他加在方法参数上，可以让SpringMVC帮我们从**Session域**中获取相关数据。

```java
@Controller
@SessionAttributes({"name"})
public class JspController {
    @RequestMapping("/testGetSessionAttr")
    public String testGetSessionAttr(@SessionAttribute("name") String name){
        System.out.println(name);
        return "testScope";
    }
}
```

