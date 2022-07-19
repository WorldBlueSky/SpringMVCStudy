# Spring MVC 小练习





## 一、计算器



```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<form action="http://localhost:8080/a/calc" >
  数字1：<input type="number" name="num1" >
  数字2：<input type="number" name="num2" >
  
  <input type="submit" value="计算数字之和">

</form>

</body>
</html>
```



form表单 action 提交到 calc的接口下，进行处理

通过<input> 进行传参，name作为key值，后端根据name进行接收参数



```java
    @RequestMapping("/calc")
    @ResponseBody
    public String getResult(Integer num1,Integer num2){
        return "<h1>两数之和为："+(num1+num2)+"<h1>";
    }
```



最终效果



![1656410651013](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656410651013.png)

点击计算按钮，form表单将输入的值作为参数进行传参



![1656410663205](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656410663205.png)



## 二、前后端交互的登陆



&emsp;&emsp;这里参时不涉及到数据库的操作，只要输入的用户账号以及密码为 admin即可显示登陆成功，进入一个主页“hello word”



/static/login.html ， 显示登录页并发送ajax请求，传递输入的 username、password 



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>登录</title>
    <link rel="stylesheet" href="css/common.css">
    <link rel="stylesheet" href="css/login.css">
</head>
<body>
    <!-- 导航栏 -->
    <div class="nav">
        <!-- logo -->
        <img src="image/logo2.jpg" alt="">
        <!-- 标题 -->
        <span class="title">我的系统</span>
        <!-- 空白的占位符 -->
        <span class="spacer"></span>
        <!-- 右侧的几个链接 -->
        <a href="index.html">主页</a>
    </div> 

    <!-- 登录页面的页面容器, 为了和其他页面样式区分开, 使用不同的类名 -->
    <div class="login-container">
        <div class="login-dialog">
            <!-- 标题 -->
            <h3>登录</h3>
            <!-- 输入用户名 -->
            <div class="row">
                <span>用户名</span>
                <input type="text" id="username">
            </div>
            <!-- 输入密码 -->
            <div class="row">
                <span>密码</span>
                <input type="password" id="password">
            </div>
            <!-- 提交按钮 -->
            <div class="row submit-row">
                <button id="submit" onclick="myfunc()">提 交</button>
            </div>
        </div>
    </div>
</body>


<script>
    function myfunc(){
          //1.拿到输入的用户名和密码控件
            var username=jQuery("#username");
            var password=jQuery("#password");

         //2.进行非空校验
         if(username.val()===""){
             alert("请先输入用户名!");
             username.focus();  // 光标归位
             return;
         }

         if(password.val()===""){
             alert("请输入密码!");
             password.focus();
             return;
         }

        //3.发起ajax请求，与后端进行交互

        $.ajax({
                url : '/a/login',
                method : 'GET',
                data : {"username": username.val(), "password": password.val()},
                success : function (data) {

                    if (data == null) {
                        alert("访问接口失败!")
                    }

                    if (data.succ === -1) {
                        alert(data.msg);
                    }

                    if (data.succ === 1) {
                        alert("登陆成功!");
                        location.href = 'http://localhost:8080/a/index';
                    }

                }
            }
        )
    }

</script>

<script src="js/jquery.min.js"></script>

</html>
```



&emsp;&emsp;登陆成功显示的主页 index.html，只是作为一个展示页，要通过/index接口才能重定向到这个页面，重定向之前要获取session查看是否登陆



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>这是首页</title>
</head>
<body>

<h1> Hello Spring MVC!</h1>

</body>
</html>
```



&emsp;&emsp;/login 登陆处理的接口，返回 status (响应状态码)、succ（登陆状态码）、msg（响应的信息），同时对username、password进行校验以及匹配，如果匹配成功 设置session



```java
   @GetMapping("/login")
    @ResponseBody
    public Object getLogin(HttpServletRequest request,String username,String password){
        System.out.println("进入接口！");
        HttpSession session = request.getSession(true);

       String msg="";
       int status=200;  // 响应码 如果为200说明访问后端接口成功
       int succ=-1;  // 登陆状态码 ，如果为-1说明登陆失败

        HashMap<String ,Object> map = new HashMap<>();

       if(username!=null && password!=null && username.equals("admin") && password.equals("admin")){
           System.out.println("执行了用户名密码匹配的判断!");
           succ=1;
           msg="登陆成功!";

           User user = new User();
           user.setUsername(username);
           user.setPassword(password);
           session.setAttribute("user",user);

       }else{
           msg="用户名或者密码错误";
       }

       map.put("status",status);
       map.put("succ",succ);
       map.put("msg",msg);

       return map;
    }
```



&emsp;&emsp;如果登陆正常，前端接收到data.success==1，那么跳转到 /index 接口，判断session是否存在，如果存在那么重定向到 首页、如果不存在 重定向到 login 登录页



```java
 @RequestMapping("/index")
    public String  index(@SessionAttribute(value = "user",required=false) User user){
        if(user==null){
            // 说明未登录状态
            return "redirect:/static/login.html";
        }else{
            return "redirect:/static/index.html";
        }
    }
```





效果演示：



进入登陆界面

![1656434532549](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656434532549.png)



前端的非空提示



未输入用户名



![1656434596835](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656434596835.png)



未输入密码

![1656434620850](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656434620850.png)



用户名和密码都输入了，匹配是否成功

![1656434731666](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656434731666.png)



匹配成功，进入/index,经过session校验后进入 首页

​        ![1656434780506](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656434780506.png)

如果没有登陆，获取不到session，直接点击主页，是会重定向到 登陆界面的。

