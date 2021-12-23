---
creation date: 2021-11-27 12:20:48
last modified: 2021-12-23 04:36:54
title: SpringMVC
categories:
- back-end
- spring
tags:
- java
- spring-mvc
---

Spring MVC 是基于 Spring 的，是其中的一个模块，其底层依赖 Servlet 实现。Spring MVC 的核心类是 DispatcherServlet，它的父类是 HttpServlet，也叫做前端控制器。

# 环境配置

## 打包方式修改

在 pom 文件中设置打包方式为 war 包

```xml
<packaging>war</packaging>
```

## 添加依赖

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

## 添加 web.xml

在 main 下创建 `webapp` 目录，然后在 “Project Structure -> Modules -> current project -> 选择 Web 模块 -> Deployment Descriptors -> 添加 web.xml (4.0)

注意：路径应该修改成 `.\src\main\webapp\WEB-INF\web.xml`

## 配置 Spring MVC Controller

在 web.xml 中添加注册 servlet

```xml
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--扩展：配置SpringMVC配置文件的位置和名称-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!-- 避免第一次访问时间太久，要求只要服务器启动就初始化 Controller -->
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <!-- / 匹配请求可以 /XXX 或 .html 或 .js 或 .css 不能匹配 .jsp -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

## 配置 SpringMVC.xml

(也就是配置 Spring 配置文件)

```xml
<context:component-scan base-package="com.demo.controller"/>

<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <!--配置优先级-->
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
            <property name="templateResolver">
                <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8"/>
                </bean>
            </property>
        </bean>
    </property>
</bean>
```

# @RequestMapping 注解

## 作用位置

该注解可以使用在 Class 上和 Method 上

作用在 Class 上的时候，该 Controller 会有自己的一个 base path，通常一个大的模块对应一个大的 path，比如 user，order，profile 之类的；

作用在 Method 上的时候，如果 Class 上有该注解，那么 Method 上该注解匹配的请求路径就是 base path 下的一个子路径。比如 Class 上作用了一个 `@RequestMapping("/order")` 然后有一个方法上面作用的是 `@RequestMapping("/status")` 那么想请求 status 页面的话，完整路径将会是 `XXX/order/status`

## value 和 method 注解参数

value 属性**必须设置**，其可以是一个数组，当传入一个数组。如果是数组的话，就是多个请求地址对应一个处理方法

```java
@RequestMapping(
	value = {"/hello", "/test"}
)
public String hello() { return "hello"; }
```

method 属性也可以设置多种请求方式，当设置 method 属性的时候通过 `RequestMehod` 枚举来设置

```java
@RequestMapping(
    value = {"/test", "testmapping"},
    method = { RequestMethod.GET }
)
public String success() { return "success"; }
```

## @RequestMapping 派生注解

如果不想设置 method 属性可以用以下注解来设置，**这些注解依然要设置 value 属性**：

1. 处理 get 请求：`@GetMapping`
2. 处理 post 请求：`@PostMapping`
3. 处理 put 请求：`@PutMapping`
4. 处理 delete 请求：`@DeleteMapping`

## params 注解参数

 RequestMapping 注解的 params 属性通过请求的请求参数匹配请求映射
@RequestMapping 注解的 params 属性是一个字符串类型的数组，可以通过四种表达式设置请求参数和请求映射的匹配关系

1. "param"：要求请求映射所匹配的请求必须携带 param 请求参数

   ```java
   @RequestMapping(params = {"username"}) // 表示请求必须包含 username 参数，不限制参数值
   ```

2. "!param"：要求请求映射所匹配的请求不可以带有 param 请求参数

   ```java
   @RequestMapping(params = {"!username"}) // 表示请求不允许带有 username 参数
   ```

3. "param = value"：要求请求映射所匹配的请求必须携带 param 请求参数且 parama == value

   ```java
   @RequestMapping(params = {"username = admin"}) // 表示请求必须包含 username 参数且值必须为 admin
   ```

4. "param != value"：要求请求映射所匹配的请求必须携带 param 请求参数但 parama != value

   ```java
   @RequestMapping(params = {"username != admin"}) // 表示请求必须包含 username 参数且值不可以为 admin
   ```

params 属性如果设置了多个属性值，则必须全部要匹配上

注意一点，如果要使用 thymeleaf 在 URL 上拼接参数的话，直接在 URL 后面用 `?` 拼接会有红线报错（实际使用不影响），如果想要消除红线，则通过括号的方式来追加参数：

```html
<a th:href="@{/test(username=123)}">通过 GET 方式请求并携带 params -> /test</a>
```

## headers注解属性

headers 注解使用的基本和 params 的使用方法一致，其规定请求头中必须包含、不包含某个 key，并且还可以规定某个 key 必须为一个特定的 value 或者不为一个特定的 value。

具体写法就不写了，但是要注意一点：如果 headers 有不匹配的地方，会报 404 的错误；value 不匹配也是 404；method 不匹配是 405；参数不匹配是 400

## ant 风格路径

ant 风格其实也就是模糊匹配，可以在 @RequestMapping 注解的 value 属性中设置一些模糊匹配的条件：

1. ?：表示任意单个字符（特殊字符除外）
2. \*：表示任意的 0 个或多个字符
3. \*\*：表示任意的一层或多层目录

需要注意的一点是：在使用 \*\* 的时候，只能使用 /\*\*/xxx 的方式

## 路径中的占位符（重要）

当通过 GET 方式添加参数的时候，原始方式是这样的：

```
/deleteUser?id=1
```

rest 方式就是这样的：

```
/deleteUser/1
```

通过直接来设置占位符：

```java
@RequestMapping("/testPath/{id}")  
public String testPath(@PathVariable("id") Integer id) {  
    System.out.println(id);  
    return "success";  
}
```

想要获取占位符的值的方式比较单一，通过给参数设置 `@PathVariable` 注解的方式来获取，其中注解的 value 值要设置的和占位符的 name 一致

注意：一旦设置占位符，那么请求路径中必须要包含参数，否则就会 404

里面还有个 `required` 属性可以设置

# SpringMVC 获取请求参数

有很多种方式可以获取到请求相关的参数，比如可以通过 `ServletAPI` 获取，还可以通过控制器方法的形参获取等等

## 通过 ServletAPI 获取

这种方式不常用，只做了解

```java
@RequestMapping("/testServletAPI")  
public String testServletAPI(HttpServletRequest req) {  
 String username = req.getParameter("username");  
 String password = req.getParameter("password");  
 System.out.println("username: " + username + "\npassword: " + password);  
  
 return "success";  
}
```

## 通过控制器方法的形参获取

这种方式只要保证控制器方法的形参的名字和请求参数的名字保持一致就可以获取到，比如说前端的一个请求里有一个叫 `username` 和一个叫 `password` 的参数，那么方法中只要保证形参名字也是 `username` 和 `password` 即可

```java
@RequestMapping("/testParam")  
public String testParam(String username, String password) {  
 System.out.println("username: " + username + "\npassword: " + password);  
 return "success";  
}
```

如果是多个参数的话，有两种方式获取：
1. 使用字符串类型形参：这种情况，框架会把多个参数用**逗号**把参数拼接起来
2. 使用字符串数组形参

## 请求参数和控制器方法形参映射

通过 `@RequestParam` 注解来实现映射，value 填写请求参数中的 name，一旦这么设置了之后，形参名字就随便起

```java
@RequestMapping("/testParam")  
public String testParam(  
 @RequestParam("user_name") String username,  
 String password,  
 String hobbies) {  
 System.out.println("username: " + username + "\npassword: " + password + "\nhobbies: " + hobbies);  
 return "success";  
}
```

里面还有个 `required` 属性可以设置，如果设置为 false 的时候，没有传过来的话就是 null，如果设置 true 但是没传就报 400；

还有 `defaultValue` 属性可以设置，如果请求中根本没有某个参数，或者有这个参数但是没有值，那么这个参数就会被设置为提前设定好的默认值

## 其他注解获取请求相关值

1. `@RequestHeader` 注解通过设置 value 可以获得请求头中特定的某条属性的值，这个注解也可以设置 `required` 属性和 `defaultValue` 属性
2. `@CookieValue` 可以获取到 `JSESSIONID` ，用法没什么区别

## 通过 POJO 封装请求参数

使用方法也很简单，就是让请求参数的名字与提前设计好的 Bean 的 field name 保持一致，然后在形参的地方直接用这个 Bean 来封装就可以了，大概写法如下：

```java
@RequestMapping("/testPOJO")  
public String testPOJO(User user) {  
 System.out.println(user);  
 return "success";  
}
```

前端会提交一个表单，表单里的数据有 `username`, `password`, `sex`, `age`, 和 `email`；User 对象里也有这五个 field

## 设置 CharacterEncodingFilter

在 web.xml 文件中添加：

```xml
<filter>  
 <filter-name>CharacterEncodingFilter</filter-name>  
 <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>  
 <init-param>  
 <param-name>encoding</param-name>  
 <param-value>UTF-8</param-value>  
 </init-param>  
</filter>
```

只要设置了 encoding 这个属性，它会强制要求 request 的参数用 UTF-8 编码，如果要求 response 也用的话，在多写一个 `init-param` 给那个 forceResponseEncoding 一个 true 就可以了（点进源码看）

# 域对象共享数据

有三（四）种不同作用范围的域对象，还有一个 JSP 不用了：
1. request 域对象：作用范围为一次请求，转发不会使 request 域对象失效
2. session 域对象：作用范围为一次会话，只要浏览器窗口打开没有关闭，则 session 一直一直存在，session 默认有效时长为 30 分钟
3. application 域对象：作用范围为整个 WEB 应用，只要服务器不关闭，这域对象一直有效

## 通过 ServletAPI 向域对象共享数据

使用方法为在控制器方法中获取到本次的 request，并且通过 set, get, remove 等方法对共享的数据进行操作（不推荐使用 ServletAPI）

```java
@RequestMapping("/testRequestByServletAPI")  
public String testRequestByServletAPI(HttpServletRequest req) {  
 req.setAttribute("testRequestScope", "hello ");  
  
 return "success";  
}
```

然后，通过 thymeleaf 获取到 request 中共享的数据：

```html
<!--/*@thymesVar id="testRequestScope" type="java.lang.String"*/-->
<p th:text="${testRequestScope}"></p><br>
```

## 通过 ModelAndView 实现

这个方式就是先创建 `ModelAndView` 对象，然后设置好 Model 数据，然后设置好视图名称，将其作为结果返回

```java
@RequestMapping("/testModelAndView")  
public ModelAndView testModelAndView() {  
 ModelAndView mav = new ModelAndView();  
 mav.addObject("testModelAndView", "Hello Model and View");  
 mav.setViewName("success");  
 return mav;  
}
```

在视图中获取数据的方式和之前一样

```html
<!--/*@thymesVar id="testModelAndView" type="java.lang.String"*/-->
<p th:text="${testModelAndView}"></p><br>
```

## 通过 Model 实现

其实这个最终也是要依靠 `ModelAndView` 来实现，先把 Model 创建出来，然后把需要的数据封装好，最后再 return 一个视图名称出去

```java
@RequestMapping("/testModel")  
public String testModel(Model model) {  
 model.addAttribute("testModel", "Hello Model");  
 return "success";  
}
```

在视图中获取（略）

## 通过 Map 实现

基本和 Model 一样，形参变成 Map，实现过程大同小异

```java
@RequestMapping("/testMap")  
public String testMap(Map<String, Object> map) {  
 map.put("testMap", "Hello Map");  
 return "success";  
}
```

## 通过 ModelMap 实现（略）
## 对以上三种方法的总结

实际上，这三个方法最后使用的实例对象都是同一个对象，并且这个对象实现了 Model 接口、实现了 Map 接口、同时也是 ModelMap 的子类

实际运行的示例的对象是：BindingAwareModelMap，这东西是继承的 ExtendedModelMap，而 ExtendedModelMap 在继承 ModelMap 的同时，还实现了 Model 接口

```java
public interface Model{}
public class ModelMap extends LinkedHashMap<String, Object> {}
public class ExtendedModelMap extends ModelMap implements Model {}
public class BindingAwareModelMap extends ExtendedModelMap {}
```

推荐使用 ModelAndView，因为后面这三个方法，最后都会封装进 ModelAndView 中，如果想研究源码的话，就去 DispatcherServlet 中去找

## 向 session 域对象共享数据

推荐使用原生 ServletAPI 来实现，在控制器方法上给一个 `HttpSession` 对象作为形参，来获取到 session 对象，并且设置共享数据：

```java
@RequestMapping("/testSession")
public String testSession(HttpSession session) {
	session.setAttribute("testSession", "Hello Session");
	return "success";
}
```

在视图中获取这个数据，只需要通过 `session.key` 的方式就可以获取到

## 向 application 域对象共享数据

这个也是通过 ServletAPI 先获取到 application 域对象，通过 `HttpSession` 先获取 session，然后在通过它的 `getServletContext()` 获取到：

```java
@RequestMapping("/testApplication")  
public String testApplication(HttpSession session) {  
 ServletContext application = session.getServletContext();  
 application.setAttribute("testApplication", "Hello Application");  
 return "success";  
}
```

# SpringMVC 视图

SpringMVC中的视图是View接口，视图的作用渲染数据，将模型 Model 中的数据展示给用户

SpringMVC视图的种类很多，默认有**转发视图**和**重定向视图**

当工程引入jstl的依赖，转发视图会自动转换为JstlView；

若使用的视图技术为Thymeleaf，在 SpringMVC 的配置文件中配置了 Thymeleaf 的视图解析器，由此视图解析器解析之后所得到的是 ThymeleafView

如果视图名称没有任何前缀和后缀的话，那么 SpringMVC 就会根据配置文件中配置的视图解析器去创造对应的视图

## 转发视图

（用的比较少）

SpringMVC 中默认的转发视图是 InternalResourceView，当控制器方法中所设置的视图名称以 `forward:` 为前缀的时候，就会创建 InternalResourceView 视图，此时的视图名称，不会被 SpringMVC 配置文件中所配置文件中所配置的视图解析器解析，而是会先去掉 forward 前缀，然后剩余的部分以转发的形式实现跳转

```java
@RequestMapping("testForward")
public String testForward() {
	return "forward:/testXXX";
}
```

## 重定向视图

SpringMVC中默认的重定向视图是RedirectView，当控制器方法中所设置的视图名称以"redirect:"为前缀时，创建RedirectView视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析

```java
```java
@RequestMapping("testRedirect")
public String testRedirect() {
	return "redirect:/testXXX";
}
```

# RESTFul

RESTFul 是一种风格，全程为 Representational State Transfer，表示层资源状态转移

