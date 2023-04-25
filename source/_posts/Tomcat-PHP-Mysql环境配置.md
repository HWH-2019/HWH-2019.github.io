---
title: Tomcat+PHP+Mysql环境配置
author: HWH
tags:
  - Tomcat
  - PHP
  - Mysql
categories: Tutorial
abbrlink: ef69a6fd
date: 2022-11-18 20:15:51
---



## 版本
- Tomcat 10.0.6
- PHP 8.0.6
- Mysql 8.0.21

## Tomcat 安装配置

- 下载tomcat 从tomcat官网[下载zip包](https://tomcat.apache.org/download-10.cgi)
- 解压到自己想要的目录下
- 使用Tomcat之前需要JDK以及JAVA_HOME环境变量配置
- 修改`conf`文件下的`logging.properties`文件,[解决控制台乱码问题](https://blog.csdn.net/weixin_44580492/article/details/106358111)
- 配置tomcat环境变量，将`bin`目录路径添加到`path`下
- `cmd`下执行startup启动服务
- 浏览器输入`localhost:8080`,成功启动服务效果如下图:

![tomcat初始界面](https://img-blog.csdnimg.cn/20210516194605404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)


## Tomcat 配置 PHP

- [官网下载php](https://windows.php.net/download#php-7.0)
- 将php解压到自己想要的目录下
- 复制一份`php.ini-development`,并改名为`php.ini`
- 修改`php.ini`文件
	- 搜索定位到`extension_dir= "ext"`,去掉此行前的`;`,也可重写并指定`ext`文件夹的路径
	- 搜索定位到`cgi.force_redirect`,改为`cgi.force_redirect=0`
- 回到`tomcat`所在目录下，打开`conf/context.xml`文件
- 将`<context>`标签加上`privileged = "true"`属性,即修改为`<Context privileged="true">`
- 打开`web.xml`文件,在添加`<web-app>`中添加如下代码：

```xml
<!-- ================== Built In Servlet Definitions ==================== -->
<servlet>
    <servlet-name>php</servlet-name>
    <servlet-class>org.apache.catalina.servlets.CGIServlet</servlet-class>
    <init-param>
      <param-name>clientInputTimeout</param-name>
      <param-value>200</param-value>
    </init-param>
    <init-param>
      <param-name>debug</param-name>
      <param-value>0</param-value>
    </init-param>
    <init-param>
      <param-name>executable</param-name>
      <!-- 此处为php-cgi.exe的路径，需要根据自己的文件路径进行修改 -->
      <param-value>D:\tomcat\php\php-cgi.exe</param-value>
    </init-param>
    <init-param>
        <param-name>passShellEnvironment</param-name>
    <param-value>true</param-value>
</init-param>
    <init-param>
      <param-name>cgiPathPrefix</param-name>
      <!-- 此处与后文对应 -->
      <param-value>WEB-INF/phpbin</param-value>
    </init-param>
     <load-on-startup>5</load-on-startup>
</servlet>
```

- 注意修改`php-cgi.exe`的路径，路径为自己安装`php`的路径

```xml
<!-- ================ Built In Servlet Mappings ========================= -->
<servlet-mapping>
    <servlet-name>php</servlet-name>
    <url-pattern>/phpbin/*</url-pattern>
</servlet-mapping>
```

- 重启服务
- 由于上面在`web.xml`中添加了`<param-value>WEB-INF/phpbin</param-value>`,因此在`WEB-INF`下新建`phpbin`文件夹,并新建`index.php`作为测试。
- `index.php`的内容为:

```php
<?php
phpinfo();
?>
```

- 确保服务已启动,访问 [http://localhost:8080/phpbin/index.php](http://localhost:8080/phpbin/index.php) 查看效果，正确效果如下：

![php验证](https://img-blog.csdnimg.cn/20210516194717224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2gyNzYzMjQ2ODIz,size_16,color_FFFFFF,t_70#pic_center)


## 配置 Tomcat 利用 jsp 连接 Mysql

- 将mysql的jdbc驱动程序放到tomcat的lib中,如:[mysql-connector-java-8.0.21.jar](https://download.csdn.net/download/h2763246823/18777147)
- 在`ROOT`目录下新建`connect_mysql.jsp`文件,添加如下内容:

```js
<%@ page language="java" import="java.util.*" pageEncoding="gbk"%>
<%@ page import="java.sql.*" %>
<html>
  <head>
    <title>My JSP starting page</title>
  </head>

  <body>
    <span>
     <%
       try {
			//加载驱动
			Class.forName("com.mysql.jdbc.Driver");
			//URL指向要访问的数据库名
            String url = "jdbc:mysql://127.0.0.1:3306/enwords?serverTimezone=UTC";
            //用户名
            String user="***";
            //密码
            String password="***";
			//建立连接
			Connection con=DriverManager.getConnection(url,user,password);
		    //创建状态
			Statement state=con.createStatement();
            //3.要执行的sql语句,需要读者自行修改
            String sql="select * from enwords limit 100,120;";
            //4.ResultSet类，用来存放获取的结果集！！
			ResultSet rs=state.executeQuery(sql);

			String word=null;
            String translation=null;
            String aString="";
            //读者请根据自己的数据库表修改打印内容。
			while(rs.next()){
			    word=rs.getString("word");
                translation=rs.getString("translation");
				aString+=word+"\t"+translation+"<br>"
					+"----------------------------<br>";
			}
			out.println(aString);
			state.close();
			con.close();

		} catch (Exception e) {
			e.printStackTrace();
		}
     %>
    </span>
  </body>
</html>
```
- 根据自己`mysql`情况修改用户名，密码，需要执行的sql语句。
- 不熟悉`JSP`语句的可以去[菜鸟教程](https://www.runoob.com/jsp/jsp-database-access.html)学习。
- 确保tomcat服务已启动,访问 [http://localhost:8080/connect_mysql.jsp](http://localhost:8080/connect_mysql.jsp) 查看效果。

## 配置 Tomcat 利用 PHP 连接 Mysql

- 为php添加mysql扩展,在`php.ini`中搜索定位`extension`,将下面两行前的`;`去掉

```ini
extension=mysqli
extension=pdo_mysql
```
- 在`WEB_INF/phpbin`下新建`connect_mysql.php`文件,并写入以下内容

```php
<?php
$servername = "localhost";
$username = "***";	//用户名
$password = "***";	//密码
$dbname = "***";	//数据库名

// 创建连接
$conn = new mysqli($servername, $username, $password, $dbname);
// 检测连接
if ($conn->connect_error) {
    die("连接失败: " . $conn->connect_error);
}
//sql语句,读者根据自己的数据库和表修改语句。
$sql = "select * from enwords limit 0,100";

$result = $conn->query($sql);

echo "-------------------------------<br>";
if ($result->num_rows > 0) {
    // 输出数据,请读者根据自己的表和打印要求进行修改。
    echo "<strong>word&nbsp&nbsp&nbsp&nbsp&nbsptranslation</strong><br>";
    while($row = $result->fetch_assoc()) {
        echo $row["word"]. "&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp" .
		$row["translation"] . "<br>";
		echo "-------------------------------<br>";
    }
} else {
    echo "0 结果";
}
$conn->close();
?>
```
- 根据自己的情况填入对应的用户名，密码，数据库名和sql语句。
- 不熟悉`php`语句的可以去[菜鸟教程](https://www.runoob.com/php/php-mysql-intro.html)学习。
- 确保tomcat服务已启动，访问[http://localhost:8080/phpbin/connect_mysql.php](http://localhost:8080/phpbin/connect_mysql.php) 查看效果。

## 可能遇到的问题解决
- 如果`php`连接`mysql`出现`The server requested authentication method unknown to the client`报错
- `cmd`下登录数据库，执行如下语句修改密码。

```sql

mysql -uroot -p

use mysql;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
```
