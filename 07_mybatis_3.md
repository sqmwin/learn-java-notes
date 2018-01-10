[TOC]

# 3. 关联关系查询

## 3.1 关联查询

-   关联查询:查询内容涉及到多个关联关系的多个表;根据表与表间的关联关系的不同，关联查询分为四种:

    1.  一对一关联查询

    2.  一对多关联查询

    3.  多对一关联查询

    4.  多对多关联查询

        ​

### 3.1.1 一对多关联查询

-   一对多关联查询:在查询一个对象时,将其所关联的所有对象都查询出来

#### 1. 定义实体类

-   若定义的是双向关联，即双方的属性中均有对方对象作为域属性出现;此时定义toString()方法时只让某一方可以输出另一方即可,不要让双方的toString()都输出对方对象,防止递归调用

-   ```java
    public class Country {
        private Integer cid;
        private String cname;
        private Set<Minister> ministers;
      	//Constructor
    	//Getter and Setter
    	//toString
      
    public class Minister {
        private Integer mid;
        private String mname;
      	//Constructor
    	//Getter and Setter
    	//toString
    ```

#### 2. 定义数据库表

```mysql
CREATE TABLE `country` (
  `cid` int(11) NOT NULL AUTO_INCREMENT,
  `cname` varchar(50) COLLATE utf8_bin NOT NULL,
  PRIMARY KEY (`cid`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 COLLATE=utf8_bin

CREATE TABLE `minister` (
  `mid` int(11) NOT NULL AUTO_INCREMENT,
  `mname` varchar(50) COLLATE utf8_bin NOT NULL,
  `cid` int(11) NOT NULL,
  PRIMARY KEY (`mid`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8 COLLATE=utf8_bin
```

#### 3. 定义dao层接口

```java
public interface SomeDao {
    //通过cid查询国家
    Country selectCountryById(int cid);
}
```

#### 4. 定义测试类

````java
    //通过id查询国家并显示国家信息和部长信息
    @org.junit.Test
    public void test01() {
        int id = 2;
        Country country = dao.selectCountryById(2);
        System.out.println(country.toString());
    }
````

#### 5. 定义映射文件

#####多表连接查询方式

-   resultMap中需要指定country类关联的属性minister集合的映射关系;使字段名与属性名相同，在\<resultMap/>中也要写出它们的映射关系;因为框架是依据\<resultMap/>封装对象的
-   查询语句为:`SELECT * FROM country,minister WHERE country.cid = ? AND country.cid = minister.cid`


-   ```xml
        <sql id="selectHead">
            SELECT * FROM country,minister
        </sql>
    	
    	<resultMap id="countryMapper" type="Country">
             <id column="cid" property="cid"/>
             <result column="cname" property="cname"/>
          	 <!--关联属性的映射关系-->
             <collection property="ministers" ofType="Minister">
                 <id column="mid" property="mid"/>
                 <result column="mname" property="mname"/>
             </collection>
         </resultMap>

        <!--通过id查询国家并显示国家信息和部长信息-->
        <select id="selectCountryById" resultMap="countryMapper">
            <include refid="selectHead"/>
            <where>
                country.cid = #{cid} AND country.cid = minister.cid
            </where>
        </select>
  ```

-   **collection标签**:用于指定实体对象与自定义类型集合对象间的关联关系

    -   collection的属性property指定被关联的对象在主类中(Country类)的属性,即Country类中的Set集合属性名;ofType是被关联对象的集合的泛型类型

-   ```mysql
    执行结果为:
    [DEBUG][selectCountryById:159] = ==>  Preparing: SELECT * FROM country,minister WHERE country.cid = ? AND country.cid = minister.cid 
    [DEBUG][selectCountryById:159] = ==> Parameters: 2(Integer)
    [DEBUG][selectCountryById:159] = <==      Total: 2
    Country{cid=2, cname='UK', ministers=[Minister{mid=4, mname='ddd'}, Minister{mid=5, mname='eee'}]}
    ```

-   ​

#####多表单独查询方式

-   多表连接查询是将多张表进行连接,连为一张表后进行查询;其本质是查询一张表,封装这张表的对象

-   多表单独查询是多张表各自查询各自的数据,将主表的查询数据结果与其他表的数据结果进行联合,封装为一个对象

    -   多表单独查询可以跨越多个mapper映射文件,使用其他映射文件的sql操作标签时,要添加其对应的namespace

-   ```xml
        <!--多表单独查询,查询country表的同时,使用resultMap关联到查询minister表的语句-->
        <!--根据cid查询minister数据-->
        <select id="selectMinisterByCid" resultType="Minister">
            SELECT * FROM minister WHERE minister.cid=#{cid}
        </select>

        <resultMap id="countryMapper" type="Country">
            <id column="cid" property="cid"/>
            <result column="cname" property="cname"/>
            <!--关联属性的映射关系-->
            <!--集合数据来自于select属性的查询方法selectMinisterByCid;而该查询的动态sql参数来自于column属性指定的字段值-->
            <collection property="ministers" ofType="Minister" column="cid" select="selectMinisterByCid"/>
        </resultMap>

        <!--根据id查询country表,对应的countryMapper调用查询minister表的语句-->
        <select id="selectCountryById" resultMap="countryMapper">
            SELECT * FROM country WHERE country.cid=#{cid}
        </select>
  ```

-   关联标签collection的数据来自于select属性指定的查询语句selectMinisterByCountry,而该查询语句的动态参数cid=#{cid}的值来自于column属性指定的Country对应的colunm属性cid,即selectCountryById中的动态参数cid

-   ```mysql
    执行结果为:
    [DEBUG][selectCountryById:159] = ==>  Preparing: SELECT * FROM country WHERE country.cid=? 
    [DEBUG][selectCountryById:159] = ==> Parameters: 2(Integer)
    [DEBUG][selectMinisterByCid:159] = ====>  Preparing: SELECT * FROM minister WHERE minister.cid=? 
    [DEBUG][selectMinisterByCid:159] = ====> Parameters: 2(Integer)
    [DEBUG][selectMinisterByCid:159] = <====      Total: 2
    [DEBUG][selectCountryById:159] = <==      Total: 1
    Country{cid=2, cname='UK', ministers=[Minister{mid=4, mname='ddd'}, Minister{mid=5, mname='eee'}]}
    ```

### 3.1.1 多对一关联查询

-   多对一关联查询:在查询多方对象时,将其关联的一方对象也查询出来
-   由于在查询多方对象时也是一个一个查询，所以多对一关联查询，其实就是一对一关联查询。即一对一关联查询的实现方式与多对一的实现方式相同
-   以部长 Minister 与国家 Country 间的多对一关系进行演示
-   查询部长时返回国家

#### 1. 修改实体类

-   将国家County对象作为minister的域属性,而country类中无minister属性

-   ```java
    public class Minister {
        private Integer mid;
        private String mname;
        private Country country;
       //Constructor
    	//Getter and Setter
    	//toString
      
    public class Country {
        private Integer cid;
        private String cname;
      	//Constructor
    	//Getter and Setter
    	//toString
    ```

-   沿用之前的数据库

#### 2. 定义dao接口

```java
    //通过mid查询minister,并显示他的country
    com.sqm.pojo2.Minister selectMinisterById(int mid);
```

#### 3. 定义测试类

```java
    //通过mid查询minister,并显示他的country
    @org.junit.Test
    public void test02() {
        int mid = 3;
        com.sqm.pojo2.Minister minister = dao.selectMinisterById(mid);
        System.out.println(minister.toString());
    }
```

####4. 定义映射文件

##### 多表连接查询

```xml
    <!--多表连接查询-->
    <resultMap id="ministerMap" type="com.sqm.pojo2.Minister">
        <id column="mid" property="mid"/>
        <result column="mname" property="mname"/>
        <!--通过关联关系标签使Minister类中的country属性与Country类进行关联,再通过id和result指定数据库表colunm与Country类中属性的映射关系-->
        <association property="country" javaType="com.sqm.pojo2.Country">
            <id column="cid" property="cid"/>
            <result column="cname" property="cname"/>
        </association>
    </resultMap>

    <select id="selectMinisterById" resultMap="ministerMap">
        SELECT * FROM minister,country WHERE mid =#{mid} AND minister.cid=country.cid
    </select>
```

-   **\<association/>标签:**在映射文件中用于体现出两个**实体对象间的关联关系**。
    -   property：指定被关联对象在主类(Minister类)中的属性，即Minister 类中的 country 属性
    -   javaType：被关联对象的类型
        -   javaType和ofType的区别:
            -   二者都是用来指定对象类型
            -   javaType指定**pojo中属性的类型**
            -   ofType指定的是**映射到list集合属性中pojo的类型**

##### 多表单独查询

```xml
    <!--多表单独查询-->
	<!--被关联的查询语句的动态参数来自于resultMap指定的column给出的参数cid,而cid参数来自使用此resultMap的select查询方法selectMinisterById的动态参数mid所指定的查询数据中-->
    <select id="selectCountryByMinisterCid" resultType="com.sqm.pojo2.Country">
        SELECT * FROM country WHERE cid=#{minister.cid}
    </select>

    <resultMap id="ministerMap" type="com.sqm.pojo2.Minister">
        <id column="mid" property="mid"/>
        <result column="mname" property="mname"/>
        <association column="cid" property="country" select="selectCountryByMinisterCid" javaType="Country"/>
    </resultMap>

    <select id="selectMinisterById" resultMap="ministerMap">
        SELECT * FROM minister WHERE mid =#{mid}
    </select>
```

### 3.1.3 自关联查询

-   自关联查询:自己既充当关联方,也充当被关联方,是 1:n或 n:1 的变型
-   将自关联分为两种情况来说。一种是当作 1:n讲解，即当前类作为一方，其包含多方的集合域属性。一种是当作 n:1 讲解，即当前类作为多方，其包含一方的域属性
-   自关联的数据库表如下:
    -   pid为parentid,即父标签的id,若pid为0则为顶级标签,无父标签
    -   此例是查询指定栏目的所有子孙栏目
-   ![newslaber](07_mybatis03.jpg)

#### 1. 以一对多方式处理

-   一方可以看到多方:例如在页面上点击父栏目，显示出其子栏目。再如，将鼠标定位在窗口中的某菜单项上会显示其所有子菜单项等
-   根据查询需求的不同，又可以分为两种情况：
    -   一种是指定pid直接通过递归调用一种方法查询所有子栏目与孙栏目
        -   通过指定pid查询:selectChildernByParentId
    -   另一种指定id查询本栏目,再通过查询子栏目的方法的递归调用来查询所有子栏目:
        -   通过指定id查询本栏目:selectNewsLabelById,再通过此id查询pid来查询所有子栏目:selectNewsLabelByParentId

##### selectChildernByParentId

-   查询出指定栏目的所有子孙栏目:**通过 select 语句的递归调用**实现查询所有下级栏目的功能
    -   查询结果的collection标签所指定的集合属性来自于递归调用的 selectChildrenByParentId
        查询
    -   第一次查询的collection标签中的column指定的动态参数来自于调用方法传递来的实际参数(即测试类中的参数),而之后的select查询的动态参数为上一次查询结果的id值

######1.  定义实体类

```java
public class NewsLabel1 {
    private Integer id;
    private String name;
    //关联属性,指定子栏目,即多方
    private Set<NewsLabel1> children;

    //getter and setter
    //toString()
```

######2. 修改dao接口

```java
public interface NewsLabelDao {
    //给出父栏目id,查询所有子孙栏目
    List<NewsLabel1> selectChildrenByParentId(int pid);
}
```

######3. 修改mapper映射

```xml
    <!--递归调用同一个方法:由给出的pid获取数据,第一次pid由test给出,之后的pid由collection标签的column指定为id,递归调用-->
    <resultMap id="newsLabelMap" type="NewsLabel1">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <collection column="id" property="children" ofType="NewsLabel1" select="selectChildrenByParentId"/>
    </resultMap>
    <!--pid的动态参数第一次来自于调用者方法的实参,之后都来自上次查询结果的id(递归)-->
    <select id="selectChildrenByParentId" resultMap="newsLabelMap">
      SELECT * FROM neswlaber WHERE pid=#{pid}
    </select>
```

######4. 修改测试类

```java
    //给出父栏目id,查询所有子孙栏目
    @Test
    public void test01() {
        int pid = 2;
        List<NewsLabel1> newsLabel1s = dao.selectChildrenByParentId(pid);
        for (NewsLabel1 newsLabel1 : newsLabel1s) {
            System.out.println(newsLabel1.toString());
        }
    }
```

##### selectNewsLabelById & selectNewsLabelByParentId

-   通过id查询出当前的栏目.再通过此id作为pid来查询子栏目,之后递归调用查询子栏目的方法

######1. 添加dao接口

```java
    //给出栏目id,查询此id的栏目及所有此栏目的子孙栏目
    List<NewsLabel1> selectNewsLabelById(int id);
```

###### 2. 添加mapper映射

-   在上一个mapper中添加

```xml
    <!--给出id后,第一次选出此id的栏目,之后再递归调用selectChildrenByParentId,将第一次返回的id作为动态参数递归查询-->
    <select id="selectNewsLabelById" resultMap="newsLabelMap">
        SELECT * FROM neswlaber WHERE id=#{id}
    </select>
```

######3. 修改测试类

```java
    //给出栏目id,查询符合此id的栏目及其所有子孙栏目
    @Test
    public void test02() {
        int id = 2;
        List<NewsLabel1> newsLabel1s = dao.selectNewsLabelById(id);
        for (NewsLabel1 newsLabel1 : newsLabel1s) {
            System.out.println(newsLabel1.toString());
        }
    }
```

#### 2. 以多对一方式处理

-   多方可以看到一方,即通过子查询父。例如在网页上显示当前页面的站内位置

##### 1.  定义实体类

```java
public class NewsLabel2 {
    private Integer id;
    private String name;
    private NewsLabel2 parent;
	//getter and setter
  	//toString()
```

##### 2. 定义dao接口

```java
    //根据pid查询所有父栏目,递归查询
    List<NewsLabel2> selectParentByParentId(int pid);
```

##### 3. 定义mapper映射

```xml
    <!--根据pid查询父栏目,递归查询-->
    <resultMap id="parentMap" type="NewsLabel2">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <association column="pid" property="parent" javaType="NewsLabel2" select="selectParentByParentId"/>
    </resultMap>
    <!--根据pid查询,返回id等于此pid的数据,递归查询-->
    <select id="selectParentByParentId" resultMap="parentMap">
        SELECT * FROM neswlaber WHERE id=#{pid}
    </select>
```

##### 4. 定义测试类

```java
    //给出pid,查询所有id为此pid的栏目,递归查询
    @Test
    public void test03() {
        int pid = 9;
        List<NewsLabel2> newsLabel2s = dao.selectParentByParentId(pid);
        for (NewsLabel2 newsLabel2 : newsLabel2s) {
            System.out.println(newsLabel2.toString());
        }
    }
```

### 3.1.4 多对多关联查询

-   多对多关系:由两个互反的一对多关系组成
    -   例如:一个学生可以选多门课程，而一门课程可以由多个学生选
    -   一般情况下，多对多关系都会通过一个**中间表**来建立,例如选课表

#### 1. 定义实体

-   在定义双向关联（双方均可看到对方的关联关系）的实体的 toString()方法时，只让一方的 toString()方法中可以输出对方，不要让双方均可输出对方。否则将会出现输出时的递归现象，程序报错

-   ```java
    public class Student {
        private Integer sid;
        private String sname;
        private Set<Course> courses;
    	//getter or setter
    	//toString()
      
    public class Course {
        private Integer cid;
        private String cname;
        private Set<Student> students;
    	//getter or setter
    	//toString() -- 不包含students
      
      
    ```

#### 2. 定义数据库表

```mysql
CREATE TABLE student(
  sid INT PRIMARY KEY AUTO_INCREMENT,
  sname VARCHAR(30) NOT NULL
)

CREATE TABLE course(
  cid INT PRIMARY KEY AUTO_INCREMENT,
  cname VARCHAR(30) NOT NULL
)

#中间表
CREATE TABLE selectcourse (
  id INT PRIMARY KEY AUTO_INCREMENT,
  sid INT NOT NULL ,
  cid INT NOT NULL 
)
```

#### 3. 定义dao层接口

```java
public interface StudentDao {
    //通过sid获取学生对象及他选的课程
    Student selectStudentBySid(int sid);
}
```

#### 4. 定义mapper映射

-   多对多关联关系也是通过映射文件\<resultMap/>的\<collection/>体现的。但是，需要注意的是 SQL
    语句中是对三张表的连接查询

```xml
    <!--通过sid选择学生并返回这个学生选择的课程-->
    <resultMap id="StudentMap" type="Student">
        <id column="sid" property="sid"/>
        <result column="sname" property="sname"/>
        <collection property="courses" ofType="Course">
            <id column="cid" property="cid"/>
            <result column="cname" property="cname"/>
        </collection>
    </resultMap>
    <select id="selectStudentBySid" resultMap="StudentMap">
        SELECT * FROM student,selectcourse,course WHERE student.sid=#{sid} AND student.sid=selectcourse.sid AND selectcourse.cid=course.cid
    </select>
```

#### 5. 定义测试类

```java
    //给出sid查找学生且同时返回学生选择的课程
    @Test
    public void test01() {
        int sid = 1;
        Student student = dao.selectStudentBySid(sid);
        System.out.println(student.toString());
    }
```

## 3.2 延迟加载

-   MyBatis 中的延迟加载，也称为懒加载
    -   指在进行关联查询时，**按照设置延迟规则推迟对关联对象的 select查询**。延迟加载可以有效的减少数据库压力
    -   MyBatis 的延迟加载只是**对关联对象的查询有迟延设置**,对于主加载对象都是直接执行查询语句

### 3.2.1 关联对象的加载时机

-   MyBatis 根据对关联对象查询的 select语句的**执行时机**，分为三种类型：
    -   直接加载、侵入式延迟加载与深度延迟加载
    -   **直接加载**:执行完主加载对象的 select 语句，**马上执行**关联对象的 select 查询
    -   **侵入式延迟**：执行对主加载对象的查询时，**不会执行**关联对象的查询,但是**当访问主加载对象的详情**时,会立即查询关联对象
        -   当关联对象的数据侵入到主加载对象的的详情重视,关联对象的详情相当于主加载对象的一部分
    -   **深度延迟**：执行对主加载对象的查询时，**不会执行**对关联对象的查询,访问主加载对象的详情时**也不会执行**关联对象的 select 查询;只有**当真正访问关联对象**的详情时才会查询关联对象
    -   **注意**:
        -   **延迟加载的应用要求，关联对象的查询与主加载对象的查询必须是分别进行的 select 语句，不能是使用多表连接所进行的 select 查询**
        -   多表连接查询实质上是查询一张表,因为只有一条查询语句;而多表分别查询则是一条select语句查询一个表,则可以应用延迟加载
        -   延迟加载设置，可以应用到一对一、一对多、多对一、多对多的所有关联关系查询中
-   下面以一对多(one2many)关联关系查询为例，讲解 MyBatis中的延迟加载应用

### 3.2.2 直接加载

#### 1. 修改主配置文件

-   在主配置文件的\<properties/>与\<typeAliases/>标签之间，**添加\<settings/>标签**，用于完成**全局参数**设置

-   在setting标签中,**全局属性 lazyLoadingEnabled 的值设置为 false，那么，对于关联对象的查询，将采用直接加载**

    -   即在查询过主加载对象后，会马上查询关联对象

    -   lazyLoadingEnabled 的**默认值为 false**，即直接加载

    -   ```xml
            <!--全局参数设置-->
            <settings>
                <!--延迟加载总开关-->
                <setting name="lazyLoadingEnabled" value="false"/>
            </settings>
        ```

-   对于主配置文件中的标签顺序,是固定的;不能随便写。在\<configuration/>标签上点击 F2，可查看它们的顺序及数量要求

-   在约束文件中:

    -   ```dtd
        <!ELEMENT configuration (properties?, settings?, typeAliases?, typeHandlers?, objectFactory?, objectWrapperFactory?, reflectorFactory?, plugins?, environments?, databaseIdProvider?, mappers?)>
        ```

    -   ？：表示子标签可以没有，若有的话，最多只能有一个。即小于等于 1。

    -   \* ：表示子标签可以没有，也可以有多个。即大于等于0。

    -   \+ ：表示子标签最少要有一个。即大于等于1。

    -   没符号：表示有且只能有一个。即等于 1

#### 2. 修改测试类

-   直接加载时,在断点处对country 表进行了查询，对minister 表也同时进行了查询

-   ```java
        //通过id查询国家并显示国家信息和部长信息
        @org.junit.Test
        public void test01() {
            int cid = 2;
            Country country = dao.selectCountryById(cid);
            //此处添加断点
            System.out.println(country.getCname());
            System.out.println(country.getMinisters().toString());
    ```

### 3.2.3 深度延迟加载

-   修改主配置文件的\<settings/>，将**延迟加载开关 lazyLoadingEnabled** 开启(置为 true),将侵**入式延迟加载开关 aggressiveLazyLoading**关闭(置为 false)

-   ```xml
        <!--全局参数设置-->
        <settings>
            <!--延迟加载总开关-->
            <setting name="lazyLoadingEnabled" value="true"/>
            <!--侵入式延迟加载开关-->
            <setting name="aggressiveLazyLoading" value="false"/>
        </settings>
    ```

-   其它代码均不作改变。此时再运行会发现，只有当代码执行到输出Minister对象详情时,底层才执行了 select 语句对 minister 表进行查询。这已经将查询推迟到了不能再推的地步， 故称为深度延迟加载

### 3.2.4 侵入式延迟加载

-   修改主配置文件的\<settings/>，将延迟加载开关 lazyLoadingEnabled 开启（置为 true,将侵入式延迟加载开关 aggressiveLazyLoading也开启（置为 true，**默认为 true**）

-   ```xml
       <!--全局参数设置-->
        <settings>
            <!--延迟加载总开关-->
            <setting name="lazyLoadingEnabled" value="true"/>
            <!--侵入式延迟加载开关-->
            <setting name="aggressiveLazyLoading" value="true"/>
        </settings>
    ```

-   其它代码均不作改变。此时运行会发现，当代码执行断点处语句时会立即查询country表，但没未查询 minister表。说明现在的延迟加载已经启动

-   但继续执行下一语句，即对 Country对象的详情进行访问时，对 minister表也进行了查询。因为该延迟策略已经**将主加载对象的关联属性值也作为主加载对象的基本信息**了，而前面已经查询出了主加载对象的基本信息，但其关联对象基本信息尚无。所以，马上进行对minister 表的查询

-   该延迟策略**使关联对象的数据侵入到了主加载对象的数据中**，所以称为侵入式延迟加载

-   若 lazyLoadingEnabled为 false，则 aggressiveLazyLoading 无论取何值， 均不起作用

    ​

### 3.2.5 延迟加载策略总结

-   | 加载策略    | lazyLoadingEnabled | aggressiveLazyLoading |
    | ------- | ------------------ | --------------------- |
    | 直接加载    | false              | -                     |
    | 深度延迟加载  | true               | false                 |
    | 侵入式延迟加载 | true               | true                  |

