##  Maven 创建 Servlet 项目的过程



### 一、创建maven项目，引入web-app模板，创建项目源代码目录



maven搭建项目，使用web-app模板



![1655701609820](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655701609820.png)



初始目录结构

![1655701685131](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655701685131.png)

添加项目的源文件的根目录java



![1655701775162](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655701775162.png)



### 二、根据Tomcat 版本添加对应版本的servlet依赖，引入Tomcat插件



![1655701968451](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655701968451.png)



加入smart Tomcat 插件，配置本地的tomcat到项目中



![1655702032383](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655702032383.png)



在pom.xml文件中加入 servlet依赖



```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>

```



## 三、配置servlet配置文件和路由文件



**1、在web.xml中定义servlet类以及映射路由,下面是模板**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="true">
    
     <servlet>
       <servlet-name>hello</servlet-name>
       <servlet-class>HelloServlet</servlet-class>
     </servlet>

  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>


</web-app>
```



**2、或者使用注解 @WebServlet("/hello") 定义映射路由**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="false">
</web-app>
```



## 四、写servlet类的代码



继承HttpServlet，重写doGet方法，确定返回的resp的类型、编码、状态码



```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取参数（参数校验）
        String name = req.getParameter("name");

        // 2.访问数据库
        // 伪代码不写了

        //3.将组装好的数据返回给前端
        resp.setContentType("text/html");
        resp.setStatus(200);
        resp.setCharacterEncoding("utf8");
        resp.getWriter().write(name);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req, resp);
    }
}

```



## 五、运行tomcat服务器，在url进行访问以及传参



![1655703723171](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1655703723171.png)
