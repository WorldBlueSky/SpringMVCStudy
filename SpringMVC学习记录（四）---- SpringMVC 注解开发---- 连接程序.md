# SpringMVC 注解开发---- 连接程序



## 1、标识后端控制器

@Controller

相当于替代了 HandlerMapping 和 HandlerAdapter 的作用，同时将这个类 托管到 Spring 的容器中管理 



## 2、实现客户与程序的互联互通



### （1）@RequestMapper



@RequestMapping 是 Spring Web应用程序中最常用到的注解之一，是用来注册接口的路由映射的。



既能修饰一个类也能修饰一个方法。

如果在类和方法上面都加上@ReestMapper,那么访问的时候就有多级目录了



### （2）@ResponseBody 



与 @RequestMapping 搭配使用，可以返回一个非页面的数据，比如说返回一个字符串，JSON格式的数据等等。



在默认情况下 @RequestMapping 返回的是页面



### （3）不同的访问目录



方法上面加上映射@RequestMapping,访问时一级目录访问

```java
package com.bit.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController{

   @RequestMapping("/hi")
   @ResponseBody
    public String hello(){
       return "Hello SpringMVC";
   }

}
```

![1656141431219](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656141431219.png)



类和方法上都加上 @RequestMapping,两级目录访问接口

```java
package com.bit.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/aaaa")
public class HelloController{

   @RequestMapping("/hi")
    @ResponseBody
  public String hello(){
       return "Hello SpringMVC";
   }

}
```

![1656141471666](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656141471666.png)



### 总结



1、@RequestMapping 的作用就是用来注册一个 url 路由，通过这个路由，前端的请求可以实现和后端程序的交互了。而@RequestMapping  这个注解既能修饰方法，又能修饰类。

2、默认情况下使用@RequestMapping 会返回一个 html页面，当加上ResponseBody 注解之后就能返回非页面的数据了。

3、@ResponseBody 也能修饰方法和类，当修饰类的时候，表示此类中的所有方法都会返回一个非页面的数据。（前后端分离，后端只返回数据）



### 解决控制器返回中文数据乱码的情况



###  错误的方案



 在Controller中修改响应头Content-Type的值为"text/html;charset=utf-8"

![1656145151707](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656145151707.png)

 修改之后，乱码问题还是没有解决 



### 注解加属性解决



 在Controller中的@RequestMapping注解添加produces = “text/html;charset=utf-8”。代码如下所示

```java
@RequestMapping(value="/hello",produces="text/html;charset=utf8")
```

produces属性能够指定返回数据的类型， 还可以指定返回值的编码 ，能够成功解决响应中文相应乱码问题



**@ReuqestMapping 各属性作用详情可以看下面这篇博客**

 [(2https://blog.csdn.net/lzb348110175/article/details/88552507条消息) 2.@RequestMapping各个属性的使用_扛麻袋的少年的博客-CSDN博客_requestmapping属性](https://blog.csdn.net/lzb348110175/article/details/88552507) 





### （4）@RequestMapping 接收的是GET 还是 POST ？



我们写一个具体的控制器，没有指定传递的方法是什么，传递name参数，使用postman发送GET请求

```java
package com.bit.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@ResponseBody
public class HelloController{

    @RequestMapping(value = "/hi",produces = "text/html;charset=utf8")
    public String hello(String name){
        return "你好 "+name;
    }


}
```



发送GET请求之后的结果，成功接受了GET请求

![1656145660376](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656145660376.png)

发送POST请求之后的结果，也能成功接收

![1656145786080](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656145786080.png)

在没有指定方法的情况下，@RequestMapping 任意类型的方法都能接收。

同时，@Request Mapping 通过Method属性可以指定接收对应的方法，如果指定了GET，发送了POST，那么就会报405 方法不接受异常。



```java
   @RequestMapping(value = "/hi",produces = "text/html;charset=utf8",method = RequestMethod.GET)
    public String hello(String name){
        return "你好 "+name;
    }
```



通过postman发送 post请求，结果显示405报错，方法不允许

![1656146078971](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656146078971.png)



 **@RequestMapping的请求方式**

（1）如果方法上的@RequestMapping注解没有设置method属性，则get和post请求默认都可以访问。任何请求都能访问接收。

（2）如果方法上的@RequestMapping注解设置了method属性，则只能是相应的请求方式可以访问。 



### （5）@RequestMapping 指定方法的方式



#### 1、通过 method 属性设置



在@RequestMapping 注解的属性中 存在 method可以进行 设置指定接收的方法



![1656146457710](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656146457710.png)



#### 2、通过@GetMapping等注解达到同样的效果



spring MVC 中还可以通过 **@GetMapping、 @PostMapping**  等一系列注解达到相同的效果



```java
    @GetMapping(value = "/hi",produces = "text/html;charset=utf8")
    public String hello(String name){
        return "你好 "+name;
    }
```



发送post请求之后访问失败 

![1656146567416](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656146567416.png)





####  3、@RequestMapping 和 @GetMapping 的区别



- @RequestMapping 支持任意一种类型请求（GET、POST、PUT）

- @GetMapping 只支持get方式的请求

- @ReuqestMapping  既支持修饰 **方法** ，也支持修饰 **类** ，@GetMapping 只支持修饰 **方法** 。

