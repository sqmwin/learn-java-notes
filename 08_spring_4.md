[TOC]

---

# 4. Spring与Dao

-   一部分是JDBC模板的使用,是Spring 所使用的操作数据库的技术之一； 另一部分则为 Spring 对于事务的管理
-   Spring 与 Dao 部分，是Spring 的两大核心技术 IoC 与 AOP 的典型应用体现
    -   对于 JDBC 模板的使用，是 IoC 的应用，是将 JDBC 模板对象注入给了Dao 层的实现类
    -   对于 Spring 的事务管理，是 AOP 的应用，将事务作为切面织入到了 Service 层的业务方法中

## 4.1 Spring与JDBC模板

-   **JdbcTemplate**:
    -   为了避免直接使用 JDBC 而带来的复杂且冗长的代码，Spring 提供了一个强有力的模板类---JdbcTemplate来简化 JDBC操作
    -   **数据源 DataSource对象**与**模板 JdbcTemplate 对象**均可通过 **Bean** 的形式定义在配置文件中，充分发挥了依赖注入的威力

### 4.1.1 导 入jar包

1.  Spring的基本包与数据库驱动jar包(mysql-connector-java.5.1.45.jar)

2.  Spring的JDBC包

    -   spring-jdbc-x.x.x.RELEASE.jar

3.  Spring的事务包Spring Transaction

    -   spring-tx-x.x.x.RELEASE.jar

4.  maven中的pom配置

    -   ```xml
        spirng...

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.45</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        ```

### 4.1.2 搭建测试环境

1.  **定义实体类 User**

  ```java
  public class User {
      private Integer id;
      private String username;
      private int age;
      //Contructor
      //getter and setter
      //toString()
  }
  ```

2.  **定义数据库及表**

  ```mysql
  CREATE TABLE user(
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(30)NOT NULL ,
    age INT 
  )
  ```

3.  **定义 UserDao**

  ```java
  public interface UserDao {
      void insertUser(User user);

      void deleteUser(int id);

      void updateUser(User user);

      String selectUsernameById(int id);

      List<String> selectUsernamesByAge(int age);

      User selectUserById(int id);

      List<User> selectAllUsers();
  }
  ```

4.  **初步定义 UserDaoImpl**

  -   只给出空实现,之后由JdbcTemplate实现

  ``` java
  public class UserDaoImpl implements UserDao {
      public void insertUser(User user) {}

      public void deleteUser(int id) {}

      public void updateUser(User user) {}

      public String selectUsernameById(int id) {return null;}

      public List<String> selectUsernamesByAge(int age) {return null;}

      public User selectUserById(int id) {return null;}

      public List<User> selectAllUsers() {return null;}
  }
  ```

5.  **定义 UserService**

  ```java
  public interface UserService {
      void addUser(User user);

      void removeUser(int id);

      void moodifyUser(User user);

      String findUsernameById(int id);

      List<String> findUsernamesById(int id);

      User findUserById(int id);

      List<User> findAllUsers();
  }
  ```

6.  **定义 UserServiceImpl**

  ```java
  public class UserServiceImpl implements UserService {
      public void addUser(User user) {}

      public void removeUser(int id) {}

      public void moodifyUser(User user) {}

      public String findUsernameById(int id) {return null;}

      public List<String> findUsernamesById(int id) {return null;}

      public User findUserById(int id) {return null;}

      public List<User> findAllUsers() {return null;}
  }
  ```

7.  **定义测试类 MyTest**

  ```java
  public class JdbcTemplateTest {
      private ApplicationContext context;
      private UserService service;

      @Before
      public void setUp() {
          context = new ClassPathXmlApplicationContext("context/spring-jdbc-template-context.xml");
          service = (UserService) context.getBean("myService");
      }

  ```

### 4.1.3 数据源的配置

-   使用 JDBC 模板，首先需要配置好数据源，**数据源**直接以**Bean** 的形式配置在 Spring 配置文件中
    -   数据源的不同，其配置方式不同
    -   三种常用的数据源:
        -   Spring默认数据源
        -   DBCP数据源
        -   C3P0数据源

#### Spring 默认的数据源 DriverManagerDataSource

-   **DriverManagerDataSource**

    -   全类名:org.springframework.jdbc.datasource.DriverManagerDataSource
    -   属性**driverClassName**:用于接收db驱动
    -   DriverManagerDataSource 类继承自 AbstractDriverBasedDataSource。其有**三个属性**用于接收连接数据库的**URL,用户名与密码**,属性分别是:**url,username,password**

-   在spring配置文件中配置bean:

    -   ```xml
            <!--Spring 默认的数据源 DriverManagerDataSource-->
          <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource" id="driverManagerDataSource">
                <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///test"/>
                <property name="username" value="root"/>
                <property name="password" value="admin"/>
            </bean>
      ```

#### DBCP数据源BasicDataSource

-   DataBase Connection Pool:

    -   导入dbcp包,pool为依赖包

    -   commons-dbcp-x.x.x.jar

        -   commons-pool-x.x.x.jar

    -   maven中pom.xml设置:

        -   ```xml
            <dependency>
                <groupId>commons-dbcp</groupId>
                <artifactId>commons-dbcp</artifactId>
                <version>1.4</version>
            </dependency>
            ```

-   DBCP数据源:BasicDataSource类

    -   全类名:org.apache.commons.dbcp.BasicDataSource
    -   四个属性:**driverClassname,url,username,password**

-   spring中配置bean:

    -   ```xml
            <!--DBCP数据源BasicDataSource-->
          <bean class="org.apache.commons.dbcp.BasicDataSource" id="basicDataSource">
                <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///test"/>
                <property name="username" value="root"/>
                <property name="password" value="admin"/>
            </bean>
      ```

#### C3P0 数据源ComboPooledDataSource

-   导入C3P0 jar包

    -   c3p0.jar

    -   maven:

        -   ```xml
            <dependency>
                <groupId>com.mchange</groupId>
                <artifactId>c3p0</artifactId>
                <version>0.9.5.2</version>
            </dependency>
            ```

-   C3P0 数据源是ComboPooledDataSource类

    -   全类名:com.mchange.v2.c3p0.ComboPooledDataSource
    -   四个属性:**driverClass、jdbcUrl、user、password**

-   spring中配置bean:

    -   ```xml
            <!--C3P0数据源:ComboPooledDataSource-->
            <bean class="com.mchange.v2.c3p0.ComboPooledDataSource" id="comboPooledDataSource">
                <property name="driverClass" value="com.mysql.jdbc.Driver"/>
                <property name="jdbcUrl" value="jdbc:mysql://test"/>
                <property name="user" value="root"/>
                <property name="password" value="admin"/>
            </bean>
      ```


### 4.1.4 从属性文件读取数据库连接信息

-   为了便于维护，可以将数据库连接信息写入到属性文件中，使Spring 配置文件从中读取数据

-   ```properties
    mysql.driver=com.mysql.jdbc.Driver
    test.url=jdbc:mysql:///test
    mysql.username=root
    mysql.password=admin
    ```

-   Spring 配置文件从属性文件中读取数据时，需要在\<property/>的value 属性中使用${xxx}， 将在属性文件中定义的 key 括起来，以引用指定属性的值

    -   如`<property name="driverClass" value="${mysql.driver}" `

    -   该属性文件若要被Spring 配置文件读取，其必须在配置文件中进行注册。注册方式有两种

        1.  \<bean/>方式

            -   使用  PropertyPlaceholderConfigurer类的bean实例方式进行注册,该类的属性location用于指定引入的properties文件的位置,这种方式不常用

            -   ```xml
                    <!--注册属性文件bean方式,不常用-->
                    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
              ```

        2.  \<context:property-placeholder/>方式

            -   在Spring配置文件头部加入context的约束

            -   在\<context:property-placeholder/>标签中的**属性location**用于指定引入的properties文件的位置

            -   ```xml
                    <!--注册属性文件方式二,context:property-placeholder-->
                    <context:property-placeholder location="jdbc.properties"/>
                    <bean class="com.sqm.springdao.service.impl.UserServiceImpl" id="userServiceTarget"/>
              ```

### 4.1.5 配置JDBC模板

-   JDBC 模板类 **JdbcTemplate** 从其父类 **JdbcAccessor** 继承了一个**属性dataSource**，用于**接收数据源**

-   在spring中注册jdbcTemplate的bean

    -   ```xml
            <!--JdbcTemplate-->
            <bean class="org.springframework.jdbc.core.JdbcTemplate">
                <property name="dataSource" ref="comboPooledDataSource"/>
            </bean>
      ```

### 4.1.6 Dao实现类继承JdbcDaoSupport类

-   JdbcDaoSupport 类中有一个**属性 JdbcTemplate**，用于**接收 JDBC 模板**

-   当Dao实现类继承JdbcDaoSupport类后,就具有了jdbc模板属性

-   在配置文件中注册模板对象bean

    -   ```xml
            <!--注册dao的bean,继承自JdbcDaoSupport,可载入JdbcTemplate-->
            <bean class="com.sqm.springdao.dao.impl.UserDaoImpl" id="userServiceTarget">
                <property name="jdbcTemplate" ref="jdbcTemplate"/>
            </bean>
      ```

-   仔细查看 JdbcDaoSupport 类，发现其有一个 dataSource 属性，查看 setDataSource()方法体可知，若 JDBC 模板为null，则会**自动创建一个模板对象**

    -   在 Spring 配置文件中，对于 JDBC模板对象的配置完全可以**省去**，而是**在 Dao 实现类中直接注入数据源对象**。这样会让系统**自动**创建 JDBC 模板对象

-   dao连接数据库的流程:

-   ```mermaid
    graph TD;
    	1(数据库)--mysql-connector-->2(jdbc.properties)
    	2--通过context:property-placeholder-->3(3种DataSource)
    	3-.jdbc模板的dataSource属性.->4(jdbcTemplate)
    	4-.Dao类的jdbcTemplate属性.->5(Dao实体类继承JdbcDaoSupport)
    	3--Dao类的dataSource属性,获取数据源与自动获取模板-->5
    ```

### 4.1.7 对db的增删改操作

-   **JdbcTemplate 类**中提供了对 DB进行**修改、查询**的方法

-   Dao 实现类使用继承自JdbcDaoSupport的 **getTemplate()方法**，可以获取到**JDBC 模板**对象

-   对 DB 的**增、删、改**都是通过 **update()方法**实现的。该方法常用的重载方法有两个

    -   ```java
        public int update ( String sql)
        ```

    -   ```java
        public int update ( String sql, Object… args)
        ```

    -   sql - 要执行的sql操作语句

    -   args - sql语句中的所有动态参数

    -   返回值为影响的数据库条数,一般不用

-   修改UserDao实现类

-   ```java
    public class UserDaoImpl extends JdbcDaoSupport implements UserDao {    public 		void insertUser(User user) {
            String insert = "INSERT INTO user(username,age) VALUES (? ,?)";
            this.getJdbcTemplate().update(insert,user.getUsername(),user.getAge());
        }

        public void deleteUser(int id) {
            String delete = "DELETE FROM user WHERE id = ?";
            this.getJdbcTemplate().update(delete, id);
        }

        public void updateUser(User user) {
            String update = "UPDATE user SET username=?,age=? WHERE id = ?";
            this.getJdbcTemplate().update(update,user.getUsername(),user.getAge(),user.getId());
        }
    ```

### 4.1.8 对db的查询操作

-   JdbcTemplate的查询结果均是以对象的形式返回;根据返回对象类型的不同，将查询分为两类：简单对象查询，与自定义对象查询
-   简单对象查询：查询结果为String、Integer等**简单对象类型**，或**该类型为元素的集合类型**，如 **List\<String>**等
-   定义对象查询：查询结果为**自定义类型**，如User 等，或**该类型为元素的集合类型**， 如 **List\<User>**等

1.  简单对象查询

    -   常用的简单对象查询方法有：查询结果为**单个对象**的 **queryForObject()**与查询结果为**List**的**queryForList()**

    -   ```java
        pubic T queryForObject (String sql, Class<T> type, Object... args)
        pubic List<T> queryForList (String sql, Class<T> type, Object... args)
        ```

    -   sql属性和args属性与update()方法相同,**types属性**为**返回值类型的class**,如String.class

    -   修改UserDao实现类:

    -   ```java
            public String selectUsernameById(int id) {
                String select = "SELECT username FROM user WHERE id = ?";
                String username = getJdbcTemplate().queryForObject(select, String.class, id);
                return username;
            }

            public List<String> selectUsernamesByAge(int age) {
                String select = "SELECT username FROM user WHERE age = ?";
                List<String> usernames = getJdbcTemplate().queryForList(select, String.class, age);
                return usernames;
            }
      ```

2.  自定义对象查询

    -   查询结果为单个对象:queryForObject()

    -   查询结果为List:query()

    -   ```java
        pubic T queryForObject (String sql, RowMapper<T> m , Object... args)
        pubic List<T> query (String sql, RowMapper<T > m, Object... args)
        ```

    -   RowMapper属性:记录映射接口,用于将查询结果中的每一条记录包装成指定对象

        -   全类名:org.springframework.jdbc.core.RowMapper

        -   RowMapper接口中有一个方法需要实现:

        -   ```java
            public Object mapRow(ResultSet resultSet,int rowNum)
            ```

        -   resultSet  -  :表示rowNum所代表的当前行的数据的结果集,代表当前行的结果

        -   rowNum - :全部数据中当前行的行号

        -   一般，该方法体中就是实现将查询结果中**当前行的数据包装为一个指定对象**

        -   实现RowMapper\<User>接口:

        -   ```java
            public class UserRowMapper implements RowMapper<User> {
                public User mapRow(ResultSet resultSet, int i) throws SQLException {
                    User user = new User();
                    user.setId(resultSet.getInt("id"));
                    user.setUsername(resultSet.getString("username"));
                    user.setAge(resultSet.getInt("age"));
                    return user;
                }
            }
            ```

        -   修改UserDaoImpl:

        -   ```java
                public User selectUserById(int id) {
                    String select = "SELECT * FROM user WHERE id = ?";
                    User user = getJdbcTemplate().queryForObject(select, new UserRowMapper(), id);
                    return user;
                }

                public List<User> selectAllUsers() {
                    String select = "SELECT * FROM user";
                    List<User> users = getJdbcTemplate().query(select, new UserRowMapper());
                    return users;
                }
          ```

### 4.1.9 注意:Jdbc模板是多例的

-   JdbcTemplate 对象是多例的，即系统会为**每一个使用模板对象的线程（方法）**创建一个JdbcTemplate 实例，并且在该线程（方法）结束时，自动释放 JdbcTemplate 实例。所以在**每次使用 JdbcTemplate 对象时**，都需要通过 **getJdbcTemplate()**方法获取。

## 4.2 Spring的事务管理

-   事务:commit(),rollback(),close(),原本是数据库中的概念,在Dao层;但一般情况下,需要将事务提升到业务层,即Service层,这是因为需要**使用事务的特性管理具体的业务**
-   在 Spring 中对事务的管理的方式:
    1.  使用Spring的书屋代理工厂管理事务
    2.  使用Spring的事务注解管理事务
    3.  使用AspectJ的AOP配置管理事务

### 4.2.1 Spring事务管理API与回顾错误与异常

-   spring事务管理主要用到两个事务相关的接口

1.  事务管理器接口PlatformTransactionManager

    -   PlatformTransactionManager接口对象:平台事务管理器:用于完成事务提交,回滚,获取事务状态信息

    -   ```java
        TransactionStatus getTransaction(@Nullable TransactionDefinition definition)
        definition - 事务定义,可以为null,描述传播行为、隔离级别、超时等。
        void commit(TransactionStatus status)
        status - 事务状态,由getTransaction()获取
        void rollback(TransactionStatus status)
        ```

        1.  常用的两个实现类

            -   **DataSourceTransactionManager**:使用 JDBC 或 iBatis 进行持久化数据时使用
            -   **HibernateTransactionManager**:使用 Hibernate进行持久化数据时使用

        2.  Spring的回滚方式

            -   Spring 事务的默认回滚方式是：**发生运行时异常时回滚**，**发生受查异常时提交**
                -   对于受查异常，程序员也可以手工设置其回滚方式

        3.  **回顾错误与异常**

            -   ```mermaid
                graph LR;
                	1(Throwable)-->2(Error)
                	1-->3(Exception)
                	3-->RuntimeException:运行时异常
                	3-->IOException
                	3-->SQLException
                	3-->...
                	3-->自定义Exception
                ```

            -   Throwable 类是 Java 语言中所有错误或异常的超类;只有当**对象是此类(或其子类之一) 的实例**时，才能通过Java 虚拟机或者 Java 的 throw 语句抛出

                -   Error是程序在**运行**过程中出现的**无法处理的错误**,如OutOfMemoryError,ThreadDeath,NoSuchMethodError,JVM一般会**终止线程**
                -   Exception:异常是编译和运行其出现的,是jvm通知程序员的一种方式,通知程序可能出现错误,要求处理
                    -   运行时异常:RuntimeException及其子类,如NullPointerException,ArrayIndexOutOfBoundsException,IllegalArguementException;由JVM抛出,在编译器不要求必须处理(捕获或抛出);这类异常可以避免
                        -   HibernamtException属于运行时异常
                    -   受查异常:编译期异常,在代码编写时必须捕获或抛出的异常,不处理则无法通过编译,如SQLException,ClassNotFoundException,IOException
                    -   RuntimeException及其子类以外的异常均属于受查异常;用户自定义的异常也属于受查异常
                    -   在自定义异常时,只要没有明确时RuntimeException的子类,定义的就是受查异常

2.  事务定义接口TransactionDefinition

    -   定义了描述事务的三类常量:**事务隔离级别**,**事务传播行为**,事务默认**超时**时限,以及对他们的操作

    1.  定义了五个事务隔离级别常量
        -   以**ISOLATION_**开头,ISOLATION_XXX
        -   **DEFAULT**:db默认的事务隔离级别;Mysql为REPEATABLE_READ;Oracle为READ_COMMITTED
        -   READ_UNCOMMITTED:读未提交,**不解决**并发问题
        -   READ_COMMITTED:读已提交,解决**脏读**,存在不可重复读和幻读
        -   REPEATABLE_READ:可重复读,解决**脏读,不可重复读**,存在幻读
        -   SERIALIZABLE:**串行**,即非并行不存在并发问题

    2.  定义了七个**事务传播**行为常量

        -   以PROPAGATION_开头,PROPAGATION\_XXX


        -   事务传播行为:处于不同事务的方法相互调用时,执行期间的**事务维护情况**
            -   如:A事务中的方法doSome()调用B事务中的方法doOther(),在调用执行期间事务的维护情况,就称为事务传播行为,事务传播行为是**加在方法上**的
        -   **REQUIRED**：指定的**方法**必须在**事务内执行**
            -   若当前存在事务，就加入到当前事务中； 若当前没有事务，则创建一个新事务
            -   是 Spring 默认的事务传播行为,最常见
        -   SUPPORTS：指定的方法支持当前事务，但若当前**没有事务**，也可以以非事务方式**执行**
        -   MANDATORY：指定的方法必须在当前事务内执行，若当前**没有事务**，则直接**抛出异常**
        -   REQUIRES_NEW：**总是新建一个事务**，若当前**存在事务**，就将当前事务**挂起**，直到新建的事务执行完毕
        -   NOT_SUPPORTED：指定的方法**不能在事务环境中执行**，若当前**存在事务**，就将当前**事务挂起**
        -   NEVER：指定的方法**不能在事务环境下执行**，若当前**存在事务**，就直接**抛出异常**
        -   NESTED：指定的方法**必须在事务内执行**。若当前**存在事务**，则在**嵌套事务内执行**；若当前**没有事务**，则创建一个**新事务**
    
    3.  定义了默认事务超时时限
        -   常量 **TIMEOUT_DEFAULT** 定义了事务底层默认的超时时限，及**不支持事务超时时限设置**的**none** 值
        -   注意，事务的超时时限起作用的条件比较多，且超时的时间计算点较复杂。所以，该值一般就**使用默认值**即可

### 4.2.2. 程序举例环境搭建

-   购买股票—transaction_buystock 项目
-   模拟购买股票:存在两个实体：银行账户 Account与股票账户 Stock。当要购买股票时，需要从 Account中扣除相应金额的存款，然后在 Stock中增加相应的股票数量。而在这个过程中，可能会抛出一个用户自定义的异常。异常的抛出，将会使两个操作回滚

1.  创建数据库表

    ```mysql
    CREATE TABLE account (
      aid INT PRIMARY KEY AUTO_INCREMENT,
      aname VARCHAR(30) NOT NULL ,
      balance INT NOT NULL 
    )
    CREATE TABLE stock (
      sid INT PRIMARY KEY AUTO_INCREMENT,
      sname VARCHAR(30) NOT NULL ,
      count INT NOT NULL
    )
    ```

2.  创建实体类

    ```java
    public class Account {
        private int aid;
        private String aname;
        private double balance;
      
    public class Stock {
        private int sid;
        private String sname;
        private int count;
    ```

3.  定义dao接口

    ```java
    public interface AcountDao {
        void insertAccount(String aname, double balance);

        void updateAccount(String aname, double balance);

        Account selectAccount(String aname);
    }
    public interface StockDao {
        void insertStock(String sname, int count);

        void updateStock(String sname, int count);

        Stock selectStock(String sname);
    }
    ```

4.  定义dao实现类

    -   账户余额在减少,股票在增加

    ```java
    public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {
        //新建一个账户
        public void insertAccount(String aname, double balance) {
            String insert = "INSERT INTO account(aname,balance) VALUES (? ,?)";
            getJdbcTemplate().update(insert, aname, balance);
        }

        //消费账户余额
        public void updateAccount(String aname, double balance) {
            String update = "UPDATE account SET balance = balance-  ? WHERE aname = ?";
            getJdbcTemplate().update(update, balance, aname);
        }

        //选择指定账户
        public Account selectAccount(String aname) {
            String select = " SELECT * FROM account WHERE aname =?";
            Account account = getJdbcTemplate().queryForObject(select, new AccountRowMapper(), aname);
            return account;
        }
    }

    public class StockDaoImpl extends JdbcDaoSupport implements StockDao {
        public void insertStock(String sname, int count) {
            String insert = "INSERT INTO stock(sname,count) VALUES(?,?)";
            getJdbcTemplate().update(insert, sname, count);
        }

        //买入的股票
        public void updateStock(String sname, int count) {
            String update = "UPDATE stock SET count=count + ? WHERE sname=?";
            getJdbcTemplate().update(update, count, sname);
        }

        public Stock selectStock(String sname) {
            String select = "SELECT * FROM stock WHERE sname = ?";
            Stock stock = getJdbcTemplate().queryForObject(select, new StockRowMapper(), sname);
            return stock;
        }
    }
    ```

5.  定义异常类

    ```java
    public class StockException extends Exception {
        public StockException() {
            super();
        }

        public StockException(String message) {
            super(message);
        }
    }

    ```

6.  定义Service接口

    -   ```java
        public interface StockProcessService {
            void openAccount(String aname, double money);

            void openStock(String sname, int count);

            //aname银行账户花money原购买sname的股票amount股;抛出异常
            void byStock(String aname, String sname, double money, int amount) throws StockException;

            Account findAccount(String aname);

            Stock findStock(String sname);
        }
        ```

7.  定义Service的实现类

    -   账户购买股票时抛出异常(1=1)必抛出异常,则账户扣款但股票未买

    -   ```java
        public class StockProcessServiceImpl implements StockProcessService {
            private AccountDao accountDao;
            private StockDao stockDao;

            public void setAccountDao(AccountDao accountDao) {
                this.accountDao = accountDao;
            }

            public void setStockDao(StockDao stockDao) {
                this.stockDao = stockDao;
            }

            public void openAccount(String aname, double money) {
                accountDao.insertAccount(aname, money);
            }

            public void openStock(String sname, int count) {
                stockDao.insertStock(sname, count);
            }

            public void byStock(String aname, String sname, double money, int amount) throws StockException {
                accountDao.updateAccount(aname, money);
                if (1 == 1) {
                    throw new StockException();
                }
                stockDao.updateStock(sname,amount);
            }

            public Account findAccount(String aname) {
                return accountDao.selectAccount(aname);
            }

            public Stock findStock(String sname) {
                return stockDao.selectStock(sname);
            }
        }
        ```

8.  Spring配置文件中添加最全约束

    -   将使用到 Spring 中 DI、AOP、事务等众多功能，所以将之前用过的所有约束进行了综合。综合后的约束为

    -   ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:aop="http://www.springframework.org/schema/aop" xmlns:context="http://www.springframework.org/schema/aop"
               xmlns:tx="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
               xmlns:c="http://www.springframework.org/schema/c"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        </beans>
        ```

9.  修改Spring配置文件内容

    ```xml
        <!--引入jdbc配置文件-->
        <context:property-placeholder location="jdbc.properties"/>
        <!--配置数据源-c3p0-->
        <bean class="com.mchange.v2.c3p0.ComboPooledDataSource" id="comboPooledDataSource">
            <property name="driverClass" value="${mysql.driver}"/>
            <property name="jdbcUrl" value="${test.url}"/>
            <property name="user" value="${mysql.username}"/>
            <property name="password" value="${mysql.password}"/>
        </bean>

        <!--配置dao-->
        <bean class="com.sqm.transaction.dao.impl.AccountDaoImpl" id="accountDao">
            <property name="dataSource" ref="comboPooledDataSource"/>
        </bean>
        <bean class="com.sqm.transaction.dao.impl.StockDaoImpl" id="stockDao">
            <property name="dataSource" ref="comboPooledDataSource"/>
        </bean>

        <!--配置service-->
        <bean class="com.sqm.transaction.service.impl.StockProcessServiceImpl" id="stockProcessService"
              p:accountDao-ref="accountDao" p:stockDao-ref="stockDao"/>
    ```

    ```mermaid
    graph LR;
    	1(数据库) --> 2(jdbc)
    	2 --> 3(c3p0数据源)
    	3 --dataSource-ref--> 4(AccountDaoImpl)
    	3 --dataSource-ref--> 5(StockDaoImpl)
    	4 --ref--> 6(StockPorcessService)
    	5 --ref--> 6
    ```

    ​

10.  定义测试类

  -    view 层测试类 MyTest。现在就可以在无事务代理的情况下运行了

  ```java
  public class TransactionTest {
      private ApplicationContext context;
      private StockProcessService service;

      @Before
      public void setUp() {
          context = new ClassPathXmlApplicationContext("context/spring-transaction-context.xml");
          service = (StockProcessService) context.getBean("stockProcessService");
      }

      //开账户与股票账户
      @Test
      public void init() {
          service.openAccount("sqm", 10000);
          service.openStock("sqmStock", 100);
      }

      //买股票,500元买100股
      @Test
      public void buyStock() throws StockException {
          service.buyStock("sqm", "sqmStock", 500, 100);
      }
  }
  ```

### 4.2.3 使用Spring的事务代理工厂管理事务

-   Service的实现类在之前是以非事务方式执行的;需要使用事务代理来管理事务,让ServiceImpl在事务环境下运行
-   事务代理使用的类为:**TransactionProxyFactoryBean**
    -   事务代理工厂bean,用于创建事务代理bean
    -   全类名:
    -   该bean需要初始化下列三个属性
        -   **transactionManager**:事务管理器
        -   **target**:目标对象,要使用事务代理的对象
        -   **transactionAttributes**:事务属性
-   在用xml配置事务代理时,受查异常的回滚由程序员设置
    -   **"-异常"**:使发生指定异常的事务进行**回滚**
    -   **"+异常"**:使发生指定异常的事务进行**提交**

1.  复制项目:上一个项目继续使用

2.  导入jar包:使用aop,导入aop-alliance与spring-aop两个jar包

3.  在容器中添加事务管理器 DataSourceTransactionManager

    -   未使用Hibernate事务管理器

    -   ```xml
            <!--配置事务管理器-->
            <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
                <property name="dataSource" ref="comboPooledDataSource"/>
            </bean>
      ```

4.  在容器中添加事务代理 TransactionProxyFactoryBean

    -   prop中的key指定要用事务的方法,prop的值为事务定义的属性
    -   -StockException:发生StockException时,回滚事务
    -   readOnly:对数据库的操作为只读


    -   ```xml
            <!--配置事务代理bean,由事务代理工厂创建-->
            <bean class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean"
                  id="stockProcessServiceProxy">
                <property name="transactionManager" ref="transactionManager"/>
                <property name="target" ref="stockProcessService"/>
                <property name="transactionAttributes">
                    <props>
                        <prop key="open*">PROPAGATION_REQUIRED</prop>
                        <prop key="find*">PROPAGATION_SUPPORTS,readOnly</prop>
                        <prop key="buyStock">PROPAGATION_REQUIRED,-StockException</prop>
                    </props>
                </property>
            </bean>
        ```

5.  修改测试类

    -   通过事务代理运行

    -   ```java
               service = (StockProcessService) context.getBean("stockProcessServiceProxy");
         ```
        ​```

### 4.2.4 使用Spring的事务注解管理事务

-   通过**@Transactional**注解方式将事务织入到要用事务的方法

-   使用注解方式需在配置文件中加入**\<tx:annotation-driven>标签**,事务注解驱动,指定**transaction-manager属性**指定要用的事务管理器

-   ```xml
        <!--配置事务注解驱动-->
        <tx:annotation-driven transaction-manager="transactionManager"/>
  ```

-   @Transaction的所有可选属性如下:

    -   **propagation**:事务传播属性:属性类型为**Propagation枚举**,默认值为:Propagation.REQUIRED
    -   **isolation**:事物的隔离级别:属性类型为**Isolation枚举**,默认值为:Isolation.DEFAULT
    -   **readOnly**:s该方法对数据库操作是否可读,属性类型为boolean,默认为false
    -   **timeout**:该方法与数据库连接时的超时时限,单位为秒,类型为int,默认值为-1,无时限
    -   **rollbackFor**:指定需要回滚的异常类:属性类型为Class[],默认为空数组,当只有一个异常类时不使用数组
    -   **rollbackForCalssName**:指定需要回滚的异常类名:类型为String[],其余同上
    -   **noRollbackFor**:指定不需要回滚的异常类:类型为Class[]
    -   **noRollbackForClassName**:同上,类型为String][]

-   @Transaction用在**方法**上的话,只能用在**public方法**上

-   @Transaction用在**类**上,指定类中的**所有方法**都织入事务

-   测试@Transaction注解事务

    1.  复制项目:使用之前的项目

    2.  在容器中添加事务管理器:使用之前的事务管理器;配置事务注解驱动

        ```xml
            <!--配置事务注解驱动-->
            <tx:annotation-driven transaction-manager="transactionManager"/>
        ```

    3.  在 Service实现类方法上添加注解

        ```java
            @Override
            @Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.SERIALIZABLE,rollbackFor = SocketException.class)
            public void buyStock(String aname, String sname, double money, int amount) throws StockException {
                accountDao.updateAccount(aname, money);
                if (1 == 1) {
                    throw new StockException();
                }
                stockDao.updateStock(sname,amount);
            }
        	@Override
            @Transactional(propagation = Propagation.SUPPORTS,readOnly = true)
            public Account findAccount(String aname) {
                return accountDao.selectAccount(aname);
            }
        ```

    4.  修改测试类

        ```java
                service = (StockProcessService) context.getBean("stockProcessService");
        ```

### 4.2.5 使用 AspectJ的AOP 配置管理事务（重点)

-   使用Spring配置文件配置事务的不足是,要针对每个目标类或每个目标方法都要配置事务代理,目标类较多时配置文件会比较臃肿

-   使用xml配置顾问方式可以自动为每个**符合切入点表达式**的类生成事务代理

-   测试使用AspectJ的AOP配置管理事务:

    1.  复制项目:使用之前的项目

    2.  导入jar包:aspectj的包(weaver,tools(可选),aspectjrt(可选)与spring与aspectj的整合包

    3.  在容器中添加事务管理器:使用之前的JDBC数据源事务管理器

    4.  配置事务通知

        -   \<tx:advice/>标签:事务通知:


        -   为事务通知设置相关属性:指定要将事务**以什么方式**织入给**哪些方法**

        -   \<tx:method>标签为使用事物的方法的定义,name值为方法名

        -   rollback-for的值为异常的类名,un-rollback-for相同

        -   ```xml
                <!--配置事务通知-->
                <tx:advice transaction-manager="transactionManager" id="transactionInterceptor">
                    <tx:attributes>
                        <tx:method name="open*" propagation="REQUIRED" isolation="SERIALIZABLE"/>
                        <tx:method name="find*" propagation="SUPPORTS" read-only="true" isolation="READ_COMMITTED"/>
                        <tx:method name="buyStock" propagation="REQUIRED" rollback-for="StockException" isolation="SERIALIZABLE"/>
                    </tx:attributes>
                </tx:advice>
            ```
    
    5.  配置AOP顾问
    
        -   将tx:advice中的事务通知指定给织入点pointcut,织入点由execution表达式表示
    
        -   切入点表达式一定要指定切入点在要使用事务通知的包(server层),否则会把dao层也当做切入点二循环调用,出现异常
    
        -   ```xml
                <!--配置事务通知-->
                <tx:advice transaction-manager="transactionManager" id="transactionInterceptor">
                    <tx:attributes>
                        <tx:method name="open*" isolation="SERIALIZABLE"/>
                        <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
                        <tx:method name="buyStock" rollback-for="StockException" isolation="SERIALIZABLE"/>
                    </tx:attributes>
                </tx:advice>
    
                <!--配置事务顾问-->
                <aop:config>
                    <aop:pointcut id="pointcut" expression="execution(* com.sqm.transaction.service.impl.*.*(..))"/>
                    <aop:advisor advice-ref="transactionInterceptor" pointcut-ref="pointcut"/>
                </aop:config>
            ```
    
    6.  修改测试类:测试类的bean仍然为目标类而不是代理类