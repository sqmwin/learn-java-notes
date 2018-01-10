[TOC]

---

# 5. SSH框架整合技术

## 5.1 Spring 与Hibernate 整合

-   除了JdbcTemplate 外，Spring 还可通过 Hibernate 来完成 Dao 的工作。即将 **Spring 与Hibernate 进行整合**

### 5.1.1 导入jar包

1.  Spring AOP 两个jar包:aop-alliance,spring-aop

2.  AspectJ两个jar包aspectj-weaver,spring-aspectj

3.  Spring的JDBC jar包spring-jdbc,mysql-connector

4.  Spring整合ORM的jar包

    -   spring-orm-x.x.x.RELEASE.jar:Object/Relational Mapping

    -   maven:

        -   ```xml
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-orm</artifactId>
                <version>5.0.2.RELEASE</version>
            </dependency>
            ```

5.  Spring的事务Jar包

    -   spring-tx

6.  Hibernate的基本jar包

    -   hibernate-core-5.2.12.Final

    -   maven:

    -   ```xml
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>5.2.12.Final</version>
        </dependency>
        ```

        -   依赖c3p0包

### 5.1.2 实体映射文件的配置

-   ```xml

    ```

-   ​

### 5.1.3 注册数据源

### 5.1.4 配置SessionFactory

1.  注意映射文件的注册方式
2.  注意当前Sessiong上下文所用的类

### 5.1.5 定义dao实现类

### 5.1.6 注册dao与service的bean

### 5.1.7 配置事务管理器

### 5.1.8 配置事务通知及顾问

### 5.1.9 (了解)在Spring中一般不适用Hibernate模板对象

## 5.2 Spring在web项目中的使用

### 5.2.1 不适用Spring的web插件

1.  新建一个web project
2.  定义index页面
3.  定义loginServlet(重点代码)
4.  定义success页面
5.  复制dao_hibernate中内容
6.  直接发布运行
7.  运行结果分析

### 5.2.1 使用Spring 的 Web 插件

1.  导入jar包
2.  注册监听器 ContextLoaderListener
3.  指定 Spring 配置文件的位置\<context-param/>
4.  修改 Spring配置文件中映射文件路径的写法
5.  通过 WebApplicationContextUtils获取 Spring 容器

## 5.3  Spring 与 Struts2 整合

### 5.3.1 Action实例由Struts2 自己创建，Servcie由容器注入给Action

1.  导入jar包
2.  将Service声明为Action的属性

### 5.3.2 Action实例由Spring容器创建

1.  将Service声明为Action的属性
2.  Spring 配置文件中注册 Action 的 Bean
3.  设置 Action 的 Bean 的scope为 prototype
4.  Struts2 配置文件中的 class 使用伪类名

## 5.4 SSH 整合 Jar包总结

### 5.4.1 Struts2 中的 Jar 包

1.  Struts的基本jar包
2.  Struts2 与 Spring 整合插件 Jar 包
3.  Struts2 与 JSon 整合插件 Jar 包
4.  Struts2 注解开发 Jar 包

### 5.4.2 Spring 中的Jar包

1.  Spring 的基本 Jar包
2.  AOP 开发需要的 Jar包
3.  Spring 整合 ORM 所需 Jar 包
4.  Spring JDBC 开发需要的 Jar包
5.  Spring 事务管理需要的 Jar 包
    -   与上面是同一个包
6.  Spring 在 WEB 项目中使用所需 Jar 包

### 5.4.3 Hibernate中的Jar包

1.  required目录下的所有jar包
2.  optional/ehcache/slf4j-api-1.6.1.jar
    -   若用到了 ehcache二级缓存，则需导入 optional/ehcache中的所有包
3.  optional/c3p0 目录下所有 Jar
4.  jpa/hibernate-entitymanager-5.0.1.Final.jar
5.  jpa-metamodel-generator 目录下的 Jar
6.  junit-4.9.jar
7.  slf4j-log4j12-1.7.12.jar
8.  mysql 驱动

### 5.4.4 包冲突

-   在不同的框架中，若存在相同的 Jar 包，即使版本号不同，也将会引发包冲突问题，导致程序运行出错。**一般是舍弃低版本，保留高版本**

## 5.5 OpenSessionInView

### 5.5.1 问题的引出

1.  定义index页面
2.  定义Action
3.  定义Service接口
4.  定义service实现类
5.  定义dao接口
6.  定义到实现类
7.  修改struts.xml
8.  修改spring配置文件

###5.5.2 原因分析

### 5.5.3 解决办法

## 5.6 SSH全注解开发

### 5.6.1 添加Struts2与Spring注解

1.  建立数据库
2.  修改dao实现类UserDaoImpl
3.  修改Service实现类UserServiceImpl
4.  修改LoginAction
5.  在 Spring 配置文件中注册组件扫描的基本包
6.  在 Spring配置文件中开启 Spring事务注解驱动

### 5.6.2 添加Hibernate注解

1.  修改实体类
2.  删除映射文件
3.  在Spring配置文件的 SessionFactory 定义中，删除映射文件的配置mappingResources
4.  在 Spring 配置文件的 SessionFactory定义中，添加实体包扫描器。SSH 全注解完成