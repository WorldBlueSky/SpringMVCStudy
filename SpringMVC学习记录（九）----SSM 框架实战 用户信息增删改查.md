# SSM 框架实战---用户信息增删改查



## 效果展示



![1656610210953](C:\Users\rain7\AppData\Roaming\Typora\typora-user-images\1656610210953.png)





## pojo层 



User 实体类



```java
package com.bit.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String username;
    private String password;
}

```



## mapper层  



UserMapper接口以及对应的xml文件



UserMapper接口

```java
package com.bit.mapper;

import com.bit.pojo.User;
import org.apache.ibatis.annotations.Param;

import java.util.HashMap;
import java.util.List;

public interface UserMapper {

    int insert(User user);

    int delete(@Param("id") int id);

    int update(HashMap<String,Object> map);

    List<User>  selectAll();

    User selectById(int id);

}
```



UserMapper.xml



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.bit.mapper.UserMapper">
    <insert id="insert" parameterType="user">
        insert into users values(#{id},#{username},#{password})
    </insert>

    <delete id="delete">
        delete from users where id=#{id}
    </delete>

    <update id="update">
        update users set username=#{username},password=#{password} where id=#{id}
    </update>

    <select id="selectAll" resultType="user" >
        select * from users;
    </select>

    <select id="selectById" resultType="user" >
        select * from users where id=#{id};
    </select>

</mapper>
```





## service层



UserService接口



```java
package com.bit.service;

import com.bit.pojo.User;

import java.util.List;

public interface UserService {

    int addUser(User user);

    int  delteteUser(int id);

    int  updateUser(int id,User user);

    List<User> selectAllUser();

    User selectOne(int id);

}

```



UserServiceImpl 实现类



```java
package com.bit.service;

import com.bit.mapper.UserMapper;
import com.bit.pojo.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.List;

@Service
public class UserServiceImpl implements UserService{

    @Autowired
    private UserMapper userMapper;

    public int addUser(User user) {
        return userMapper.insert(user);
    }

    public int delteteUser(int id) {
        return userMapper.delete(id);
    }

    public int updateUser(int id, User user) {

        HashMap<String,Object> map = new HashMap<String, Object>();

        map.put("id",id);
        map.put("username",user.getUsername());
        map.put("password",user.getPassword());

        return userMapper.update(map);
    }

    public List<User> selectAllUser() {
        return userMapper.selectAll();
    }

    public User selectOne(int id) {
        return userMapper.selectById(id);
    }

    public static void main(String[] args) {

    }
}

```



## controller层  



UserController 类



```java
package com.bit.controller;

import com.bit.pojo.User;
import com.bit.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.jws.WebParam;

@Controller
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping("/select")
    @ResponseBody
    public Object select(){
        return userService.selectAllUser();
    }

    @RequestMapping("/insert")
    public Object insert(String username,String password){
        System.out.println("进入到后端的insert接口中!");
        User user = new User();
        user.setUsername(username);
        user.setPassword(password);

        System.out.println(user);
        userService.addUser(user);

        return "redirect:/static/index.html";
    }

    @RequestMapping("/update")
    public Object update(Integer id, String username,String password){
        System.out.println("进入到后端的update接口中!");
        User user = new User();

        System.out.println(id);
            user.setUsername(username);
            user.setPassword(password);


        System.out.println(user);
        userService.updateUser(id,user);

        return "redirect:/static/index.html";
    }

    @RequestMapping("/delete")
    public Object delete(Integer id){
        System.out.println("进入到后端的delete接口中!");

        System.out.println(id);

        int ret = userService.delteteUser(id);
        if(ret==1){
            System.out.println("删除成功!");
        }else{
            System.out.println("删除失败!");
        }

        return "redirect:/static/index.html";
    }
}

```



## 前端主页



使用form表单实现增加、修改，使用Ajax、Dom API 实现查询和修改



```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>练习Spring MVC</title>
        <meta name="description" content="Charcoal is a free Bootstrap 4 UI kit build by @attacomsian at Wired Dots." />
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <!--Bootstrap 4-->
        <link rel="stylesheet" href="css/bootstrap.min.css">
    </head>
    <body>

        <nav class="navbar navbar-expand-md navbar-dark fixed-top sticky-navigation">
            <a class="navbar-brand font-weight-bold" href="#">Spring 练习</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#topMenu" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>

            <div class="collapse navbar-collapse" id="topMenu">
            </div>
        </nav>



        <!--components-->
        <section class="my-5 pt-5">
            <div class="container">

                <!-- Tables  -->
                <div class="row mb-5" id="tables">
                    <div class="col-sm-12">
                        <br class="mt-3 mb-5">
                            <h3>用户信息的增删改查</h3>

                        <br>
                        <br>


                        <form action="http://localhost:8080/springmvc/user/insert" >
                              用户名：<input type="text" name="username">
                              用户密码：<input type="password" name="password">
                             <input type="submit" value="增加用户">
                          </form>

                            <br>
                            <br>

                            <form action="http://localhost:8080/springmvc/user/update" >
                                用户id：<input type="number" name="id">
                                用户名：<input type="text" name="username">
                                用户密码：<input type="password" name="password">
                                <input type="submit" value="修改用户">
                            </form>

                        <br>
                        <br>


                            <table class="table">
                                <thead class="thead-dark">
                                    <tr>
                                        <th>用户ID</th>
                                        <th>用户名</th>
                                        <th>密码</th>
                                        <th>编辑</th>

                                    </tr>
                                </thead>
                                <tbody id="usertable">
                                  
<!--                                   <tr>-->
<!--                                        <td>1</td>-->
<!--                                        <td>两数之和</td>-->
<!--                                        <td>简单</td>-->
<!--                                        <td> <a href="" >删除</a> </td>-->
<!--                                    </tr>-->

                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </section>



        <script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/js/bootstrap.min.js"></script>
        <script src="js/app.js"></script>
        <script>
            // 在页面加载的时候，尝试从服务器获取题目列表，通过 ajax 的方式来进行获取

          function getUserMessages() {
              $.ajax({
                  url:'http://localhost:8080/springmvc/user/select',
                  method:"GET",
                  success:function (data) {
                          createUserMassages(data);
                  }
              })

          }

          // 使用DOM API 将得到的数据data 写入到页面里面
              function createUserMassages(data) {

              console.log("进入回调函数内部!")
                 let userTable = document.querySelector("#usertable");

                 for (let user of data){
                     let tr = document.createElement("tr");

                     let tdId = document.createElement("td");
                     tdId.innerHTML = user.id;

                     let tdName = document.createElement("td");
                     tdName.innerHTML = user.username;

                     let tdPassword = document.createElement("td");
                     tdPassword.innerHTML = user.password;

                    let  tdA = document.createElement("td");
                    let  a = document.createElement("a");
                    a.href = "http://localhost:8080/springmvc/user/delete?id="+user.id;
                    a.innerHTML="删除";
                    tdA.appendChild(a);


                     tr.appendChild(tdId);
                     tr.appendChild(tdName);
                     tr.appendChild(tdPassword);
                     tr.appendChild(tdA);

                     userTable.appendChild(tr);
                 }


              }

            getUserMessages();

        </script>
    </body>
</html>

```



