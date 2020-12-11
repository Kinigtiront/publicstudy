# Springboot+Vue实现前后端分离

## 前端核心代码

```html
<form action="user/login" method="get" id="userrrr">
  <p>First name: <input type="text" name="fname" v-model="user"/></p>
  <p>Last name: <input type="text" name="lname" v-model="password"/></p>
  <input type="submit" value="Submit" v-on:click="login"/>
</form>
<p>{{user}}</p><p>{{password}}</p>
<script>
	var app = new Vue({
        el:"userrrr",
        data:{
            user:user,
            password:password,
            userdata:{}
        },
        methods:{
            login:funtion()
            {
            	var that=this;
            	axios.post('user/login',
            	Qs.stringify({
					name:that.name,
            		password:that.password
        		}).then(response=>
        			that.userdata = response.data
        		)
            )
        	}
        }
    })
</script>
```



## 后端核心代码

```java
Controller类
@RestController
public class UserController{
    @Autowired
    private UserMapper userMapper;
    
    @RequestMapping("user/login")
    public User login(String name,String password){
        User user = userMapper.login(name,password);
        return user;
    }
}

Mybatis
<select id="login" resultType="User">
        select * from User(user,password) values(#{user},#{password})
</select>
```

## 流程

### 1、在form表单输入，点击提交

### 2、触发login事件

#### 通过axios的post方法把值传到Controller类，==使用Qs.stringify==解析，实现把值从前端传到后端的功能。

#### 不手动用Qs库的stringify方法作为参数，会自动调用axios源码中的stringify方法解析，出现无法传参的问题

### 3、唤醒接口useMapper

#### usemappper接收到参数后，调用login方法(因为select语句的id是login)，从数据库中获得该用户的数据，

#### return到前端

### 4、前端获得数据

#### login事件触发成功后，主任务空了，then任务(大概是微任务?)加入到主任务中，用返回的user(response.data)初始化userdata对象，实现了后端返回数据到前端的功能



## 注解

#### @RestController注解是把UserController类转化为一个bean

#### @Autowired  private UserMapper userMapper;  把usermapper注入到controller类转化的bean中，可以理解为controller的一个可以调用方法的属性

#### @RequestMapping 把数据映射到对应网页