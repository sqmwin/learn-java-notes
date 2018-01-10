[TOC]

# 2. 单表的CURD操作

## 2.1 自定义 Dao 接口实现类

### 2.1.1 搭建测试环境

1.  修改dao接口,加入所有CURD方法

    ```java
    public interface StudentDao {
        //插入Student
        void insertStudent(Student student);
        //插入后指定id
        void insertStudentCatchId(Student student);
        //删除,指定id值
        void deleteStudentById(int id);
        //修改Student
        void updateStudent(Student student);
        //查询所有
        List<Student> selectAllStudents();
        //查询所有,以id-student键值对形式返回
        Map<String,Student> selectAllStudentMap();
        //根据姓名查询,返回的列表的第一个元素为所查询的对象
        List<Student> selectStudentsByName(String name);
    }
    ```

2.  修改dao实现类,实现各个具体方法

3.  修改测试类,将dao的创建放在@Before注解中

    ```java
        private StudentDao dao;
    	@Before
        public void setUp() {
            dao = new StudentDaoImpl();
        }
    ```

### 2.1.2 插入数据

1.  修改映射文件,映射文件中的#{xxx}占位符底层使用的是反射,所以**xxx是类的成员变量,而不是数据库中表的字段名**;INSERT INTO ... (..)VALUES(...)

    ```xml
    <mapper namespace="com.sqm.dao.StudentDao">
        <!--插入一个student对象-->
        <insert id="insertStudent">
            INSERT INTO student(name,age,score)
            VALUES (#{name},#{age},#{score})
        </insert>
    </mapper>
    ```

2.  修改dao实现类,使用工具类获取SqlSession对象

3.  修改测试类,执行插入语句

### 2.1.3 插入后用新id 初始化被插入对象

#### 1. 修改映射文件

-   若在mysql中执行完INSERT INTO 语句后加上`SELECT @@IDENTITY `或者`SELECT LAST_INSERT_ID()`,则可输出插入行的id值,主键的值

-   mybaits中映射文件的insert标签的子标签**selectKey用于获取新插入记录的主键值**;语法即为mysql中的语法,执行此语句后会自动生成id值,通常是主键,自动增长

    -   selectKey标签的属性:
        -   resultType:获取的主键类型
        -   keyProperty:主键在Java类对应的属性名,实际将与insert()方法的第二个参数对象对应
        -   order:指出selectKey操作是在insert语句之前还是之后,可以不指定,mysql为AFTER,oracle为BEFORE

-   ```xml
        <!--插入并指定其id值-->
        <insert id="insertStudentCatchId" >
            INSERT INTO student(name,age,score)
                    VALUES (#{name},#{age},#{score})
            <selectKey keyProperty="id" resultType="int" order="AFTER">
                SELECT last_insert_id();
            </selectKey>
        </insert>
  ```

#### 2. 修改dao实现类,采用插入后获取id的语句

```java
    public void insertStudentCatchId(Student student) {
        try {
            session = MyBatisUtil.getSession();
            session.insert("insertStudentCatchId", student);
            session.commit();
        } finally {
            if (session != null) {
                session.close();
            }
        }
    }
```

#### 3. 修改测试类,获得插入的id值

```java
    //插入后自动生成id值
    @org.junit.Test
    public void Test02() {
        Student student = new Student("student",22,99);
        System.out.println("插入前student = " + student);
        dao.insertStudentCatchId(student);
        System.out.println("插入后student = " + student);
    }
```

#### 4. id是何时获取到的

-   无论插入操作是提交还是回滚，数据库均会为insert 的记录分配一个新的id,即使回滚，这个id 已经被占用。后面再插入并提交的记录数据，被分配的 id 是跳过此 id 后的 id
-   主键值的生成只与 insert语句是否执行有关，而与最终是否提交无关

### 2.1.4 删除数据

#### 1. 修改映射文件

-   删除语句的动态参数`#{xxx}`也表示一个占位符,代表delete()的第二个参数parameter,xxx可以为任意值,**无需与实际参数值相同**; DELETE FROM ... WHERE

-   ```xml
        <!--根据给定的id删除数据-->
        <delete id="deleteStudentById">
            DELETE FROM student.student WHERE id=#{choose}
        </delete>
  ```

#### 2.修改dao实现类

```java
    public void deleteStudentById(int id) {
        try {
            session = MyBatisUtil.getSession();
            session.delete("deleteStudentById", id);
            session.commit();
        } finally {
            if (session != null) {
                session.close();
            }
        }
    }
```

#### 3. 修改测试类

```java
    //删除指定的id的数据
    @org.junit.Test
    public void Test03(){
        dao.deleteStudentById(6);
    }
```

### 2.1.5 修改数据

#### 1. 修改映射文件

-   修改语句的动态参数`#{xxx}`**必需与实际参数值类型相同**,即为student对象的属性名称,不能随意填写;UPDATE ... SET ... WHERE...

-   ```xml
        <!--根据给定的Student对象修改数据-->
        <update id="updateStudent">
            UPDATE student.student SET name=#{name},age=#{age},score=#{score}
            WHERE id=#{id}
        </update>
  ```

#### 2.修改dao实现类

```java
    public void updateStudent(Student student) {
        try {
            session = MyBatisUtil.getSession();
            session.update("updateStudent", student);
            session.commit();
        } finally {
            if (session != null) {
                session.close();
            }
        }
    }
```

#### 3. 修改测试类

```java
    //修改指定的Student数据
    @org.junit.Test
    public void Test04(){
        Student student = new Student("define", 32, 99);
        student.setId(5);
        dao.updateStudent(student);
    }
```



### 2.1.6 查询所有对象并返回List

#### 1. 修改映射文件

-   select标签的resultType属性不是最终的List类型,而是每查询到一条记录,将记录封装为对象的类型;SELECT * FROM student

-   **同一个映射文件一般是映射同一个类的操作**,例如StudentDao一个映射文件,TeacherDao一个映射文件

    -   resultType使用全限定类名,同样可使用**别名**用于简化写法

-   ```xml
        <!--查询所有数据并返回list-->
        <select id="selectAllStudents" resultType="Student">
            SELECT * FROM student.student
        </select>
  ```

#### 2.修改dao实现类

```java
    public List<Student> selectAllStudents() {
        //创建接收容器
        List<Student> students = null;
        try {
            session = MyBatisUtil.getSession();
            students = session.selectList("selectAllStudents");
            //select语句无需commit
        } finally {
            if (session != null) {
                session.close();
            }
        }
        return students;
    }

```

#### 3. 修改测试类

```java
    //获取所有Student对象并以List形式返回
    @org.junit.Test
    public void Test05() {
        List<Student> students = dao.selectAllStudents();
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

### 2.1.7 查询所有对象并返回Map

#### 1. 修改映射文件

-   映射文件不用修改,与selectAllStudents的select语句相同

-   ```xml
        <!--查询所有数据并返回Map-->
        <select id="selectAllStudents" resultType="Student">
            SELECT * FROM student.student
        </select>
  ```

#### 2.修改dao实现类

-   使用selectMap()方法完成查询,此方法返回的所有记录,每一条封装成一个value对象,并作为Map集合的value,将指定属性,也就是方法的String mapKey参数所对应的字段名作为Map集合的key,**方法原型为:`Map<K,V> selectMap(String statement,String mapKey)`**

```java
    public Map selectAllStudentMap(String mapKey) {
        //创建接受容器
        Map studentMap = null;
        try {
            session = MyBatisUtil.getSession();
            studentMap = session.selectMap("selectAllStudents", mapKey);
        } finally {
            if (session != null) {
                session.close();
            }
        }
        return studentMap;
    }
```

#### 3. 修改测试类

-   在实际使用时再限制类型,因为key的类型可以是任意数据库中对应的类型

```java
    //获取所有对象,以map形式返回,key为每个指定的唯一字段,value为每一条数据
    @org.junit.Test
    public void test06() {
        Map<Integer, Student> studentMap = dao.selectAllStudentMap("id");
        Set<Integer> studentMapSet = studentMap.keySet();
        for (Integer key : studentMapSet) {
            Student student = studentMap.get(key);
            System.out.println(key + ":" +student.toString());
        }
    }
```

#### 4. 说明

-   若指定的key在数据库中并不唯一,则后面的记录值会覆盖掉前面的值;即指定 key 的 value
    值，一定是 DB 中该同名属性的最后一条记录值

### 2.1.8 查询单个对象

#### 1. 修改映射文件

-   ```xml
        <!--通过id值查询单个对象-->
        <select id="selectStudentById" resultType="Student">
            SELECT *
            FROM student.student
            WHERE id = #{id}
        </select>
  ```

#### 2.修改dao实现类

-   使用SqlSession的selectOne()方法,其会将查询的结果记录封装为一个指定类型的对象;方法原型为：`Object selectOne (String statement, Object parameter)`
    -   statement： 映射文件中配置的SQL语句的id
    -   parameter：查询条件中动态参数的值 

```java
    public Student selectStudentById(int id) {
        //定义返回值的容器
        Student student = null;
        try {
            session = MyBatisUtil.getSession();
            student= session.selectOne("selectStudentById", id);
        } finally {
            if (session != null) {
                session.close();
            }
        }
        return student;
    }
```

#### 3. 修改测试类

```java
    //获取指定id的单个student对象
    @org.junit.Test
    public void test07() {
        Student student = dao.selectStudentById(3);
        System.out.println(student.toString());
    }

```

### 2.1.9 模糊查询

#### 1. 修改映射文件

-   在进行模糊查询时,用到WHERE ... LIKE ... 语句,LIKE语句中,%代表零或任意多个字符,_代表一个字符,例如:`WHERE name LIKE _a%`

-   也可以使用字符串的拼接,SQL 中的字符串的拼接使用的是函数**concat(arg1,arg2,…)**。**注意不能使用Java 中的字符串连接符+**

    -   ```mysql
        SELECT * FROM student.student WHERE name LIKE concat('%',#{value},'%')
        ```

    -   `#{xxx}`相当于preparedStatement语句中的?占位符

    -   在mapper的select标签中${value}代表的是直接将参数拼接到sql语句中,可能会发生sql注入

    -   ```mysql
        SELECT * FROM student.student WHERE name LIKE '%${value}%'
        ```

#### 2.修改dao实现类

```java
    public List<Student> selectStudentsByName(String name) {
        List<Student> students;
        try {
            session = MyBatisUtil.getSession();
            students = session.selectList("selectStudentsByName", name);
        } finally {
            if (session != null) {
                session.close();
            }
        }
        return students;
    }
```

#### 3. 修改测试类

```java
    //模糊查询
    @org.junit.Test
    public void test08() {
        String name = "i";
        List<Student> students = dao.selectStudentsByName(name);
        System.out.println("name中包含" + name + "的student有:");
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

#### 4. $与#的区别

-   \$与#的区别是很大的。**#为占位符**，**而\$为字符串拼接符**。
-   字符串拼接是**将参数值以硬编码的方式直接拼接到了 SQL 语句中**。字符串拼接就会引发两个问题：**SQL 注入问题与没有使用预编译所导致的执行效率低下问题**。
-   占位符的引入，解决以上两个问题;占位符的底层执行时，**是为 SQL 动态进行赋值**
    -   一般情况下，动态参数的值是**由用户输入的**,例如用户指定查询某个id，则**不能使用拼接符$**，因为有可能会出现SQL注入；
    -   若**动态参数的值是由系统计算生成的，则可以使用拼接符$**,例如从student对象中获取属性值与数据库字段对应,但仍存在执行效率问题
-   在 MyBatis中:
    -   使用#号占位符，则后台执行 SQL使用的为PreparedStatement，将会防止 SQL 注入
    -   而使用$符，则为字符串拼接，使用的为 Statement，将无法防止 SQL 注入

#### 5. SQL的预编译回顾

-   当 Java代码通过 JDBC 的 PreparedStatement 向 DB 发送一条 SQL 语句时，DBMS 会首先编译 SQL 语句，然后将编译好的 SQL 放入到 DBMS 的数据库缓存池中再执行。当 DBMS 再次接收到对该数据库操作的 SQL 时，先从 DB 缓存池中查找该语句是否被编译过，若被编译过， 则直接执行，否则先编译后将编译结果放入 DB 缓存池，再执行。

### 2.1.10 根据给定的Map进行查询

-   mapper 中 SQL 语句的动态参数也可以是 Map 的 key,例如同时指定student对象的age和score来查询
-   将多条查询语句条件的参数封装在Map\<String,Object\>中

#### 1. 修改配置文件

```xml
    <!--通过指定的字段名来查找符合条件的数据-->
    <select id="selectStudentsByMap" resultType="Student">
        SELECT *
        FROM student.student
        WHERE age &lt;= '%' #{ageCondition} '%' AND score >= '%' #{scoreCondition} '%';
    </select>
```

####  2. 修改dao实现类

```java
   	//根据给定Map模糊查询,map的key为字段名,value为模糊查询的条件
	public List<Student> selectStudentsByMap(Map<String,Object> map) {
        List<Student> students;
        try {
            session = MyBatisUtil.getSession();
            students = session.selectList("selectStudentsByMap", map);
        } finally {
            session.close();
        }
        return students;
    }
```

#### 3. 修改测试类

```java
    //根据给定的map模糊查询
    @org.junit.Test
    public void test09() {
        Object ageCondition = 22;
        Object scoreCondition = 80;
        //指定的map中的key必须包含mapper中sql语句要查找的项目
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("ageCondition",ageCondition);
        map.put("scoreCondition",scoreCondition);
        List<Student> students = dao.selectStudentsByMap(map);
        System.out.println("年龄小于等于" + ageCondition + "且得分大于等于" + scoreCondition + "的student有");
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

### 2.1.11 根据给定的Map进行查询2

-   mapper 中 SQL 语句的动态参数也可以是 Map 的 key,例如//根据id查询单个学生对象,既可以指定特定id的Student对象来查询,也可以通过直接查询id来查询,将两种查询方法封装在Map中
    -   直接通过id查询,则在map中封装为key="studentId",value=id值
    -   通过指定id的student对象查询,则在map中封装为key="student",value=student对象

#### 1. 修改配置文件

\#{studentId}代表直接获取map中key为studentId的值

\#{student.id}代表获取map中key为student的对象的id属性的值

```xml
    <!--通过id值或指定id的Student对象来查询单个对象-->
    <select id="selectStudent" resultMap="studentMap">
        SELECT *
        FROM student.student
        WHERE s_id = #{studentId} OR s_id = #{student.id};
    </select>
```

### 2. 修改dao实现类

```java
    //根据id查询单个学生对象,既可以指定特定id的Student对象来查询,也可以通过直接查询id来查询,将两种查询方法封装在Map中
	public Student selectStudent(Map map) {
        Student student;
        try {
            session = MyBatisUtil.getSession();
            student = session.selectOne("selectStudent", map);
        } finally {
            if (session != null) {
                session.close();
            }
        }
        return student;
    }
```

#### 3. 修改测试类

```java
    //通过指定id值或指定特定id值的student对象来获取student对象
    @org.junit.Test
    public void test07() {
        int id = 2;
        Student student = new Student();
        student.setId(id);
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("studentId", id);
        map.put("student", student);
        System.out.println("要查询的id值为:" + id + ";要查询的student对象为:" +student.toString());
        student = dao.selectStudent(map);
        System.out.println(student.toString());
    }
```

#

## 2.2 属性名与查询字段名不相同的情况

-   mapper的子标签中,resultType属性值可以将查询结果直接映射为实体 Bean对象的条件是，SQL 查询的字段名与实体 Bean 的属性名一致,**自动将查询结果字段名称作为对象的属性名,通过反射机制完成对象的创建**
-   若属性名与查询字段名不同时,则需要用到**resultMap标签**来指定属性名与查询字段名之间的映射关系

### 2.2.1 使用结果映射resultMap

-   在mapper的子标签中使用resultMap标签确立实体类的属性与查询表的字段的映射关系,这里的Map是mapper的意思,resultMap是对resultType的增强

-   ```xml
    <mapper namespace="com.sqm.dao.StudentDao">
        <!--设定结果映射-->
        <resultMap id="studentMap" type="Student">
            <!--id标签专指id属性即主键字段与实体类的id属性对应的关系-->
            <id column="s_id" property="id"/>
            <!--result标签指其他属性间的对应关系,没有区别的属性可以不设对应关系,仍然为resultType的关系-->
            <result column="s_name" property="name"/>
            <result column="s_age" property="age"/>
            <result column="s_score" property="score"/>
        </resultMap>	
     	
        <!--通过名字中包含的字符串模糊查询-->
        <select id="selectStudentsByName" resultMap="studentMap">
            SELECT *
            FROM student.student
            WHERE s_name LIKE concat('%',#{value},'%')
        </select>

    </mapper>  
    ```

    -   type：指定要映射的实体类
    -   id：指定该 resultMap 映射关系的名称,之后的sql语句操作方法在resultMap属性中引用其值
    -   `<id>`标签：id 的字段名 column 与实体类的属性property 间的映射关系
    -   `<result>`标签：id 以外其它字段名 column 与实体类的属性 property间的映射关系,若字段名与实体类的属性名相同，可以不写入resultMap中。

## 2.3 Mapper动态代理

-   Java语句中的Dao 的实现类其实并没有干什么实质性的工作,它仅通过 SqlSession相关 API定位到映射文件 mapper中相应 id 的SQL语句;真正对 DB 进行操作的工作其实是由框架通过 mapper中的**SQL语句操作标签**完成的

### 2.3.1  映射文件的 namespace 属性值

-   MyBatis 框架要求，将映射文件中mapper标签的 namespace 属性设为 Dao 接口的全类名;通过接口名即可定位到映射文件 mapper

-   ```xml
    <mapper namespace="com.sqm.dao.StudentDao">
    ```

### 2.3.2 日志输出控制文件

-   若为读取根Logger则输出太多无关紧要的语句,通过限定logger的namespace与mapper对应即可


-   ```properties
    log4j.logger.com.sqm.dao.StudentDao=debug,console
    ```

### 2.3.3 Dao接口方法名

-   MyBatis 框架要求，接口中的方法名，与映射文件中相应的SQL 标签的 id 值相同。系统

    会自动根据方法名到相应的映射文件中查找同名的 SQL 映射id

-   通过方法名就可定位到映射文件mapper 中相应的SQL语句

-   ```java
        //插入Student
        void insertStudent(Student student);
  ```

-   ```xml
        <!--插入一个student对象-->
        <insert id="insertStudent">
  ```

### 2.3.4 Dao对象的获取

-   之前dao对象的方法通过session来获取mapper中的方法

-   现在只需调用 SqlSession的 getMapper(Class\<T\> class)方法，即可获取指定接口的实现类对象。该方法的参数为指定 Dao接口类的 class值

-   ```java
    session = factory.operSession();
    dao = session.getMapper(StudentDao.class);
    ```

### 2.3.5 删除dao实现类

-   由于通过调用 Dao 接口的方法，不仅可以从SQL 映射文件中找到所要执行SQL 语句，

    还**可通过方法参数及返回值**将 SQL语句的动态参数传入，将查询结果返回。所以，Dao 的实现工作，完全可以由 MyBatis系统自动根据映射文件完成。所以，Dao 的实现类就不再需要了。

### 2.3.6 修改测试类

#### 1. @Before 与@After 注解方法

-   在@Before注解方法中获取到 SqlSession对象后，通过 SqlSession 的 getMapper()方法创建 Dao 接口实现类的动态代理对象。

-   在@After 注解方法中关闭 SqlSession 对象。

-   即把之前在dao实现类中获取session的代码放在测试类中

-   ```java
        //通过session的getMapper(Class<T> type)动态代理获得dao对象
        @Before
        public void setUp() {
            session = MyBatisUtil.getSession();
            dao = session.getMapper(StudentDao.class);
        }

        //将关闭session方法放入@after中
        @After
        public void tearDown() {
            session.close();
        }
  ```

#### 2. 添加SqlSession提交方法

-   通过commit()方法在每个test后提交增删改操作
-   即把之前在dao实现类中获取session的代码放在测试类中

#### 3. 删除 session.selectMap()相关方法的代码

-   MyBatis 框架对于 **Dao 查询**的自动实现，底层只会调用 **selectOne()与 selectList()**方法
-   接受类型为List则自动选择selectList();否则自动选择selectOne()方法

### 2.3.7 多查询条件无法整体接收的解决办法

[之前的例子是通过Map设置多查询条件](###2.1.10 根据给定的Map进行查询),还可以在mapper的sql语句中通过**参数索引\#{index}的方式**逐个接收每个参数

-   索引由arg0,arg1....表示,表示参数在接口方法中的索引位置

#### 1. 修改dao接口

```java
List<Student> selectStudentByConditions(String name,int age);
```

#### 2. 修改测试类

```java
  	//根据给定的条件来筛选数据
    @org.junit.Test
    public void test10() {
        String name = "s";
        int age = 25;
        int score = 85;
        List<Student> students = dao.selectStudentsByConditions(name, age,score);
        System.out.println("名字中包含" + name + "且年龄小于等于" + age + "得分大于" + score +"的学生有");
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

#### 3. 修改映射文件

```xml
    <!--通过给定的条件来筛选数据-->
    <select id="selectStudentsByConditions" resultMap="studentMap">
        SELECT *
        FROM student.student
        WHERE s_name LIKE '%' #{arg0} '%' AND s_age &lt;= #{arg1} AND s_score &gt;= #{arg2}
    </select>
```

## 2.4 动态SQL

-   当查询语句不确定时,根据用户提交的查询条件进行查询,提交的查询条件不同，执行的 SQL
    语句不同

-   **动态SQL:通过 MyBatis 提供的各种标签对条件作出判断以实现动态拼接 SQL 语句**

-   条件判断使用的表达式为 **OGNL** 表达式,即依赖包ognl-3.1.15.jar

    -   常用的动态sql标签有**\<if>,\<where>,\<choose>,\<foreach>**
    -   mybatis中的动态sql语句与JSTL语句非常相似

-   注意事项:

    -   在 mapper 的动态 SQL 中若出现大于号（>）、小于号（<）、大于等于号（>=），小于等于号（<=）等符号，最好将其转换为实体符号。否则，XML 可能会出现解析出错问题。特别是对于小于号（<），在 XML 中是绝对不能出现的。否则，一定出错。

    -   | 原符号  | <      | <=      | >      | >=      | &       | `        | "        |
        | ---- | ------ | ------- | ------ | ------- | ------- | -------- | -------- |
        | 替换符号 | `&lt;` | `&lt;=` | `&gt;` | `&gt;=` | `&amp;` | `&apos;` | `&quot;` |

        或者把所有的sql语句用CDATA区段包围,在CDATA区段的字符串会被解析器忽略

        ```xml
        <![CDATA[ SELECT * FROM student ]]>
        ```

### 2.4.1 \<if/>标签

#### 1. 定义dao接口

```java
    //根据给出的student对象的限制条件得到选择结果
    List<Student> selectStudentsIf(Student student);
```



#### 2. 定义映射文件

-   为了解决两个条件均未做设定的情况，**在 where 后添加了一个“1=1”的条件**。这样就不至于两个条件均未设定而出现只剩下一个 where，而没有任何可拼接的条件的不完整 SQL 语句。
-   if标签中的test语句是条件判断语句,用于判断Student类的属性是否满足条件,使用的是Student类的属性值而不是字段名,如`<if test="name != null and name != ''">`

```xml
    <!--根据if标签输出语句:判断给出的数据中的条件-->
    <select id="selectStudentsIf" resultMap="StudentMap">
        SELECT *
        FROM student
        WHERE 1 = 1
        <if test="name != null and name != ''">
            AND s_name LIKE '%' #{name} '%'
        </if>
        <if test="age > 0">
            AND s_age &gt; #{age}
        </if>
    </select>
```

#### 3. 修改测试类

```java
   //根据if标签中的sql语句判断获取数据
    @org.junit.Test
    public void test01() {
        Student condition = new Student();
        student.setName("song");
        student.setAge(15);
        List<Student> students = dao.selectStudentsIf(condition);
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

#### 4. 形成的SQL

-   name给出,age给出,传入两个参数

-   ```mysql
    Preparing: SELECT * FROM student WHERE 1 = 1 AND s_name LIKE '%' ? '%' AND s_age > ? 
    Parameters: li(String), 15(Integer)
    ```

-   name为null或空,age给出,传入一个参数

-   ```mysql
    Preparing: SELECT * FROM student WHERE 1 = 1 AND s_age > ? 
    Parameters: 15(Integer)
    ```


-   name给出,age给负数,传入一个参数

```mysql
Preparing: SELECT * FROM student WHERE 1 = 1 AND s_name LIKE '%' ? '%' 
Parameters: si(String)
```

-   两个参数都不给出,则返回全部参数

-   ```mysql
    Preparing: SELECT * FROM student WHERE 1 = 1 
    Parameters:
    ```

### 2.4.2\<where/>标签

-   if标签需要在where后添加一个必要的条件如1=1,否则当if的条件全部为false时,sql语句错误,程序会出错,where标签就是解决这个问题
-   where标签把if标签及条件包含在内,如果if标签全部为false,则where标签内的语句相当于true
-   where标签当于一个WHERE,当标签内的条件都为false时,则相当于忽略where标签内的所有sql语句

#### 1. 定义dao接口

```java
    //根据给出的限制条件的student对象返回数据,使用where标签
    List<Student> selectStudentsWhere(Student student);
```

#### 2. 定义映射文件

where标签中的第一个if标签下的sql语句,可以不写AND,但其他所有之后的if标签下的sql语句必须要写and

```xml
    <!--根据if标签条件输出语句,被where包含,若if全为false,则where中的语句相当于true-->
    <!--第一个AND可以不写,但之后if标签下的AND必须要写-->
    <select id="selectStudentsWhere" resultMap="StudentMap">
        SELECT *
        FROM student
        <where>
            <if test="name != null and name != ''">
                AND s_name LIKE '%' #{name} '%'
            </if>
            <if test="age > 0">
                AND s_age &gt; #{age}
            </if>
        </where>
    </select>
```



#### 3. 修改测试类

```java
    //根据if标签中的条件返回数据,若if全部为false,则where标签中包含的语句相当于true
    @org.junit.Test
    public void test02() {
        Student condition = new Student();
        //condition.setName("i");
        condition.setAge(-5);
        List<Student> students = dao.selectStudentsWhere(condition);
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

#### 4. 形成的SQL

-   两个查询条件为true时

```mysql
Preparing: SELECT * FROM student WHERE s_name LIKE '%' ? '%' AND s_age > ? 
Parameters: i(String), 22(Integer)
```

-   一个查询条件为true时

```mysql
Preparing: SELECT * FROM student WHERE s_age > ? 
Parameters: 22(Integer)
```

-   条件全为false时,返回全部数据

-   ```mysql
    Preparing: SELECT * FROM student 
    Parameters:
    ```

### 2.4.3\<choose/>标签

-   该标签中只能包含\<when/>标签和\<otherwise/>标签,可以包含多个when标签和最多一个otherwise标签;它们联合使用，完成Java 中的开关语句 switch..case功能
-   otherwise标签可以省略,此时若when标签内的条件不满足时,则会输出全部数据,相当于WHERE true
-   本例要完成的需求是，若姓名不空，则按照姓名查询；若姓名为空，则按照年龄查询；
    若没有查询条件，则没有查询结果

#### 1. 定义dao接口

-   可以不通过定义student对象,而使用接收接口方法参数索引的方式

```java
    //根据给出的限制条件返回数据结果,使用choose标签
    List<Student> selectStudentsChoose(String arg0,int arg1);
```

#### 2. 定义映射文件

-   when,if标签中的判断语句的参数也可改为接收接口方法的参数索引arg0,arg1

```xml
    <!--根据choose条件输出语句,choose中只能有一个或多个when标签和一个otherwise标签,相当于switch...case语句-->
    <select id="selectStudentsChoose" resultMap="StudentMap">
        SELECT * FROM student
        <where>
            <choose>
              <when test="arg0 != null and arg0 != ''">
                  AND s_name LIKE '%' #{arg0} '%'
              </when>
                <when test="arg1 > 0">
                    AND s_age = ${arg1}
                </when>
                <otherwise>
                    AND 1!= 1
                </otherwise>
            </choose>
        </where>
    </select>
```

#### 3. 修改测试类

```java
    @org.junit.Test
    public void test03() {
        String name = "li";
        int age = 19;
        List<Student> students = dao.selectStudentsChoose(name, age);
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

#### 4. 形成的SQL

-   两个when条件true时

-   ```mysql
    Preparing: SELECT * FROM student WHERE s_name LIKE '%' ? '%' 
    Parameters: li(String)
    ```

-   一个when条件true时


-   ```mysql
    Preparing: SELECT * FROM student WHERE s_age > ? 
    Parameters: 22(Integer)
    ```

-   两个when条件为false时,otherwise生效,不输出语句


-   ```mysql
    Preparing: SELECT * FROM student WHERE 1 != 1 
    Parameters: 
    ```

-   若去掉otherwise标签,两个when条件为false时,输出全部语句,相当于WHERE true

-   ```mysql
    Preparing: SELECT * FROM student 
    Parameters: 
    ```

### 2.4.4 \<foreach/>标签:遍历数组

-   **\<foreach/>**标签用于实现对于**数组与集合的遍历**。对其使用，需要注意：

    -   collection属性表示要遍历的集合类型，这里是数组，即array。

    -   item属性为每次遍历的属性;例如.item="myid":sql语句中对应的占位符为:`#{myid}`

    -   其他三个属性open、close、separator 为对遍历内容的 SQL 语句拼接:

        -   ```mysql
            SELECT * FROM student WHERE s_id IN ( ? , ? )
            ```

    -   ```xml
        <foreach collection= "array" open="(" close=")" item="myid" separator=",">
          #{myid}
        </foreach>
        ```

    -   ​

-   本例实现的需求是:查询出 id 为 1 与 3 的学生信息

#### 1. 定义dao接口

```java
    //根据给出的数组条件返回数据结果,使用foreach遍历条件
    List<Student> selectStudentsForeachArray(Object[] studentId);
```

#### 2. 定义映射文件

-   动态 QL的判断中使用的都是OGNL表达式
-   OGNL表达式中的**数组必须使用array表示**，数组长度使用**array.length**表示

```xml
    <!--根据给出的数组条件,遍历数组获取数据-->
    <select id="selectStudentsForeachArray" resultMap="StudentMap">
        SELECT * FROM student
        <where>
            <if test="array != null and array.length > 0 " >
                s_id IN
                <foreach collection="array" open="(" close=")" item="myid" separator=",">
                    #{myid}
                </foreach>
            </if>
        </where>
    </select>
```

#### 3. 修改测试类

```java
    //根据给出的数组条件,获取数据,查询出 id 为 1 与 3 的学生信息
    @org.junit.Test
    public void test04() {
        Object[] array = {1, 3};
        List<Student> students = dao.selectStudentsForeachArray(array);
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```

### 2.4.5\<foreach/>标签:遍历泛型为基本类型的List集合

#### 1. 定义dao接口

```java
    //根据给出的基本类型List集合条件返回数据结果,使用foreach遍历条件
    List<Student> selectStudentsForeachList(List<Integer> studentIds);
```

#### 2. 定义映射文件

-   OGNL表达式中List集合使用list表示,list大小使用list.size表示

```xml
    <!--根据给出的基本类型List集合条件返回数据结果,使用foreach遍历条件-->
    <!---->
    <select id="selectStudentsForeachList" resultMap="StudentMap">
        SELECT * FROM student
        <where>
            <if test=" list != null and list.size > 0">
                s_id IN
                <foreach collection="list" open="(" close=")" separator="," item="arg0">
                    #{arg0}
                </foreach>
            </if>
        </where>
    </select>
```



#### 3. 修改测试类

```java
    //根据给出的数组条件,获取数据,查询出id为 1 与 3 的学生信息
    @org.junit.Test
    public void test04() {
        Object[] array = {1, 3};
        List<Student> students = dao.selectStudentsForeachArray(array);
        for (Student student : students) {
            System.out.println(student.toString());
        }
    }
```



### 2.4.6 \<foreach/>标签:遍历泛型为自定义类型的List集合

#### 1. 定义dao接口

```java
    //根据给出的自定义类型List集合条件返回数据结果,使用foreach遍历条件
    List<Student> selectStudentsForeachList2(List<Student> students);
```



#### 2. 定义映射文件

-   foreach标签中的item属性指定要遍历的每一个自定义类型对象,在包含的sql语句中使用`#{item.属性值}`来获取对应占位符查找的值

```xml
    <!--根据给出的自定义类型List集合条件返回数据结果,使用foreach遍历条件-->
    <select id="selectStudentsForeachList2" resultMap="StudentMap">
      SELECT * FROM student
      <where>
          <if test="list != null and list.size > 0">
              s_id IN
              <foreach collection="list" item="student" open="(" close=")" separator=",">
                  #{student.id}
              </foreach>
          </if>
      </where>
    </select>
```

#### 3. 修改测试类

```java
    //根据给出的自定义类型List集合条件返回数据结果,使用foreach遍历条件
    @org.junit.Test
    public void test06() {
        Student s1 = new Student();
        s1.setId(1);
        Student s2 = new Student();
        s2.setId(3);
        List<Student> students = new ArrayList<Student>();
        students.add(s1);
        students.add(s2);
        List<Student> results = dao.selectStudentsForeachList2(students);
        for (Student result : results) {
            System.out.println(result.toString());
        }
    }
```

### 2.4.7\<sql/>标签

-   \<sql/>标签用于定义 SQL语句片断，**以便其它 SQL语句操作标签复用**
-   其他标签使用该sql片段时需用\<include/>子标签引入\<sql/>中定义的sql语句片段
    -   include标签的refid代表引入的sql片段标签的id值
-   该\<sql/>标签可以定义 SQL 语句中的**任何部分**，所以\<include/> 子标签可以放在动态 SQL
    的**任何位置**

#### 1. 定义dao接口

```java
    //测试sql标签,仍然使用前一个方法
    List<Student> selectStudentsBySqlFragment(List<Student> students);
```



#### 2. 定义映射文件

```xml
    <!--sql标签:定义sql语句片段-->
    <sql id="selectHead">
        SELECT * FROM student
    </sql>
    <sql id="iterateStudentId">
        <foreach collection="list" open="(" close=")" separator="," item="arg0" >
            #{arg0.id}
        </foreach>
    </sql>

    <!--测试sql标签-->
    <select id="selectStudentsBySqlFragment" resultMap="StudentMap">
      <include refid="selectHead"/>
      <where>
          <if test="list != null and list.size >0">
              s_id IN
              <include refid="iterateStudentId"/>
          </if>
      </where>
    </select>
```



#### 3. 修改测试类

```java
    //方法同上,用于测试sql标签的语句片段
    @org.junit.Test
    public void test07() {
        Student s1 = new Student();
        s1.setId(1);
        Student s2 = new Student();
        s2.setId(3);
        List<Student> students = new ArrayList<Student>();
        students.add(s1);
        students.add(s2);
        List<Student> results = dao.selectStudentsBySqlFragment(students);
        for (Student result : results) {
            System.out.println(result.toString());
        }
    }
```



