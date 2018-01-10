[TOC]

# 1.MyBatis入门

## 1.1 mybatis安装

​	要使用 MyBatis， 只需将 [mybatis-x.x.x.jar](https://github.com/mybatis/mybatis-3/releases) 文件置于 classpath 中即可。

​	如果使用 Maven 来构建项目，则需将下面的 dependency 代码置于 pom.xml 文件中：

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

## 1.2 MyBatis概述

-   MyBatis是一个优秀的基于 Java 的持久层框架，它**内部封装了 JDBC**，使开发者**只需关注SQL 语句**本身，而不用再花费精力去处理诸如注册驱动、创建 Connection、配置 Statement
    等繁杂过程。

### MyBatis 与 Hibernate

-   Hibernate
    -   全自动ORM:自动封装数据库,实现了POJO 和数据库表之间的映射，以及 SQL 的自动生成和执行。
-   mybatis
    -   半自动ORM:做好POJO与SQL语句的映射但不会自动生成SQL语句
-   mybatis特点:
    -   在 XML 文件中配置 SQL 语句，实现了 **SQL 语句与代码的分离**，提高程序维护性
    -   程序员自己去编写SQL 语句，程序员可以结合数据库自身的特点灵活控制SQL 语句，因此能够实现比Hibernate等全自动 ORM框架更高的查询效率，能够完成复杂查询。
    -   简单，易于学习，易于使用，上手快。
-   mybatis体系结构
-   ![](07_mybatis01.jpg)
-   ​

##1.3 mybatis工作原理

-   ![](07_mybatis02.jpg)

## 1.4 mybatis程序

-   将student信息写入database中

### 1.4.1 基本程序

1.  导入jar包

    1.  mybatis-3.4.5.jar
    2.  mybatis依赖jar包,所有依赖包[官网上有](http://www.mybatis.org/mybatis-3/zh/dependencies.html),先列出必要的
        1.  ognl-3.1.15.jar
        2.  javassist-3.22.0-CR2.jar
        3.  slf4j-api-1.7.25.jar
        4.  slf4j-api-log4j12-1.7.25.jar
        5.  log4j-1.2.17.jar
        6.  commons-logging-1.2.jar
        7.  log4j-core-2.10.0.jar
        8.  log4j-api-2.10.0.jar
        9.  cglib-3.2.5.jar
            1.  cglib的依赖包ant-1.9.6.jar与ant-launcher-1.9.6.jar
        10.  数据库连接池:commons-dbcp-1.4.jar与commons-pool-1.5.4.jar
    3.  测试包junit-4.12.jar
    4.  jdbc包mysql-conncetor-java-5.1.6.jar
    5.  mybatis增强缓存包mybatis-encache-1.0.3.jar
    6.  spring与mybatis整合包mybatis-spring-1.2.2.jar(可选)

    maven中pom.xml配置:

    ```xml
        <properties>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
        
        <dependencies>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.6</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.4.5</version>
            </dependency>
            <dependency>
                <groupId>ognl</groupId>
                <artifactId>ognl</artifactId>
                <version>3.1.15</version>
            </dependency>
            <dependency>
                <groupId>org.javassist</groupId>
                <artifactId>javassist</artifactId>
                <version>3.22.0-CR2</version>
            </dependency>
            <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-api</artifactId>
                <version>1.7.25</version>
            </dependency>
            <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
                <version>1.7.25</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.17</version>
            </dependency>
            <dependency>
                <groupId>org.apache.logging.log4j</groupId>
                <artifactId>log4j-core</artifactId>
                <version>2.10.0</version>
            </dependency>
            <dependency>
                <groupId>org.apache.logging.log4j</groupId>
                <artifactId>log4j-api</artifactId>
                <version>2.10.0</version>
            </dependency>
            <dependency>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
                <version>1.2</version>
            </dependency>
            <dependency>
                <groupId>cglib</groupId>
                <artifactId>cglib</artifactId>
                <version>3.2.5</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
            <dependency>
                <groupId>commons-dbcp</groupId>
                <artifactId>commons-dbcp</artifactId>
                <version>1.4</version>
            </dependency>

            <dependency>
                <groupId>org.mybatis.caches</groupId>
                <artifactId>mybatis-ehcache</artifactId>
                <version>1.0.3</version>
            </dependency>
        </dependencies>
    ```

2.  定义实体pojo类:Student类

3.  在mysql数据库中生成表结构schema:与student对应

4.  定义dao接口:提供CRUD功能

5.  **定义映射文件**

    -   映射文件简称mapper,一般放在dao包中,格式为xml.如mapper.xml;maven中放在resources目录下的自定义的文件夹中

    -   mapper的约束dtd文件在mybatis核心jar包的org.apache.ibatis.builder.xml中(**mybatis-3-mapper.dtd**),此包中还有mybatis的主配置文件(**mybatis-3-config.dtd**)

    -   映射文件头

        -   ```xml
            <?xml version="1.0" encoding="UTF-8" ?>
            <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
            ```

        -   ​

    -   映射文件的属性

        -   mapper标签:根元素

            -   namespace属性:每个mapper的ns必须不同,此属性用于区分不同mapper的同名的sql映射id

            -   insert标签:内是SQL插入语句

                -   id属性:在命名空间中唯一的标识符，可以被用来引用这条语句。
                -   parameterType:将会传入这条语句的参数类的完全限定名或别名。

            -   ```xml
                <mapper namespace="studentdao">
                    <insert id="insertStudent" parameterType="com.sqm.pojo.Student">
                        INSERT INTO student(id,name,age,score)
                        VALUES (#{id},#{name},#{age},#{score})
                    </insert>
                </mapper>
                ```

            -   `#{xxx}`:代表预处理语句,在sql中将被识别为?,占位符

    -   映射文件的顶级元素(mapper标签的子标签),按照应该被定义的顺序:

    -   `cache` – 给定命名空间的缓存配置。

    -   `cache-ref` – 其他命名空间缓存配置的引用。

    -   `resultMap` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。

    -   `parameterMap` –~~已废弃！老式风格的参数映射。内联参数是首选,这个元素可能在将来被移除，这里不会记录。~~

    -   `sql` – 可被其他语句引用的可重用语句块。

    -   `insert` – 映射插入语句

    -   `update` – 映射更新语句

    -   `delete` – 映射删除语句

    -   `select` – 映射查询语句

        ​

6.  定义主配置文件

    1.  主配置文件也是xml格式,dtd约束为mybatis-3-config.dtd,主配置文件命名随意,例如mybatis.xml;主配置文件一般放在根目录,maven中放在resources目录

        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE configuration
                PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-config.dtd">
        ```

    2.  主配置文件负责配置mybatis运行环境和注册映射文件

    3.  主配置文件的顶层结构如下:

        -   configuration 配置
        -   properties 属性
        -   settings 设置
        -   typeAliases 类型别名
        -   typeHandlers 类型处理器
        -   objectFactory 对象工厂
        -   plugins 插件
        -   environments 环境
            -   environment 环境变量
                -   transactionManager 事务管理器
                -   dataSource 数据源
        -   databaseIdProvider 数据库厂商标识
        -   mappers 映射器

    4.  environments标签中定义数据源的四大属性:

        -   ```xml
            <dataSource type="POOLED">
              <property name="driver" value="${driver}"/>
              <property name="url" value="${url}"/>
              <property name="username" value="${username}"/>
              <property name="password" value="${password}"/>
            </dataSource>
            ```

    5.  映射器mappers中的子标签mapper用于注册各个映射

        -   ```xml
            <mappers>
              <mapper resource="mapper/StudentDaoMapper.xml"/>
            </mappers>
            ```

7.  定义dao实现类

    -   ```java
        public class StudentDaoImpl implements IStudentDao{
        	private SqlSession session;
            public void insertStudent(Student sutdent){
                try{
        			//1.读取主配置文件
        			//2.创建SqlSessionFactory对象
        			//3.创建SqlSession对象
        			//4.通过SqlSession操作数据
        			//5.提交SqlSession

                } catch(IOException e){
                	e.printStackTrace();
                } finally {
                	//6.关闭SqlSession

                }	
            } 
        }
        ```

8.  定义测试类

    -   ```java
        IStudentDao dao = new StudentDaoImpl();
        Student student = new Student("sqm",26,88);
        dao.insertStudent(student);
        ```

9.  添加**日志控制文件**

    -   mybatis中常用log4j进行日志处理,将log4j.properties放入源目录下

    -   日志级别为debug,可显示执行的sql语句,参数值,对db的影响条数

    -   日志级别为trace,可多显示出查询出的每条记录的每个字段名及值

    -   若使用跟日志对象rootLogger,会输出太多信息,建议更改为mapper的namespace值:如logger.test

        -   ```properties
            //输出太多信息
            log4j.rootLogger=debug,console

            #log4j.logger.namespace_value=...
            log4j.logger.test = debug,console
            ```

### 1.4.2 使用mybatis工具类

-   简化获取SqlSession对象的方法,通过在工具类中定义单例SqlSessionFactory对象获取SqlSession

-   ```java
    public class MyBatisUtil{
    	//私有静态对象
        private static SqlSessionFactory factory;

        //获取session对象
        public static SqlSession getSession() {
            try {
                if (factory == null) {
                    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
                    factory = new SqlSessionFactoryBuilder().build(inputStream);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
            return factory.openSession();
        }
    }	
    ```

#### 修改Dao接口的实现类

-   由于SqlSession对象必须关闭,所以在dao类的方法中添加finally语句加入session.close();此时无需catch代码块

-   ```java
        public void insertStudent(Student sutdent){
            try {
                //1.使用工具类获取SqlSession对象
                session = MyBatisUtil.getSession();
                //2.通过SqlSession操作数据
                session.insert("insertStudent", student);
                //3.提交SqlSession
                session.commit();
            } finally {
                //4.关闭SqlSession
                if (session != null) {
                    session.close();
                }
            }
        } 
  ```



### 1.4.3 从属性文件中获取dbcp连接四要素

-   从properties文件中获取dbcp连接四要素

-   在源目录下定义jdbc.properties

-   ```properties
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql:///student
    jdbc.username=root
    jdbc.password=admin
    ```

-   修改主配置文件,**通过修改properties标签的resource属性定位属性文**件

    -   这些属性都是可外部配置且可动态替换的

-   ```xml
    //注册属性文件
    <properties resource="jdbc.properties">
      <property name="username" value="dev_user"/>
      <property name="password" value="F2Fa3!33TYyg"/>
    </properties>

    ...

    <dataSource type="POOLED">
      <property name="driver" value="${jdbc.driver}"/>
      <property name="url" value="${jdbc.url}"/>
      <property name="username" value="${jdbc.username}"/>
      <property name="password" value="${jdbc.password}"/>
    </dataSource>
    ```

-   添加多个映射文件

    -   每个映射文件的namespace属性不同,用于区分不同mapper的同名的操作

    -   ```xml
        <mappers>
          <mapper resource="com/sqm/dao/mapper.xml"/>
          <mapper resource="com/sqm/dao/mapper2.xml"/>
        </mappers>
        ```

    -   ```xml
        <mapper namespace="test">
          <insert id="insertStudent"></insert...>
        </mapper>
        <mapper namespace="reyco">
            <insert id="insertStudent"></insert...>
        </mapper>
        ```

-   修改dao实现类

    -   dao实现时session操作的方法可指定声明参数格式为 ` "命名空间.方法"`的参数进行操作

        -   ```java
            session.insert("test.insertStudent",student);
            ```

## 1.5 主配置文件详解

-   主配置文件名可以随意命名，其主要完成以下几个功能：
    -   注册存放 DB 连接四要素的属性文件(properties标签)	
    -   注册实体类的全限定性类名的别名(typeAliases标签)
    -   配置 MyBatis 运行环境，即数据源与事务管理器(environments标签)
    -   注册映射文件(mappers标签)
-   主配置文件的顶层结构如下(**有先后顺序** ):
    -   configuration 配置
    -   properties 属性
    -   settings 设置
    -   typeAliases 类型别名
    -   typeHandlers 类型处理器
    -   objectFactory 对象工厂
    -   plugins 插件
    -   environments 环境
        -   environment 环境变量
            -   transactionManager 事务管理器
            -   dataSource 数据源
    -   databaseIdProvider 数据库厂商标识
    -   mappers 映射器

#### 通过properties标签设置属性文件

-   通常用来注册jdbc四要素的属性文件,resource属性值即为属性文件的路径

-   ```xml
     <properties resource="jdbc.properties"/>
     ```
    ​```

#### 通过typeAliases标签指定别名

-   一般项目中的路径层次很多,可以通过指定**特定类或包**别名的方式来简化配置文件

-   package标签:标签的name属性指定要简化的包,此包下的所有类的类名都被简化

    -   ```xml
            <typeAliases>
                <package name="com.sqm.pojo"/>
            </typeAliases>
      ```

-   typealias标签:单独指定某个类名的别名,type属性指定要简化类名的类,alias属性指定简化后的别名

    -   ```xml
            <typeAliases>   
                <typeAlias type="com.sqm.util.MyBatisUtil" alias="MyBatisUtil"/>
                <typeAlias type="com.sqm.dao.impl.StudentDaoImpl" alias="StudentDao"/>
            </typeAliases>
      ```

    -   注意:package标签和typealias标签不能同时存在

-   mybatis中所有基本类型的别名为**基本类型前加下划线**:例如:int别名为`_int`,boolean别名为` _boolean`

-   mybatis中所有包装类的别名为包装类名的**小写形式**:例如:String别名为`string`,Object别名为`object`,Date别名为`date`,HashMap别名为`hashmap`

#### 通过environments标签配置mybatis运行环境

-   environments标签可包含多个environment子标签,每个environment标签代表了一个运行环境

    -   environments的default属性指定了当前mybaits运行的环境
        -   environment的id属性代表了不同环境名称,作为environments的default值出现

-   environment的子标签:

-   **transactionManager**标签指定mybatis的**事务处理器**

    -   MyBatis 支持两种事务管理器类型：JDBC 与 MANAGED。

        -   JDBC：使用 JDBC 的事务管理机制。即通过Connection 的 commit()方法提交，通过rollback()方法回滚。但默认情况下，MyBatis将自动提交功能关闭了，改为了**手动提交**,即程序中需要显式的对事务进行提交或回滚。
        -   MANAGED：由**容器**来管理事务的整个生命周期（如 Spring 容器）

    -   ```xml
               <transactionManager type="JDBC">
                   <property name="" value=""/>
               </transactionManager>
         ```
        ​```

-   **dataSource**标签配置mybatis使用的数据源类型与数据库连接的基本属性

    -   type属性指定使用的数据源类型常见有类型有：UNPOOLED、POOLED、JDNI

        -   UNPOOLED ：不使用连接池。即每次请求，都会为其创建一个DB 连接，使用完毕后，会马上将此连接关闭。
        -   POOLED：使用数据库连接池来维护连接。
        -   JNDI：数据源可以定义到应用的外部，通过JNDI 容器获取数据库连接。

    -   property子标签指定jdbc的连接属性,即可以直接指定,也可以通过properties中指定的属性源中读取数据

    -   ```xml
               <dataSource type="POOLED">
                   <property name="driver" value="${jdbc.driver}"/>
                   <property name="url" value="${jdbc.url}"/>
                   <property name="username" value="${jdbc.username}"/>
                   <property name="password" value="${jdbc.password}"/>
               </dataSource>
         ```
        ​```

#### 通过mappers标签指定映射文件

-   mappers的**子标签mapper**指定所用的映射文件路径

    -   通过resource属性指定:在项目中的相对路径

    -   ```xml
        <mappers>
                <mapper resource="mapper/StudentDaoMapper.xml"/>
        </mappers>
        ```

    -   通过url属性指定:可以通过本地文件目录或网络的url读取映射文件(此方法不常用)

    -   ```xml
               <mapper url="F:/Git/MybatisTest/src/main/resources/mapperStudentDaoMapper.xml"/>
         		<mapper url="http://www.xxx.com/resources/mapper.xml"/>
         ```
        ​```

    -   通过class属性指定:直接指定要映射的类的接口的全类名

        -   映射文件名要与接口名称相同
        -   映射文件要与接口在同一个包内
        -   映射文件中的mapper标签的namespace属性值为接口的全类名

    -   ```xml
            <mappers>
                <mapper class="com.sqm.dao.StudentDao"/>
            </mappers>
      ```

-   当映射文件较多时可用**package标签**指定映射文件存放的包,package的name属性指定包的全名

    -   映射的类**要使用mapper动态代理实现**

    -   映射文件名要与接口名相同

    -   映射文件要与接口在同一个包内

    -   mapper标签的namespace属性值为接口的全类名

    -   ```xml
            <mappers>
                <mapper package="com.sqm.dao."/>
            </mappers>
      ```

-   后面两种通过类或包指定mapper.xml的方式,在maven中不适用

## 1.6 API 详解

### 1.6.1 Resources类

-   mybatis的资源类,用于读取资源文件,通过不同的方法读取并解析配置文件,返回**不同类型的IO流对象**
-   getResourceAsFile:以文件形式读取
-   getResourceAsProperties:以properties形式读取
-   getResourceAsReader:以字符流读取
-   getResourceAsStream:以字节流读取
-   getResourceURL:读取url路径例如网络的资源文件
    -   所有方法参数可以为(String resource):resource文件的路径
        -   (ClassLoader loader,String resource):指定读取的类加载器

### 1.6.2 SqlSessionFactoryBuilder类

-   仅用于创建工厂对象,方法build()用完即可即可销毁,build()方法重载多,根据获取的资源文件的对象类型不同而重载
    -   build的第一个参数为获取的配置文件,第二个参数一般为资源文件要使用的环境environment的id值,若不指定则使用default的环境

### 1.6.3 SqlSessionFactory接口

-   此接口创建的工厂对象为一个**重量级对象**(**资源消耗较大**),线程安全,仅仅需要一个工厂对象即可,使用openSession()方法创建一个sql会话对象
    -   openSession(boolean flag):参数若不设,默认为false,即需手动commit()的对象;参数若为true,则自动提交语句

### 1.6.4 SqlSession接口

-   用于操作持久化内容,一个sqlSession对应一次**数据库会话**,sqlsession创建则会话开始,sqlsession关闭则会话结束
-   sqlsession接口对象是**线程不安全的**,所以每一次操作必须执行close(),被关闭的前提是已经提交;若没有提交要关闭,则会执行回滚操作,若被提交则直接关闭会话
-   SqlSession接口中的CURD会话操作语句:
    -   **insert(String statement,[Object parameter])**:执行插入sql语句,**第一个参数statement,要使用的语句在mapper中的的id值**,**第二个参数为传递给statement的对象**,insert中是被插入的对象,可以不指定
    -   **delete(String statement,[Object parameter])**:执行删除sql语句,parameter是传递给statement的对象,delete指的是被删除的对象
    -   **update(String statement,[Object parameter])**:执行修改sql语句.第二个参数在update语句中指的是要更新的的对象
    -   insert,delete,update的**返回值都是int,**表示被sql语句影响的数据有几行
    -   **select(String statement,[Object parameter],[RowBounds bounds], ResultHandler handler)**:执行选择sql语句,**ResultHandler对象是对返回的结果进行操作的操作器类**;**RowBounds是限制检索索引,用于限制返回的数据区域**
    -   **selectList(String statement,[Object parameter],[RowBounds bounds])**:执行选择sql语句,**以列表集合`List<E>`方式返回**,列表中每一个元素是一行数据
    -   **selectMap(String statement, [Object parameter], [String mapKey], [RowBounds rowBounds])**:执行选择sql语句,**返回值为`Map<K,V>`**,**第三个参数mapKey为该属性作用列表中每个值的键,通常是主键id**
    -   **selectOne(String statement,[Object paramenter])**:执行选择sql语句,返回值为选择的对象类
-   SqlSession中的其他语句:
    -   commit():提交语句,无参形式;参数可为boolean形式,表示是否强制提交
    -   rollback([boolean force]):回滚语句
    -   close():关闭会话

### 1.6.5 源码分析

-   由源码可知,在资源流使用后,SqlSessionFactoryBuilder对象的build()方法会把流关闭,所以无需手动关闭inputStream
-   SqlSessionFactory中无参的openSession()方法,将事务transaction的自动提交变为false,关闭了自动提交
-   SqlSession的insert(),delete(),update()的方法,其底层均是调用执行了 **update()方法**
    -   且update()方法中均把**dirty变量设置为true**,开启了脏读,即多个事务可以同时修改同一个数据
    -   commit()方法提交修改后又将dirty变量设置为false
-   SqlSession中无需过多显式回滚