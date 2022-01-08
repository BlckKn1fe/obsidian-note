---
creation date: 2022-01-06 10:36:40
last modified: 2022-01-07 18:07:23
title: SpringBoot
categories:
- spring
- back-end
tags:
- springBoot
---

文档地址：https://yuque.com/atguigu/springboot

本视频源码地址：https://gitee.com/leifengyang/springboot2

SpringBoot 通过 pom 来引入各种依赖，并且这些依赖有它自己官方给你配置好的默认配置，不需要我们去手动操作。SpringBoot 通过 starter 为单位，为不同的开发场景提供不同 set of dependencies

# 容器

## @Configuration 注解

可以使用一个配置类来代替配置文件，需要在类上添加 `@Configuration` 注解

```java
@Configuration
public class MyConfig { 
 	@Bean
    public User user01() { return new User("张三", 23); }
}
```

@Configuration 注解中，有一个叫 proxyBeanMethods 的属性，默认是 true，其作用就是为了保证 MyConfig 实例去调用声明的方法的时候，返回的都是单一实例，确保从容器内拿到的对象是同一个。

当 proxyBeanMethods 设置为 false 的时候，是一种 lite 模式，此时容器中的组件之间不应该存在依赖关系；proxyBeanMethods 设置为 true 的时候，为 full 模式，可以满足组件之间的依赖关系

@Bean 注解作用在方法体上，用来告诉 SpringBoot 方法声明的是一个容器中的组件对象

## @Import 注解

`@Import` 注解用来给容器中导入组件，可以作用在任意一个容器对象的类上，推荐写在配置类上面；注解的参数为一个 class 对象的数组，框架会调用该类的无参构造来创建对象在容器中，**默认组件的名字就是全类名**

## @Conditional 注解

条件装配：满足Conditional指定的条件，则进行组件注入，它有很多派生注解

比如说，有一个 User 对象，它的 field 中有一个 Pet 类，如果说容器中没有存在 Pet 对象来给 User 设置的话，就可以通过 @Conditional 注解来规定在容器中是否要生成 User 对象

```java
// 配置类中某个方法
@ConditionalOnBean(name = "cat")
@Bean
public User user01() {
    User user = new User("name", 20);
    // cat() 为配置类中配置的另外一个 Pet 对象
    user.setPet(cat());
    return user;
}

```

上面这段 demo 的意思就是，只有咋容器中存在 name 叫 cat 的的 Bean 对象的时候，才会创建 user01 这个对象

如果注解设置在 class 上的话，那么所有配置的 Bean 都需要满足设置的条件

## @ImportResource 注解

这个方式主要是用在配置类上，导入 xml 配置文件的，有的老项目如果全部改成用注解的方式会比较麻烦，可以通过在配置类上标注 @ImportResource 的方式来加载 xml 配置文件

注意：如果没有这个注解，SpringBoot 是不会自己去读取配置文件的

```java
@ImportResource("classpath:beans.xml")
```

## 配置绑定（properties）

@ConfigurationProperties 可以把配置文件中的值读取到 Java 对象中，这个注解中可以对 prefix 或 value 进行设置，假设有一个配置文件

```properties
mycar.brand=BYD
mycar.price=100000
```

这个 mycar 就是前缀，然后我们在写一个 Car 对象来装配这些配置信息

```java
@Component("car")
@ConfigurationProperties("mycar")
public class Car {

    private String brand;
    private Integer price;
	
    // 略...
}

```

此时，brand 和 price 就会对应上，然后装配上

还有一种方式可以达到一样的效果

通过 @EnableConfigurationProperties + @ConfigurationProperties 的方式

## 自动配置原理

@SpringBootApplication 注解相当于：

- @SpringBootConfiguration
- @EnableAutoConfiguration
- @ComponentScan

三个注解加在一起，其中 @EnableAutoConfiguration 中还有一个@AutoConfigurationPackage，它的作用是读到程序入口，然后获取入口所在包下的所有组件，并且将他们全部注册到容器中

@EnableAutoConfiguration 的另外一个注解是 @Import(AutoConfigurationImportSelector.class)，它归根结底是通过底层加载配置文件然后通过 SpringFactories 去生成需要的组件，配置文件的位置在 META-INFO/spring.factories 里，主要在 spring-boot-autoconfigure-version.RELEASE.jar 中

实际运行的时候，它并不会把 spring.factories 中全部的组件都加载进去，它会按需、按照场景加载，会根据 @Conditional 注解来限制，比如说某一个包中的某个组件是否加载，取决于实际工作场景中的 class 加载的全不全，如果不全就说明不需要加载这个组件

加载顺序大概就是先从 XXXAutoConfiguration 中去根据条件往容器中添加满足条件的组件，然后这些组件的具体配置在从 XXXProperties 对象中去拿值，而 XXXProperties 又会去 application.properties 去找值

# 开发技巧

## Lombok

引入 Lombok 依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

通过 Lombok 可以直接在 Bean 对象上直接添加 

- @AllArgsConstructor
- @NoArgsConstructor
- @Data

这些直接，它会自动的添加 set、get、构造函数、toString 等方法

并且，可以直接在任意类上添加 Logger 的相关注解，在类中可以直接通过 log.XXX 的方式使用 logger 相关的功能

更多其他使用参考官网：

https://projectlombok.org/features/all

## Dev-Tolls

感觉没啥调用，可以重新 reload 项目，还有其他功能，具体看官网吧

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

Ctrl + F9 进行 reload

## Spring Initializer

就是 IDEA 自带的那个，可以提前设置好很多 starter，IDEA 也会帮我们自动创建好项目目录结构等等

# 配置文件

## yaml 形式

除了用 properties 的方式，SpringBoot 还支持用 yaml 的格式来配置 SpringBoot

它的基本语法：

1. 键值对形式，`key: value`，注意冒号后面有空格
2. 大小写敏感
3. 缩进表示层级关系
4. 缩进多少不重要，只要相同层级元素左对齐
5. "#" 来表示注释

表示对象：

```yaml
# 行内写法
k: {k1: v1, k2: v2, k3: v3}
# 或者
k:
	k1: v1
	k2: v2
	k3: v3
```

表示数组：

```yaml
# 行内写法
k: [v1, v2, v3]
# 或者
k:
	- v1
	- v2
	- v3
```

注意一点，单引号之间的内容会将特殊字符直接输出出来，会被转义，但是双引号之间的内容特殊字符会生效，不被转义。比如单引号的 `\n` ，控制台打印是 `\n`前端收到的是 `\\n`；而双引号的话，控制台会直接幻皇，前端收到的是 `\n`

