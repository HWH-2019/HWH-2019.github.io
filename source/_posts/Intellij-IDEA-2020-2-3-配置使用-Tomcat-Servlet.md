---
title: Intellij IDEA 2020.2.3 配置使用 Tomcat+Servlet
author: HWH
tags:
  - Tomcat
  - Servlet
  - Intellij IDEA
categories: Tutorial
abbrlink: df095734
date: 2022-11-18 20:22:54
---


## 创建项目
- 添加新项目如下图：

![添加新项目](https://img-blog.csdnimg.cn/20210517162520150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 弹出的窗口中选择`Java Enterprise`，点击`Next`

![选择Java Enterprise](https://img-blog.csdnimg.cn/20210517162815563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 下一个界面中勾选`Servlet`，点击`Next`

![](https://img-blog.csdnimg.cn/20210517163111308.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 选择文件名和文件路径，点击`FInish`

![](https://img-blog.csdnimg.cn/20210517163554264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

## 配置 Tomcat 服务器

- 若为在`Intellij IDEA` 中添加过`Tomcat Server`，则在`Setting`中设置添加自己安装的`tomcat`服务器，`tomcat`服务器安装可以参考[Tomcat+PHP+Mysql环境配置](https://hwh-2019.github.io/2022/11/18/Tomcat-PHP-Mysql环境配置/)

![](https://img-blog.csdnimg.cn/20210517164931586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 点击`Run`中的`Edit Configuration`

![](https://img-blog.csdnimg.cn/20210517163832878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 点击添加`Tomcat Server`

![](https://img-blog.csdnimg.cn/20210517164059685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 下方会警告`Warning: No artifacts marked for deployment`，切换到Deployment下。
![](https://img-blog.csdnimg.cn/20210517164356390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 添加`build artifact`，点击`Ok`

![](https://img-blog.csdnimg.cn/20210517164540814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

## 初步使用
### 执行 jsp 文件

- 在`src/main/webapp`文件夹下新建文件`index.jsp`

![](https://img-blog.csdnimg.cn/20210517171554670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- `index.jsp`文件内容如下
```JSP
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>
<body>
<h1><%= "Hello World!" %>
</h1>
<br/>
<a href="hello-servlet">Hello Servlet</a>
</body>
</html>
```

- 点击运行启动服务，效果如下：

![](https://img-blog.csdnimg.cn/20210517171452430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

### 执行 Servlet
- 在`src/main/java`文件夹下新建包`servlet`，并在其中添加`HelloWorld.java`文件

![](https://img-blog.csdnimg.cn/20210517172058218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- `HelloWorld.java`文件内容如下：

```java
package servlet;

// 导入必需的 java 库

import java.io.*;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

// 扩展 HttpServlet 类
@WebServlet("/Servlet")
public class HelloWorld extends HttpServlet {

    private String message;

    public void init() throws ServletException
    {
        // 执行必需的初始化
        message = "Hello World";
    }

    public void doGet(HttpServletRequest request,
                      HttpServletResponse response)
            throws ServletException, IOException
    {
        // 设置响应内容类型
        response.setContentType("text/html");

        // 实际的逻辑是在这里
        PrintWriter out = response.getWriter();
        out.println("<h1>" + message + "</h1>");
    }

    public void destroy()
    {
        // 什么也不做
    }
}
```

- 打开`src/main/webapp/WEB_INF`下的`web.xml`文件，在`<web-app>`中添加如下内容：

```xml
    <servlet>
        <servlet-name>HelloWorld</servlet-name>
        <servlet-class>servlet.HelloWorld</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>HelloWorld</servlet-name>
        <url-pattern>/HelloWorld</url-pattern>
    </servlet-mapping>
```

- 点击运行启动服务，浏览器访问切换到`HelloWorld`目录下

```java
http://localhost:8080/demo1_war_exploded
//由上面的路径切换到下面的路径
http://localhost:8080/demo1_war_exploded/HelloWorld
```
- 成功的效果如下：

![](https://img-blog.csdnimg.cn/2021051717310279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

## Tomcat 版本问题
如果使用`tomcat 10`版本，则上面切换到`HelloWorld`目录后会报错。
解决方法有两个：
1. 更换tomcat版本，安装`tomcat 9`
2. 手动导入`tomcat 10`所带的`servlet-api.jar`

### 手动导入 servlet-api.jar

- 打开`File`下`Project Structure`中的`Modules`，切换到`Dependencies`

![](https://img-blog.csdnimg.cn/20210517174145999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 点击加号添加，选择自己`tomcat`路径`lib`下的`servlet-api.jar`

![](https://img-blog.csdnimg.cn/2021051717445148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

- 打开项目下的`pom.xml`文件，删除如下内容：

![](https://img-blog.csdnimg.cn/20210517174816294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70)

```xml
<dependency>
	<groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

- 点击重新运行启动服务，浏览器访问切换到`HelloWorld`目录下，即可正常访问。
