# SpringMVC

## 拦截器

场景：淘宝购物车页面必须登录后才能访问，如果没登录则跳转登录页面，如果登录了就访问购物车

所以我们需要在每一个Handler方法前都加一个判断登录的逻辑吗？这时候就引入了拦截器，可以在多个Handler方法**执行之前**或者**之后**都进行一些处理，甚至某些情况下需要拦截掉，不让Handler方法执行。那么可以使用SpringMVC为我们提供的拦截器。

==内部就是AOP==

--------

### 拦截器和过滤器的区别

过滤器是在Servlet执行之前或者之后进行处理。而拦截器是对**Handler**（处理器）执行前后进行处理。

![image-20250319015739447](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503190157510.png)	

### 创建拦截器

创建类实现HandlerInterceptor接口，重写方法

```java
public class MyInterceptor implements HandlerInterceptor {
    
    //在handler方法执行之前会被调用
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
         //返回值代表是否放行，如果为true则放行，如果为fasle则拦截，目标方法执行不到
        return true;
    }
    
    //在handler方法执行之后会被调用，可以操作ModelAndView对象
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    //也是handler方法执行之后调用，不常用，通常用来关闭连接
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

- preHandle方法会在Handler方法执行之前执行，我们可以在其中进行一些前置的**判断或者处理**。
- postHandle方法会在Handler方法执行之后执行，我们可以在其中**对域中的数据进行修改**，也可以修改要跳转的页面。
- afterCompletion方法会在最后执行，这个时候已经没有办法对域中的数据进行修改，也没有办法修改要跳转的页面。我们在这个方法中一般进行一些**资源的释放**。

方法中的参数

**request** 当前请求对象
**response** 响应对象
**handler** 相当于是真正能够处理请求的handler方法封装成的对象，对象中有这方法的相关信息
**modelAndView**   handler方法执行后的modelAndView对象，我们可以修改其中要跳转的路径或者是域中的数据
**ex** 异常对象



配置拦截器

```xml
    <!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--
                    配置拦截器要拦截的路径
                    /*    代表当前一级路径，不包含子路径
                    /**   代表当前一级路径和多级路径，使用的更多

                    例如：
                        /test/*   这种会拦截下面这种路径/test/add  /test/delete
                                  但是拦截不了多级路径的情况例如  /test/add/abc  /test/add/abc/bcd
                        /test/**  这种可以拦截多级目录的情况，无论    /test/add还是/test/add/abc/bcd 都可以拦截
            -->
            <mvc:mapping path="/**"/>
            <!--配置排除拦截的路径-->
            <mvc:exclude-mapping path="/"/>
            <!--配置拦截器对象注入容器-->
            <bean class="com.sangeng.interceptor.MyInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

--------

### 案例：登录状态拦截器

我们的接口需要做用户登录状态的校验，如果用户没有登录则跳转到登录页面，登录的情况下则可以正常访问我们的接口。

#### 分析

- 怎么判断是否登录？


​	登录时往session写入用户相关信息，然后在其他请求中从session中获取这些信息，如果获取不到说明不是登录状态。

- 登录接口是否应该进行拦截？


​	不能拦截

- 静态资源是否要进行拦截？


​	不能拦截

#### 代码实现

登录页面html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form method="post" action="/login">
  用户名：<input type="text" name="username">
  密码：<input type="password" name="password">
  <input type="submit">
</form>
</body>
</html>
```

登录接口

接口中，校验用户名密码是否正确（模拟校验即可，先不查询数据库）。如果用户名密码正确，登录成功。把用户名写入session中。

```java
@Controller
public class LoginController {
    @PostMapping("/login")
    public String login(String username, String password, HttpSession session) {
        System.out.println(username);
        System.out.println(password);
        //在session中写入用户数据
        session.setAttribute("username", username);
        return "/WEB-INF/page/success.jsp";
    }
}
```

定义拦截器

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        //用request来获取session对象
        String username = (String)session.getAttribute("username");
        //获取session对象中的数据
        //StringUtils工具类的判断
        if(StringUtils.isEmpty(username)) {
            String contextPath = request.getServletContext().getContextPath();
            //获取项目的根路径
            response.sendRedirect(contextPath+"/static/login.html");
            //重定向
        }
        //不为空，放行
        else{
            return true;
        }
        return false;
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

配置拦截器

登录接口和静态资源不应该拦截

```xml
    <mvc:interceptors>
        <mvc:interceptor>
            <!--要拦截的路径-->
            <mvc:mapping path="/**"/>
            <!--排除不拦截的路径-->
            <mvc:exclude-mapping path="/static/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/WEB-INF/page/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/login"></mvc:exclude-mapping>
            <bean class="com.sangeng.interceptor.LoginInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

-------

### 多拦截器执行顺序

如果我们配置了多个拦截器，拦截器的顺序是**按照配置的先后顺序**的。

#### preHandler都返回true的情况下

![image-20250319021342231](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503190213309.png)	

#### 如果拦截器3的preHandle方法返回值为false

![image-20250319021418534](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202503190214585.png)	

只有所有拦截器都放行了，postHandle方法才会被执行。

只有当前拦截器放行了，当前拦截器的afterCompletion方法才会执行。

-----

## 统一异常处理

我们在实际项目中Dao层和Service层的异常都会被抛到Controller层。但是如果我们在Controller的方法中都加上异常的try...catch处理也会显的非常的繁琐。

所以SpringMVC为我们提供了统一异常处理方案。可以把Controller层的异常进行统一处理。这样既提高了代码的复用性也让异常处理代码和我们的业务代码解耦。

一种是实现HandlerExceptionResolver接口的方式，一种是使用@ControllerAdvice注解的方式。

-----

### HandlerExceptionResolver接口（不常用）

```java
@Component
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
    //如果handler中出现了异常，就会调用到该方法，我们可以在本方法中进行统一的异常处理。
     @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        //获取异常信息，把异常信息放入域对象中
        String msg = ex.getMessage();
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("msg",msg);
        //跳转到error.jsp
        modelAndView.setViewName("/WEB-INF/page/error.jsp");
        return modelAndView;
    }
}
```

--------

### @ControllerAdvice（重要）

创建类加上**@ControllerAdvice**注解进行标识，自定义异常处理方法使用**@ExceptionHandler**标识可以处理的异常。

```java
@Component
@ControllerAdvice
public class MyControllerAdvice {

    @ExceptionHandler({NullPointerException.class,ArithmeticException.class})
    public ModelAndView handlerException(Exception ex){
        //如果出现了相关的异常，就会调用该方法
        String msg = ex.getMessage();
        ModelAndView modelAndView = new ModelAndView();
        //把异常信息存入域中
        modelAndView.addObject("msg",msg);
        //跳转到error.jsp
        modelAndView.setViewName("/WEB-INF/page/error.jsp");
        return modelAndView;
    }
}
```

我们在实际项目中一般会选择使用@ControllerAdvice 来进行异常的统一处理。**因为不是实现接口，可以自定义方法的返回类型**

在前后端不分离的项目中，异常处理一般是跳转到错误页面，让用户有个更好的体验。
而前后端分离的项目中，异常处理一般是把异常信息封装到Json中写入响应体。

例如下面这种方式就是前后端分离的异常处理方案，把异常信息封装到对象中，转换成json写入响应体。

```java
@Component
@ControllerAdvice
public class MyControllerAdvice {
    @ExceptionHandler({NullPointerException.class,ArithmeticException.class})
    @ResponseBody//写入响应体
    public Result handlerException(Exception ex){
        Result result = new Result();
        result.setMsg(ex.getMessage());
        result.setCode(500);
        return result;
    }
}
```

-----

## 文件上传

Http协议规定了我们在进行文件上传时的请求格式要求。所以在进行文件上传时，除了在表单中增加一个用于**上传文件的表单项（input标签，type=file）**外必须要满足以下的条件才能进行上传。

**请求方式为POST请求**

如果是使用表单进行提交的话，可以把form标签的**method**属性设置为**POST**

请求头**Content-Type**必须为**multipart/form-data**

如果是使用表单的话可以把表单的**entype**属性设置成**multipart/form-data**

---------

### SpringMVC接收上传的文件

引入依赖

```xml
    <!--commons文件上传，如果需要文件上传功能，需要添加本依赖-->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
    </dependency>
```

配置

```xml
    <!--
          文件上传解析器
          注意：id 必须为 multipartResolver
      -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置默认字符编码 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 一次请求上传的文件的总大小的最大值，单位是字节-->
        <property name="maxUploadSize" value="#{1024*1024*100}"/>  <!--100mb-->
        <!-- 每个上传文件大小的最大值，单位是字节-->
        <property name="maxUploadSizePerFile" value="#{1024*1024*50}"/> <!--50mb-->
    </bean>
```

上传表单,注意method，enctype

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/upload" method="post" enctype="multipart/form-data">
        <input type="file" name="uploadFile">
        <input type="submit">
    </form>
</body>
</html>
```

上传接口

```java
@Controller
public class UploadController {

    @RequestMapping("/upload")
    public String upload(MultipartFile uploadFile) throws IOException {
        uploadFile.transferTo(new File("test.txt"));//文件储存为test.txt到项目根目录下
        return "/WEB-INF/page/success.jsp";
    }
}
```

MultipartFile常见用法

- 获取上传文件的原名

  ~~~~java
  uploadFile.getOriginalFilename()
  ~~~~

- 获取文件类型的MIME类型

  ~~~~java
  uploadFile.getContentType()
  ~~~~

- 获取上传文件的大小

  ~~~~java
  uploadFile.getSize()
  ~~~~

- 获取对应上传文件的输入流

  ~~~~java
  uploadFile.getInputStream()
  ~~~~


-------

## 文件下载

**文件下载要求**

①设置响应头Content-Type

​	要求把提供下载文件的MIME类型作为响应头Content-Type的值

②设置响应头Content-disposition

​	要求把文件名经过URL编码后的值写入响应头Content-disposition。但是要求符合以下格式，因为这样可以解决不同浏览器中文文件名 乱码问题。

```java
Content-disposition: attachment; filename=%E4%B8%8B%E6%B5%B7%E5%81%9Aup%E4%B8%BB%E9%82%A3%E4%BA%9B%E5%B9%B4.txt;filename*=utf-8''%E4%B8%8B%E6%B5%B7%E5%81%9Aup%E4%B8%BB%E9%82%A3%E4%BA%9B%E5%B9%B4.txt
```

③文件数据写入响应体中

--------

**自定义封装一个下载工具类**

```java
public class DownLoadUtils {
    /**
     * 该方法可以快速实现设置两个下载需要的响应头和把文件数据写入响应体
     * @param filePath 要下载文件的相对路径
     * @param context  ServletContext对象
     * @param response  响应体，文件要写入响应体中
     * @throws Exception
     */
    public static void downloadFile(String filePath, ServletContext context, HttpServletResponse response) throws Exception {
        String realPath = context.getRealPath(filePath);
        File file = new File(realPath);
        String filename = file.getName();
        FileInputStream fis = new FileInputStream(realPath);
        String mimeType = context.getMimeType(filename);//获取文件的mime类型
        response.setHeader("content-type",mimeType);//设置响应头
        String fname= URLEncoder.encode(filename,"UTF-8");//对文件名进行URL编码，解决乱码问题
        response.setHeader("Content-disposition","attachment; filename="+fname+";"+"filename*=utf-8''"+fname);
        ServletOutputStream sos = response.getOutputStream();//拿到往响应体中写数据的流
        byte[] buff = new byte[1024 * 8];
        int len = 0;
        while((len = fis.read(buff)) != -1){
            sos.write(buff,0,len);
        }
        sos.close();
        fis.close();
    }
}
```

**下载接口**

```java
@Controller
public class DownloadController {
    
    @RequestMapping("/download")
    public void download(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //调用工具类下载abc.txt文件
        DownLoadUtils.downloadFile("/WEB-INF/File/abc.txt",request.getServletContext(),response);
    }
}
```

文件下载要把文件写到响应体中
写完之后session自动执行committed

如果下载完后跳转到jsp页面，jsp页面就是一个servlet，其中有各种标签，也要写入到响应体中
但是响应体已经提交了，就写不进去了

所以文件下载的时候不需要跳转页面

**超链接下载接口下载**

```html
<a href="/download">下载文件</a>
```

