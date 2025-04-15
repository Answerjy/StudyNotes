# SpringAI

## 什么是AI

![](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504150101250.png)

## Transformer神经网络

![image-20250415010958646](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504150109702.png)	

### 大模型工作方式

大模型是让Transformer进行推理预测，这段文字后面应该是什么（根据上文推理下面的文本）

一个字一个字生成，不断拼接后再通过transformer推理

------

## 大模型应用开发

### 模型部署

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504150116779.png" alt="image-20250415011602673" style="zoom: 50%;" />	

### 本地部署

https://ollama.com/

下载ollama

选择模型复制run命令

![image-20250415202538829](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153198.png)	

命令行运行,下载的话建议用pull

![image-20250415202620497](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153905.png)	

终止对话/bye

![image-20250415210446616](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153207.png)	

查看状态ollama ps

![image-20250415210512290](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153171.png)	

停止这个模型ollama stop deepseek-r1:1.5b

![image-20250415210600073](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153270.png)	

-------

#### 调用大模型API接口部署

python

![image-20250415012250301](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504150122353.png)	

## 大模型应用

传统能力和大模型能力相结合

![image-20250415014012060](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153109.png)	

大模型没有记忆，需要用传统的数据库存储回答，才能形成“记忆”

浏览器对话的是大模型应用，不是大模型

## AI应用开发技术架构

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153969.png" alt="image-20250415015046017" style="zoom:50%;" />	

![image-20250415015104364](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153635.png)	

-------

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153511.png" alt="image-20250415015115933" style="zoom:50%;" />	

![image-20250415015406890](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152153923.png)	

-----

<img src="C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250415015436711.png" alt="image-20250415015436711" style="zoom:50%;" />	

![image-20250415015558913](C:/Users/Answer/AppData/Roaming/Typora/typora-user-images/image-20250415015558913.png)	

-------

## SpringAI框架

![image-20250415015956891](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154889.png)	

------

## 请求方式进行对话

![image-20250415120258823](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154640.png)	

只有框选的是我们需要改变的东西，而其他基本是固定的，springAI就是帮助我们写好这些固定的代码的

---

### 快速入门

1.构建SpringBoot项目，需要JDK17以及springboot3.0以上

![image-20250415213644509](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154652.png)	

**注意：**

springboot勾选的lombok有bug，不生效

需要手动引入

![image-20250415211518946](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154474.png)	

pom.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.3</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>myai</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>myai</name>
    <description>myai</description>
    <url/>
    <licenses>
        <license/>
    </licenses>
    <developers>
        <developer/>
    </developers>
    <scm>
        <connection/>
        <developerConnection/>
        <tag/>
        <url/>
    </scm>
    <properties>
        <java.version>17</java.version>
        <spring-ai.version>1.0.0-M7</spring-ai.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-starter-model-ollama</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.22</version>
        </dependency>

        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.ai</groupId>
                <artifactId>spring-ai-bom</artifactId>
                <version>${spring-ai.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

2.配置yml文件

```yaml
spring:
  application:
    name: myai
  ai:
    ollama:
      base-url: http://localhost:11434
      chat:
        model: deepseek-r1:1.5b
```

其他模型，比如openAI（需要api-key）

<img src="https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154157.png" alt="image-20250415214107211" style="zoom:50%;" />	

3.配置config类构建客户端

```java
@Configuration
public class CommonConfiguration {

    @Bean
    public ChatClient chatClient(OllamaChatModel model) {
        return ChatClient
                .builder(model)
                .defaultSystem("你是邓超")//指定system
                .build();
    }
}
```

4.controller类中执行代码，返回给前端

```java
@RestController
@RequestMapping("/ai")
@RequiredArgsConstructor

public class ChatController {

    private final ChatClient chatClient;

    @RequestMapping(value = "/chat",produces = "text/html;charset=UTF-8")//流式输出需要加这个produces参数
    public Flux<String> chat(String prompt) {//阻塞输出这个里就是用String接收
        return chatClient.prompt()
                .user(prompt)
                .stream()//阻塞输出就是.call()
                .content();
    }
}
```

5.启动项目进行测试（命令行需要开启本地模型）

![image-20250415212016849](https://cdn.jsdelivr.net/gh/Answerjy/Cloud-image-Hosting/img/202504152154002.png)	



