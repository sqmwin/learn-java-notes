[TOC]

# 4. 查询缓存

-   查询缓存的使用，主要是为了**提高查询访问速度**。将用户对同一数据的重复查询过程简化，**不再每次均从数据库查询获取结果数据**，从而提高访问速度

## 4.1 一级查询缓存

-   MyBatis 一级查询缓存是基于org.apache.ibatis.cache.impl.PerpetualCache类的 HashMap本地缓存，其**作用域是 SqlSession**

-   同一个 SqlSession中多次次执行相同的 sql查询语句，第一次执行完毕后，会将查询结果写入到缓存中，之后会**从缓存中直接获取数据**，而不再到数据库中进行查询，从而提高查询效率

-   当一个 SqlSession结束后，该 SqlSession中的一级查询缓存也就不存在了。**MyBatis 默认一级查询缓存是开启状态，且不能关闭**。

-   ```mermaid
    graph TD;
    	1(代码中的查询语句)-->2((缓存空间))
    	2-->1;
    	2-->3[数据库];
    	3-->2;
    	查询语句==>查找缓存区域;
    	查找缓存区域==>4[存在相同查询];
    	4==N==>查询数据库;
    	查询数据库==>返回查询结果;
    	4==Y==>返回查询结果;
    ```

### 4.1.1 一级查询缓存的存在性证明

#### 1. 修改测试类

```java
    //证明一级缓存存在,在同一个SqlSession中查询多次相同数据第二次开始会直接从缓存中读取数据
    @Test
    public void test02() {
        int sid = 1;
        //第一次查询
        Student student = dao.selectStudentBySid(sid);
        System.out.println(student.toString());
        //第二次查询
        Student student2 = dao.selectStudentBySid(sid);
        System.out.println(student2.toString());
    }
```

#### 2. 查看控制台

-   执行完后，发现只执行了一次从 DB 中的查询，第二次的结果是直接输出的,并没有再次查询数据库。说明，第二次是从 SqlSession缓存中读取的

-   ```mysql
    [DEBUG][selectStudentBySid:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
    [DEBUG][selectStudentBySid:159] = ==> Parameters: 1(Integer)
    [DEBUG][selectStudentBySid:159] = <==      Total: 3
    Student{sid=1, sname='sqm', courses=[Course{cid=2, cname='c++'}, Course{cid=3, cname='python'}, Course{cid=1, cname='java'}]}
    Student{sid=1, sname='sqm', courses=[Course{cid=2, cname='c++'}, Course{cid=3, cname='python'}, Course{cid=1, cname='java'}]}
    ```

### 4.1.2 从缓存读取数据的依据是Sql映射的 id

-   一级缓存缓存的是**相同Sql映射id的查询结果**，而非相同Sql语句的查询结果
-   myBatis 内部对于查询缓存，**无论是一级查询缓存还是二级查询缓存**，**其底层均使用一个HashMap实现**：key 为 Sql 的id相关内容，value 为从数据库中查询出的结果

#### 1. 修改映射文件

-   在映射文件中对某一个\<select/>标签进行完全复制，然后修改一下这个 SQL 映射的 id。也就是说，这两个 SQL 映射除了 id 不同，其它均相同，即查询结果肯定是相同的

-   ```xml
        <select id="selectStudentBySid" resultMap="StudentMap">
            SELECT * FROM student,selectcourse,course WHERE student.sid=#{sid} AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid
        </select>

        <select id="selectStudentBySid2" resultMap="StudentMap">
            SELECT * FROM student,selectcourse,course WHERE student.sid=#{sid} AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid
        </select>
    ```

#### 2. 修改dao层接口

```java
    Student selectStudentBySid(int sid);
    Student selectStudentBySid2(int sid);
```

#### 3. 修改测试类

```java
    //证明缓存读取数据的依据是sql的id,即缓存为HashMap格式,key为sql的id数据,value为sql的查询数据
    @Test
    public void test03() {
        int sid = 1;
        //第一次查询
        Student student = dao.selectStudentBySid(sid);
        System.out.println(student.toString());
        //第二次查询
        Student student2 = dao.selectStudentBySid2(sid);
        System.out.println(student2.toString());
    }
```

#### 4. 查看控制台

-   第二次查询结果与第一次的完全相同，但第二次查询并没有从缓存中读取数据,而是直接从 DB 中进行的查询;这是因为从缓存读取数据的依据是查询 SQL的映射 id，而非查询结果

```mysql
[DEBUG][selectStudentBySid:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
[DEBUG][selectStudentBySid:159] = ==> Parameters: 1(Integer)
[DEBUG][selectStudentBySid:159] = <==      Total: 3
Student{sid=1, sname='sqm', courses=[Course{cid=1, cname='java'}, Course{cid=2, cname='c++'}, Course{cid=3, cname='python'}]}
[DEBUG][selectStudentBySid2:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
[DEBUG][selectStudentBySid2:159] = ==> Parameters: 1(Integer)
[DEBUG][selectStudentBySid2:159] = <==      Total: 3
Student{sid=1, sname='sqm', courses=[Course{cid=3, cname='python'}, Course{cid=1, cname='java'}, Course{cid=2, cname='c++'}]}
```

### 4.1.3 增删改操作对一级查询缓存的影响

-   增、删、改操作，无论是否进行提交 sqlSession.commit()，均会清空一级查询缓存，使查询再次从 DB 中 select
-   因为增删改操作影响了底层的数据,所以不能从缓存中读取数据

#### 1. 修改测试类

```java
    //证明增删改操作会清除一级缓存
    @Test
    public void test04() {
        int sid = 3;
        //第一次查询
        Student student = dao.selectStudentBySid(sid);
        System.out.println(student.toString());

        //Student insert = new Student();
        //insert.setSname("zhangsan");
        //dao.insertStudent(insert);
        //dao.deleteStudentById(sid);
        dao.updateStudent(student);

        //第二次查询
        Student student2 = dao.selectStudentBySid(sid);
        System.out.println(student2.toString());
    }

```

#### 2. 查看控制台

```mysql
[DEBUG][selectStudentBySid:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
[DEBUG][selectStudentBySid:159] = ==> Parameters: 3(Integer)
[DEBUG][selectStudentBySid:159] = <==      Total: 2
Student{sid=3, sname='songqiming', courses=[Course{cid=2, cname='c++'}, Course{cid=3, cname='python'}]}
[DEBUG][updateStudent:159] = ==>  Preparing: UPDATE student SET sname=? WHERE sid=? 
[DEBUG][updateStudent:159] = ==> Parameters: songqiming(String), 3(Integer)
[DEBUG][updateStudent:159] = <==    Updates: 1
[DEBUG][selectStudentBySid:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
[DEBUG][selectStudentBySid:159] = ==> Parameters: 3(Integer)
[DEBUG][selectStudentBySid:159] = <==      Total: 2
Student{sid=3, sname='songqiming', courses=[Course{cid=3, cname='python'}, Course{cid=2, cname='c++'}]}
```

## 4.2 内置二级查询缓存

-   myBatis 查询缓存的作用域是根**据映射文件 mapper 的 namespace 划分的,相同namespace的mapper 查询数据存放在同一个缓存区域。不同namespace下的数据互不干扰**
-   无论是一级缓存还是二级缓存，都是按照 namespace进行**分别存放**的
-   SqlSession关闭后一级缓存消失,而二级缓存生命周期与整个应用同步,与SqlSession是否关闭无关
-   二级缓存中的数据**不是为了**在多个查询之间共享（所有查询中只要查询结果中存在该对象的，就直接从缓存中读取，这是对数据的共享，Hibernate中的缓存就是为了共享，但MyBaits 的不是）
-   **而是为了延长该查询结果的保存时间，提高系统性能**

### 4.2.1 二级缓存用法

-   二级查询缓存的使用很简单，只需要完成两步即可

#### 1. 实体序列化

-   要求查询结果所涉及到的实体类要**实现 java.io.Serializable接口**。**若该实体类存在父类， 或其具有域属性，则父类与域属性类也要实现序列化接口**

-   ```java
    public class Student implements Serializable{
        private Integer sid;
        private String sname;
        private Set<Course> courses;

    public class Course implements Serializable {
        private Integer cid;
        private String cname;
        private Set<Student> students;
    ```

#### 2. mapper 映射中添加\<cache/>标签

-   在 mapper 映射文件的\<mapper/>标签中添加\<cache/>子标签

-   ```xml
    <mapper namespace="com.sqm.selectcourse.dao.StudentDao">
        <cache/>
    ```

#### 3. 二级缓存配置

-   为\<cache/>标签添加一些相关属性设置，可以对二级缓存的运行性能进行控制。当然， 若不指定设置，则均保持默认值

    -   eviction：逐出策略。当二级缓存中的对象达到**最大值**时，就需要通过逐出策略将缓存中的对象**移出缓存**。**默认为 LRU**。常用的策略有：
        -   FIFO：First In First Out，**先进先出**
        -   LRU：Least Recently Used，**未被使用时间最长的**
    -   flushInterval：刷新缓存的时间间隔，单位毫秒。这里的**刷新缓存即清空缓存**。**一般不指定，即当执行增删改时刷新缓存**。
    -   readOnly：设置缓存中数据**是否只读**。只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改，这**提供了很重要的性能优势**。但读写的缓存会返回缓存对象的拷贝。这会慢一些，**但是安全**，因此**默认是 false**
    -   size：二级缓存中可以存放的最多对象个数。**默认为 1024 个**

-   ```xml
        <!--eviction默认为LRU(未被使用时间最长的先出缓存;flushInterval一般不指定,清空缓存的毫秒时间间隔;readOnly默认false;size为二级缓存最大的对象个数-->
        <cache eviction="FIFO" flushInterval="10800000" readOnly="true" size="512"/>
    ```

###4.2.2 二级查询缓存的存在性证明

-   于映射文件中的同一个查询，肯定是同一个 namespace中的查询（因为就是同一个查询）。在一次查询后，将 SqlSession 关闭，再进行一次相同查询，发现并没有到DB中进行select 查询。说明二级查询缓存是存在的

#### 1. 修改测试类

-   第一次查询完关闭SqlSession再开启新一个SqlSession再查询一次再关闭

-   ```java
        //证明二级缓存的存在
        @Test
        public void test05() {
            int sid = 3;
            //第一次查询
            Student student = dao.selectStudentBySid(sid);
            System.out.println(student.toString());

            session.close();

            session = MyBatisUtil.getSession();
            dao = session.getMapper(StudentDao.class);
            //第二次查询
            Student student2 = dao.selectStudentBySid(sid);
            System.out.println(student2.toString());
        }
    ```

#### 2. 查看控制台

-   Cache Hit Ratio 表示缓存命中率。开启二级缓存后，每执行一次查询，系统都会计算一次二级缓存的命中率。第一次查询也是先从缓存中查询，只不过缓存中一定是没有的。所以会再从 DB 中查询。由于二级缓存中不存在该数据，所以命中率为 0。但第二次查询是从二级缓存中读取的，所以这一次的命中率为 1/2 = 0.5;若有第三次查询的话，则命中率会是/3 = 0.66

-   ```mysql
    [DEBUG][StudentDao:62] = Cache Hit Ratio [com.sqm.selectcourse.dao.StudentDao]: 0.0
    [DEBUG][selectStudentBySid:159] = ==>  Preparing: SELECT * FROM student,selectcourse,course WHERE student.sid=? AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid 
    [DEBUG][selectStudentBySid:159] = ==> Parameters: 3(Integer)
    [DEBUG][selectStudentBySid:159] = <==      Total: 2
    Student{sid=3, sname='songqiming', courses=[Course{cid=3, cname='python'}, Course{cid=2, cname='c++'}]}
    [DEBUG][StudentDao:62] = Cache Hit Ratio [com.sqm.selectcourse.dao.StudentDao]: 0.5
    Student{sid=3, sname='songqiming', courses=[Course{cid=3, cname='python'}, Course{cid=2, cname='c++'}]}
    ```

### 4.2.3 增删改操作对二级查询缓存的影响

#### 1. 默认对缓存的刷新

-   **增删改操作**，无论是否进行提交 sqlSession.**commit()**，**均会清空一级、二级查询缓存**， 使查询再次从 DB 中 select

##### 1. 修改测试类

-   在上一次测试基础上的第二次查询前进行增删改

##### 2. 查看控制台

-   在第二次查询时的缓存命中率为 0.5，但还是从 DB 中查询了。说明在缓存中与该查询相对应的key 是存在的，但其 value 被清空。而 value 被清空的原因是前面执行了对DB 的增删改操作，所以不会从缓存中直接将 null 值返回，而是从 DB 中进行查询

#### 2. 设置删改操作不刷新二级缓存

-   若要使某个增、删或改操作不清空二级缓存，则需要在其\<insert/>或\<delete/>或\<update/>中**添加属性 flushCache=”false”**，**默认为 true**

-   ```xml
        <insert id="insertStudent" flushCache="false">
            INSERT INTO student(sname) VALUES (#{sname})
        </insert>
    ```

### 4.2.4 二级缓存的关闭

-   二级缓存默认为开启状态。若要将其关闭，则需要进行相关设置。根据关闭的范围大小，可以分为全局关闭与局部关闭

1.  **全局关闭:**整个应用的二级缓存全部关闭，**所有查询均不使用二级缓存**

    -   全局设置\<settings/>中，该属性为 **cacheEnabled，设置为 false，则关闭**；设置为 true，则开启，**默认值为 true**。即二级缓存默认是开启的

    -   ```xml
            <!--全局参数设置-->
            <settings>
                <setting name="cacheEnabled" value="false"/>
            </settings>
        ```

2.  局部关闭:整个应用的二级缓存是开启的，但只是针对于某个\<select/>查询，不使用二级缓存。此时可以**单独只关闭该\<select/>标签的二级缓存**

    -   在要关闭二级缓存的select操作标签上添加属性useCache="false"

    -   ```xml
            <select id="selectStudentBySid2" resultMap="StudentMap" useCache="false">
              ...
        	</select>
        ```

### 4.2.5 二级缓存使用原则

1.  **只能在一个命名空间下使用二级缓存**
    -   由于二级缓存中的数据是基于 namespace的，即不同 namespace 中的数据互不干扰。在多个 namespace 中若均存在对同一个表的操作，那么这多个 namespace中的数据可能就会出现不一致现象
2.  **在单表上使用二级缓存**
    -   如果一个表与其它表有关联关系，那么就非常有可能存在多个namespace 对同一数据的操作。而不同 namespace中的数据互不干扰，所以有可能出现这多个 namespace中的数据不一致现象
3.  **查询多于修改时使用二级缓存**
    -   在查询操作远远多于增删改操作的情况下可以使用二级缓存。因为任何增删改操作都将刷新二级缓存，**对二级缓存的频繁刷新将降低系统性能**

## 4.3 ehcache二级查询缓存

-   mybatis 的特长是 SQL 操作，缓存数据管理不是其特长，为了提高缓存的性能，myBatis 允许使用第三方缓存产品。ehCache 就是其中的一种
-   **使用 ehcache 二级缓存，实体类无需实现序列化接口**

1.  导入jar包

    -   这里需要两个 Jar 包：一个为 ehcache 的核心 Jar包，一个是 myBatis 与 ehcache 整合的插件 Jar包。它们可以从 [git](https://github.com/mybatis/ehcache-cache/releases)下载

    -   mybatis-encache-1.0.3.jar

        -   依赖:encache-core-2.6.8.jar与slf4j-api-1.6.1.jar(MyBatis已依赖)

    -   maven中的pom.xml:

        -   ```xml
                    <dependency>
                        <groupId>org.mybatis.caches</groupId>
                        <artifactId>mybatis-ehcache</artifactId>
                        <version>1.0.3</version>
                    </dependency>
            ```

2.  添加ehcache.xml配置文件

    1.  解压 EHCache 的核心 Jar 包 ehcache-core-2.6.8.jar，将其中的一个配置文件ehcache-failsafe.xml 直接**放到项目的src目录下**(maven中是resources文件夹)中，并更名为**ehcache.xml**

    2.  **\<diskStore/>标签**

        -   指定一个文件目录，当内存空间不够，需要将二级缓存中数据写到硬盘上时，会写到这个指定目录中。其值一般为 *java.io.tmpdir*，表示当前系统的默认文件临时目录

        -   当前系统的默认临时文件目录，可以通过 System.property()方法查看：

        -   ```java
                //查看系统默认临时文件目录
                @Test
                public void test06() {
                    String path = System.getProperty("java.io.tmpdir");
                    System.out.println(path);
                }
            ```

        -   返回C:\Users\sqm\AppData\Local\Temp\

    3.  **\<defaultCache/>标签**

        -   设定缓存的默认属性数据：
            -   **maxElementsInMemory**：指定该内存缓存区**可以存放缓存对象的最多个数**。
            -   **eternal**：设定缓存对象**是否不会过期**。若设为 true，表示对象永远不会过期，此时会忽略 timeToIdleSeconds 与 timeToLiveSeconds 属性。默认值为 false。
            -   **timeToIdleSeconds**：设定允许对象处于**空闲状态的最长时间**，以**秒**为单位。当对象自从最近一次被访问后，**若处于空闲状态的时间超过了 timeToIdleSeconds 设定的值，这个对象就会过期**。当对象过期，ehcache 就会将它从缓存中清除。 **设置值为0，则对象可以无限期地处于空闲状态**
            -   **timeToLiveSeconds**：设定对象允许**存在于缓存中的最长时间**，以**秒**为单位。当对象自从被存放到缓存后，若处于缓存中的时间超过了timeToLiveSeconds 设定的值，这个对象就会过期。当对象过期，ehcache 就会将它从缓存中清除。设置值为 0，则对象可以无限期地存在于缓存中。注意，**只有timeToLiveSeconds≥ timeToIdleSeconds**，才有意义
            -   **overflowToDisk**：设定为 true，表示当缓存对象达到了 maxElementsInMemory 界限， 会将溢出的对象写到\<diskStore>元素指定的硬盘目录缓存中
            -   **maxElementsOnDisk**：指定**硬盘缓存区**可以存放缓存对象的最多个数。
            -   **diskPersistent**：指定当程序结束时，**硬盘缓存区中的缓存对象是否做持久化**
            -   diskExpiryThreadIntervalSeconds：指定硬盘中缓存对象的失效时间间隔。
            -   memoryStoreEvictionPolicy：如果内存缓存区超过限制，选择移向硬盘缓存区中的对象时使用的策略。支持三种策略：
                -   FIFO：First In FirstOut，先进先出
                -   LFU：Less Frequently Used，最少使用
                -   LRU：LeastRecently Used，最近最少使用

### 4.3.1 启用ehcache 缓存机制

-   在映射文件的 mapper 中的\<cache/>中通过 **type属性**指定缓存机制为ehcache 缓存(org.mybatis.caches.ehcache.EhcacheCache)。默认为

    myBatis内置的二级缓存org.apache.ibatis.cache.impl.PerpetualCache

    ```xml
        <!--设置缓存类型为ehcache缓存-->
        <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
    ```

    ​

### 4.3.2 ehcache 在不同 mapper 中的个性化设置

-   在 ehcache.xml中设置的属性值，会对该项目中所有使用 ehcache缓存机制的缓存区域起作用。

-   一个项目中可以有多个mapper，不同的 mapper有不同的缓存区域。对于不同存区域也可进行专门针对于当前区域的个性化设置，可通过**指定不同mapper 的\<cache>属性值来设置**

    -   **\<cache>属性值的优先级高于 ehcache.xml 中的属性值**

    -   ```xml
            <!--设置缓存类型为ehcache缓存-->
            <cache type="org.mybatis.caches.ehcache.EhcacheCache">
                <property name="maxElementsInMemory" value="5000"/>
                <property name="timeToIdleSeconds" value="240"/>
              	...
            </cache>
        ```

# 5. MyBatis注解式开发

-   mybatis 的注解，主要是用于**替换映射文件**。而映射文件中无非存放着增、删、改、查的 SQL
    映射标签。所以，**mybatis 注解，就是要替换映射文件中的 SQL 标签**

## 5.1 注解的基础知识

1.  注解的基础语法

    1.  注解后是没有分号的。
    2.  注解首字母是大写的，因为**注解与类、接口是同一级别**的。一个注解，后台对应着一个**@interface 类**。
    3.  在同一语法单元(类,接口,方法,属性等)上，同一注解只能使用一次。
    4.  在注解与语法单元之间**可以隔若干空行、注释**等非代码内容

2.  注解的注解

    -   ```java
        @Documented
        @Retention(RetentionPolicy.RUNTIME)
        @Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, MODULE, PARAMETER, TYPE})
        public @interface Deprecated {
        ```

    -   @Deprecated 源码，看到其定义上还有三个注解：@Documented、@Retention、@Target。

        -   **@Target**：用于指定该注解**可以标注的语法类型**。CONSTRUCTOR（构造器）、LOCAL_VARIABLE（局部变量）、METHOD（方法）、FIELD（属性）、PACKAGE（包）、PARAMETER（参数）、TYPE（类型）
            -   对于 TYPE 常量，其意义有两个：一是指该注解可以用在类、接口、枚举等类上;二是指该注解可以**作为其它注解的属性值**
        -   @Documented：用于指定该注解定义时的注释信息能否显示在 javaAPI说明文档上。没有添加的话，使用 javadoc生成 API 文档时就不会将该注解的信息添加到文档
        -   @RetentionPolicy：这是一个 **enum 类型**，共有三个值：SOURCE,CLASS 和 RUNTIME
            -   **SOURCE**：代表这个 Annotation 类型的信息**只会保留在程序源码**里，源码如果经过了**编译之后，Annotation 的数据就会消失**，并不会保留在编译好的**.class** 文件中。
            -   **CLASS**：代表这个Annotation 类型的信息除了保留在程序源码里外，同时也会保留在**编译好的.class** 文件里。但**在执行**时，并不会把这一些信息加载到**虚拟机(JVM)**中去。这是**这是Retention的默认值**
            -   **RUNTIME**：表示在源码、编译好的.class 文件中保留信息，同时在执行时还会把这些信息**加载到 JVM** 中
            -   例如：@Override 中的 Retention 值为 SOURCE，编译成功了就不要这一些检查的信息。相反@Deprecated 中 Retention 的值为 RUNTIME，表示除了在编译时会警告我们使用了哪个被 Deprecated 的方法，在执行的时候也可以查出该方法是否被 Deprecated

3.  注解的属性

    -   当某变量被声明了，但却未被使用；或某集合在声明或定义时未加泛型说明等情况发生时，会在代码下给出**警告黄线**。可在代码上添加一个注解@SuppressWarnings()

    -   打开其源码，看到其定义与@Deprecated是不同的，其接口体中声明了一个方法`String[] value()`。那么**该注解在使用时必须包含一个属性 value，类型为 String[]。且该参数没有默认值，即必须给出 value的值**

        -   ```java
            @Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, MODULE})
            @Retention(RetentionPolicy.SOURCE)
            public @interface SuppressWarnings {
                /**
                 * The set of warnings that are to be suppressed by the compiler in the
                 * annotated element.  Duplicate names are permitted.  The second and
                 * successive occurrences of a name are ignored.  The presence of
                 * unrecognized warning names is <i>not</i> an error: Compilers must
                 * ignore any warning names they do not recognize.  They are, however,
                 * free to emit a warning if an annotation contains an unrecognized
                 * warning name.
                 *
                 * <p> The string {@code "unchecked"} is used to suppress
                 * unchecked warnings. Compiler vendors should document the
                 * additional warning names they support in conjunction with this
                 * annotation type. They are encouraged to cooperate to ensure
                 * that the same names work across multiple compilers.
                 * @return the set of warnings to be suppressed
                 */
                String[] value();
            }
            ```

    -   对于注解的属性，需要注意以下几点:

        1.  数组问题
            -   该属性在源码定义时被声明为数组，但**在具体使用时却只要赋予一个值**，此时无需将该值再定义为一个数组后赋给该属性。**直接将该值赋给该属性即可**。
            -   例如，对于声明为字符串数组 String[]的 value 属性，可以将**字符串 String** 直接赋给该 value
        2.  默认值问题
            -   若某属性在注解定义时**声明了其默认值**，则在注解使用时，**可以不为其指定属性值**。注解会自动使用其默认值
        3.  value属性问题
            -   若注解在使用时只需使用其 value 属性，其它属性要么有默认值，要么该注解只声明了一个 value 属性，此时，在注解使用时 **value属性名称可省略**，而直接在注解的括号中写出该value属性的值
        4.  无属性问题
            -   有些注解在定义时，是没有属性的，那么在使用时只需给出注解名称即可

## 5.2 MyBatis注解

1.  **@Insert**

    -   其 value属性用于指定要执行的insert 语句

2.  **@SelectKey**

    -   用于替换 XML中的<selectKey/>标签，**用于返回新插入数据的 id 值**

    -   statement属性：获取新插入记录主键值的SQL 语句

    -   keyProperty：获取的该主键值返回后初始化对象的哪个属性

    -   resultType：返回值类型

    -   ```java
            @SelectKey(statement = "SELECT @@IDENTITY",keyProperty = "sid",resultType = int.class,before = false)
        ```

3.  **@Delete**

    -   其 value 属性用于指定要执行的 delete 语句

4.  **@Update**

    -   其 value 属性用于指定要执行的 update 语句

5.  **@Select**

    -   其 value属性用于指定要执行的 select 语句

## 5.3 程序举例

1.  修改dao接口

    -   在 Dao接口的每个抽象方法上添加注解

    -   ```java
            @Insert("INSERT INTO student(sname) VALUES (#{sname})")
            void insertStudentByAnnotation(Student student);

            @Insert("INSERT INTO student(sname) VALUES (#{sname})")
            @SelectKey(statement = "SELECT @@IDENTITY",keyProperty = "sid",resultType = int.class,before = false)
            void insertStudentCatchSid(Student student);

            @Delete("DELETE FROM student WHERE sid=#{sid}")
            void deleteStudentBySidByAnnotation(int sid);

            @Update("UPDATE student SET sname=#{sname} WHERE sid=#{sid}")
            void updateStudentByAnnotation(Student student);

            @Select("SELECT * FROM student")
            List<Student> selectStudents();

            @Select("SELECT * FROM student WHERE sid=#{sid}")
            Student selectStudentBySidByAnnotation(int sid);

            @Select("SELECT * FROM student WHERE sid=#{student.id}")
            Student selectStudentByMap(Map<String, Object> map);

            @Select("SELECT * FROM student WHERE name LIKE CONCAT('%',#{sname},'%')")
            List<Student> selectStudentsBySName(String sname);
        ```

2.  删除映射文件

    -   把不需要的映射文件删除

3.  修改主配置文件

    -   由于没有了映射文件，所以主配置文件中不能使用\<mapper/>注册 mapper 的位置了。需要使用\<package/>标签

    -   ```xml
            <mappers>
                <package name="com.sqm.selectcourse.dao"/>
            </mappers>
        ```

4.  修改测试类

    -   根据不同方法进行测试