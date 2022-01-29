---
creation date: 2022-01-22 20:34:25
last modified: 2022-01-22 20:35:00
title: MyBatis
categories:
- database
tags:
- mybatis
---



# MyBatis 基础入门



## 主配置文件

注意：集成到 SpringBoot 之后，可以统一在 `applicaiton.yaml` 中配置，所以基本用不到这个，具体入门使用可以去看官网

https://mybatis.org/mybatis-3/zh/getting-started.html#

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <!-- 指定 mapper 文件路径 -->
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```





## mapper XML文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace 写对应 mapper 接口的全类名 -->
<mapper namespace="">
    <!-- id 为 mapper 中的方法名 -->
    <select id="" resultType="">
        
    </select>
</mapper>
```



1. `mapper` 是根标签，必须有，其中 namespace 属性必须有值，且一般为 Dao 接口的全类名
2. `mapper` 中可以写 `<select>`， `<update>`， `<delete>`， `<insert>`等标签来写对应的语句
2. 不同标签中的 `id` 一般为 Dao 接口中的方法名



## 获取 sqlSession

注意：当 MyBatis 集成到 SpringBoot 之后，SpringBoot 会自动根据我们在 `application.yaml` 中配置的数据源等信息，自动在容器内创建了 `SqlSessionFactory`，所以我们直接就能拿到，下面展示的是最原始用法

```java
@Test
public void testSelectStudentById() throws IOException {
    String config = "mybatis.xml";
    InputStream inputStream = Resources.getResourceAsStream(config);
    // 基于配置文件来创建 SQLSessionFactory
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession session = sqlSessionFactory.openSession();

    String sqlId = "com.demo.mybatisstart.dao.StudentDao" + "." + "selectStudentById";
    // 第二个参数为，selectStudentById 的参数
    Student student = session.selectOne(sqlId, 1);

    System.out.println(student);

    session.close();
}
```



## 初见占位符

占位符为 `#`，在写 sql 语句的时候通过 `#{}` 的方式，中括号中的内容，如果不用 `@param` 注解来设置 name 的话，默认中括号中写形参的名字

```java
public Student selectStudentById(Integer id);
```

```xml
<mapper namespace="com.demo.mybatisstart.dao.StudentDao">
    <select id="selectStudentById" resultType="com.demo.mybatisstart.domain.Student">
        select * from student where id = #{id};
    </select>
</mapper>
```



当参数为对象的时候，`#{}`中写对象的属性名

```xml
<insert id="insertStudent">
    insert into student values(#{id}, #{name}, #{email}, #{age})
</insert>
```



## 事务提交

注意：MyBatis 集成到 SpringBoot 之后，auto commit 的功能是自动打开的，如果是原生使用 MyBatis 的话，似乎是默认关闭的，所以当对数据库有修改的时候，需要手动执行一下 `session.commit()`

```java
@Test
public void testInsertStudent() throws IOException {
    SqlSession session = sqlSessionFactory.openSession();
    String sqlId = "com.demo.mybatisstart.dao.StudentDao" + "." + "insertStudent";
    int rows = session.insert(sqlId);
    session.commit();
    System.out.println(rows);
    session.close();
}
```



# 重要对象



## SqlSessionFactory

这实际上是一个接口，它的具体实现类为 DefaultSqlSession。通常这个对象一个项目中只应该存在一个，因为它的创建占用的资源很多

其中常用方法：

1. openSession()：返回一个 sqlSession 对象，默认需要手动 commit
2. openSession(boolean b)：返回一个 SQLSession 对象，布尔值决定是否自动 commit



## SqlSession

这本身也是个接口，它的具体实现类为 DefaultSqlSession。这个默认的实现类**不是线程安全的**，使用时需在一个方法体内获取 SqlSession，执行 sql 之后，关闭 SqlSession

它提供了很多执行 sql 语句的方法：

1. selectOne：执行 sql 语句，最多得到一行记录，否则报错
2. selectList：得到多行记录
3. selectMap：得到一个 Map 集合
4. insert：插入新数据
5. update：更新数据
6. delete：删除数据
7. commit：提交事务
8. rollback：回滚事务



# 代理



## Dao 实现对象

MyBatis 通过代理的方式，来动态的在内存中创建 Dao 接口的代理对象。只要我们在对应的 mapper 文件中提供必要信息即可，这些信息有 Dao 的全类名，Dao 中的方法名，以及返回类型

当我们想直接调用 Dao 中的方法的时候，我们通过 sqlSession 中的 getMapper 方法来得到 Dao 的实现类：

```java
@Test
public void testDaoProxy() {
    SqlSession sqlSession = sqlSessionFactory.openSession();
    StudentDao mapper = sqlSession.getMapper(StudentDao.class);
    Student student = mapper.selectStudentById(1);
    System.out.println(student);
    sqlSession.close();
}
```



## 参数

parameterType 为参数类型，可以不写，MyBatis 通过反射机制可以拿到接口中参数的类型

当只有一个简单类型的参数的时候，sql语句中 `#{}` 内的接参数的名字任意；当时多个参数的时候，需要通过 `@param` 注解来命名参数，然后在 sql 语句中，占位符中写的就是给 `@param` 的参数值

```java
public Student selectStudentById(@Param("id") Integer id);
```



当形参是一个对象的时候，MyBatis 会调用对象对应属性名的 get 方法来获取值，无论是查询还是修改操作，都可以用对象来作为参数

```xml
<select id="selectStudentsByObject" resultType="com.demo.mybatisstart.domain.Student">
    select * from student where name = #{name} or age = #{age}
</select>
```

```java
// 通过对象查询
public List<Student> selectStudentsByObject(Student student);
```



另外，形参不一定非是对应表的对象，只要占位符用的名字，能和对象的属性名对应上，并且满足查询逻辑，一样也可以工作。



## 按位置传参

参数位置从 0 开始，语法为 `#{arg位置}`，这个参数的位置对应方法形参的顺序

注意：从 3.3 版本之前是使用 `#{0}`, `#{1}` 的方式，从 3.4 之后都使用 `#{arg0}`



## 用 Map 传参

用键值对的方式来实现，`#{}`里面写 key，一般不用



# 占位符



## #占位符

`#`占位符底层使用 PreparedStatement 实现，值会替换掉 `?`

使用 `#` 更安全，有效避免 SQL 注入



## $占位符

`$` 占位符使用的 Statement 作为底层，通过字符串拼接的方式来生成一条 SQL 语句，参数传给它什么，它就原封不动的替换过去，传字符串为参数的时候记得要加引号

**注意：参数必须使用 `@Param` 来命名**

通常，`$` 占位符用来表示表名或者列名，比如 `ORDER BY ${colName}`



# ResultType

resultType 是在执行 select 时使用，作用在 \<select\> 标签中使用

resultType 值一般为两种：

1. 全类型
2. 别名

别名有两种方式

1. 在主配置中（不用这个）（在 \<typeAliases\> 标签中）

   也可以在其子标签下，使用 \<package\> 标签，然后别名就为其类名

2. 在类上方添加 `@Alias` 注解

# 模糊查询

两种方式实现：

1. 给`#{}` 直接传入 `%param%` 的方式，这样的话参数需要带上 `%`

2. 在 SQL 语句中按下面格式写

   ```sqlite
   select * from student where name like "%" #{} "%"
   ```

   注意空格



# 动态 SQL

同一个 Dao 方法可以根据不同的条件，表示不同的 SQL 语句，主要是 where 部分有变化

主要使用 MyBatis 提供的标签，来实现：\<if\>, \<where\>, \<foreach\>, 和 \<sql\> （主要）

在使用动态 SQL 的时候，Dao 方法的形参使用 Java 对象





