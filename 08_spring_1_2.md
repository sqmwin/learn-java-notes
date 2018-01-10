[TOC]

SpringTest框架学习笔记

- 此笔记通过教程一步一步学习而记录,有bug很正常

# 1. Spring简介

- spring为代码"解耦"
  - 主业务逻辑:项目专用业务,复用性低
  - 系统级业务逻辑:日志,安全,事务等,无专用业务,复用性高

## 1.1 spring特点

- 非侵入式:不会在业务逻辑(POJO层)上出现
- 容器
- IOC:Inversion fo Control:创建实例由spring完成而不是调用者
  - 控制反转的实现方式有依赖查找和依赖注入
    - 依赖查找:Dependency Lookup
    - 依赖注入:Dependency Injection:spring使用,spring与bean之间通过配置文件联系
- AOP:Aspect Orent Programming:面向切面编程

#2 spring快速入门

###2.1.1 导包

1. spring-framework-5.0.2.RELEASE
   1. 至少是sping框架的四个基本包:beans,core,context,spEL(expression)

   2. maven中引入spring

      1. ```xml
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-core</artifactId>
             <version>5.0.2.RELEASE</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-context</artifactId>
             <version>5.0.2.RELEASE</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-beans</artifactId>
             <version>5.0.2.RELEASE</version>
         </dependency>
         <dependency>
             <groupId>org.springframework</groupId>
             <artifactId>spring-expression</artifactId>
             <version>5.0.2.RELEASE</version>
         </dependency>
         ```
2. 三个依赖库
   1. commons-logging.jar
      1. 日志记录与的规范,相当于slf4j.jar的作用

   2. log4j.jar
      1. 日志记录的实现

   3. junit.jar
      1. 测试包

   4. maven的pom.xml

      -   ```xml
                 <dependency>
                     <groupId>junit</groupId>
                     <artifactId>junit</artifactId>
                     <version>4.12</version>
                 </dependency>
                 <dependency>
                     <groupId>commons-logging</groupId>
                     <artifactId>commons-logging</artifactId>
                     <version>1.2</version>
                 </dependency>
                 <dependency>
                     <groupId>log4j</groupId>
                     <artifactId>log4j</artifactId>
                     <version>1.2.17</version>
                 </dependency>
           ```

###2.1.2 定义接口与实体类

- 也就是service方法,业务接口与业务类

### 2.1.3创建spring配置文件

- idea的右键菜单中就有


- 名称建议:applicationContext.xml

- 配置文件的约束为`%spring%/schema/beans/spring-beans.xsd`

- ```xml
      <!--定义一个实例对象,一个实例对应一个bean-->
      <bean id="studentService" class="com.sqm.service01.IStudentServiceImpl"/>
  id - 此bean实例的唯一标识
  class - 此bean所属的类,只能是类而不能是接口
  ```

###2.1.4 定义测试类

```java
    @org.junit.Test
    public void test(){
        //获取容器
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //从容器中获取对象
        IStudentService service = (IStudentService) context.getBean("studentService");
        service.some();
    }
```

- spring容器:ApplicationContext用于加载spring配置文件

  - 配置文件在项目中的类路径下:通过`ClassPathXmlApplicationContext(String path)`加载

    - maven的类路径为资源路径resources

  - 配置文件在本地目录中:通过`FileSystemXmlApplicationContext(String absoultPath)`加载

  - 配置文件在项目的根路径下:

    - ```java
      ApplicationContext context = new FileSystemXmlApplicationContext("applicationContext.xml");
      //这里的地址是以项目为基准的相对的地址
      ```

- ~~BeanFactory接口容器~~:也可用于生成spring容器,此接口是ApplicationContext接口的父类**(此方法过时了)**

  - 使用实现类XmlBeanFactory加载配置文件

  - XmlBeanFactory中的构造方法参数Resource为spring配置文件

    - ```java
      public XmlBeanFactory(Resource resource)
      ```

    - Resource是一个接口,其实现类有ClassPathResource(指定类路径的Resource文件)与FileSystemResource(指定根目录或本地磁盘文件)

      - ```java
        构造:
        public ClassPathResource(java.lang.String path)
        public FileSystemResource(java.io.File file)
        ```

    - ​

  - 两种容器的区别

    - 两种容器对象不是同一个对象,即不是同一个容器,**装配时机不同**
    - ApplicationContext容器在容器初始化时将所有对象都装配好,"饿汉式"
      - 执行效率高,占用内存高
    - ~~BeanFactory容器~~采用延迟加载策略,在**第一次**调用getBean()时装配对应对象

## 2.2 Bean的装配

- 即Bean对象的创建,容器根据代码要求创建Bean对象后再传递给代码的过程

### 2.2.1 默认装配方式

- getBean()**从容器中**获取指定对象,先调用无参构造器

### 2.2.2 动态工厂Bean

- 通过工厂类创建Bean实例而不是直接使用spring容器来装配Bean对象,此时工厂类将与Bean类耦合在一起

  1. 将动态工厂Bean作为普通Bean使用

     - 在配置文件中注册动态工厂Bean,容器通过getBean获取动态工厂对象,再由动态工厂对象获取目标对象;**此时目标对象不由spring容器来管理**

     - ```xml
       <bean id="factory" class="com.sqm.service02.ServiceFactory"/>
       ```

     - ```java
       public class ServiceFactory{
       	public IUserService getUserSedrvice{
           	return new UserServiceImpl();
           }
       }
       ```

     - 缺点是工厂类与目标类耦合在一起,测试类也与工厂耦合

  2. 使用Spring的动态工厂Bean

     - Spring有专门的属性定义来使用动态工厂创建Bean

       - bean标签中的factory-bean属性指定**此Bean的**工厂Bean,factory-method属性指定创建此类对象的工厂中的方法;当工厂Bean和目标Bean都配置后,此时测试类中不需要获取工厂Bean对象,可直接获取目标Bean对象

       - ```xml
         <bean id="factory" class="com.sqm.service02.ServiceFactory"/>
         <bean id="userService" factory-bean="factory" factory-method="getUserService"/>
         ```

### 2.2.3 静态工厂Bean

- 通过工厂模式的静态工厂来创建实例Bean:创建Bean的方法是静态的

- 此时**无需在配置文件中配置工厂Bean,**,但仍然需要指定目标对象Bean,此时bean的class属性值为工厂类,factory-method为静态方法,不指定factory-bean了

  - ```xml
    <bean id = "userService2" class="com.sqm.service02.ServiceFactory" factory-method="getUserService"/>
    ```

### 2.2.4 容器中的bean作用域

- 通过bean标签的scope属性指定作用域,以下是能取的5中作用域值:

  1. **singleton**:单态模式,默认是此模式;在整个spring容器中,此bean是单例
  2. **prototype**:原型模式;每次getBean()都是新的实例
  3. request:对每个HTTP请求,生成一个新的实例
  4. session:对每个不同的HTTPsession,生成一个新的实例
  5. global session:当使用portlet集群时有效,多个web应用共享一个session;一般应用中等效于session

- scope值为3,4,5作用域时只针对web应用中的spring,才有效

- scope="singleton"时,此bean在容器创建时就装配好

- scope="prototype"时,使用实例时才装配

- ```xml
  <bean id ="userService3" class="com.sqm.serive02.UserServiceImpl" scope="singleton"/>
  <bean id ="userService4" class="com.sqm.serive02.UserServiceImpl" scope="prototype"/>
  ```

### 2.2.5 bean后处理器

- 一种特殊bean,容器中所有bean在初始化时,均会**自动执行**该类的两个方法,因为是自动执行无需程序调用,所以无需指定id属性

- 在bean后处理器的方法中,只要对bean类与类中的方法进行判断,即可扩展或增强指定的bean的指定方法;

- 代码中自定义bean后处理器类,是**实现BeanPostProcessor接口**的类

  - 接口中两个方法分别在目标bean的初始化完毕之前和完毕之后执行,返回功能被扩展或加强的目标bean

  - ```java
    //初始化之前自动调用
    public Object postProcessBeforeInitialization(Object bean,String beanId) throws BeansException
    //初始化之后自动调用
    public Object postProcessAfterInitialization(Object bean,String beanId) throws BeansException
    bean - 系统即将初始化的bean实例
    beanId - 该bean的id值,若没有id则为name值
    两个方法返回bean(即在两个方法中加强后的原bean对象)
    ```

- 例如自定义bean后处理器,重写初始化后执行的方法,加强名为some的方法

- ```java
  //此时参数bean被fianl修饰,一旦赋值则无法改变
  @Override
  public Object postProcessBeforeInitialization(final Object bean,String beanId) throws BeansException {
  	//只针对id为studentService的bean对象
      if("studentService".equals(beanId){
  		//通过动态代理加强bean对象
        	//Proxy.newProxyInstance(ClassLoader classLoader,Class[] interfaces,Method handler)
          Object proxy = Proxy.newPorxyInstance(bean.getClass().getClassLoader(),bean.getClass().getInterfaces(),new InvocationHandler(){
  			//动态代理加强原方法
              public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
                  if("some".equals(method.getName())){
                    //这里面是加强的方法
  					System.out.println("目标方法执行开始时间" + System.currentTimeMillis());
                    		Object result = method.invoke(bean,args);
  					System.out.println("目标方法执行结束时间" + System.currentTimeMillis());
                    		return result;
                  }
                	//不加强的方法仍然按之前的继续
                	return method.invoke(bean,args);
              }
          });
        	//若为studentService则启用动态代理且返回加强后的对象proxy
        	return proxy;
      }
  	return bean;
  }
  ```

- ​

### 2.2.6 bean的生命始末

- bean的初始化后的生命行为和销毁前的生命行为都可以定制

  1. 首先在bean中定义一系列方法,**方法名随意的public void 方法**
  2. 其次在配置文件的对应bean标签内添加属性
     1. init-method属性:指定初始化的方法名
     2. destroy-method属性:指定销毁的方法名
        1. 此属性需满足此bean为单例,即scope为默认或singleton;同时确保容器关闭,ApplicationContext没有close()方法,但其实现类有,**将其强转为实现类对象,或者直接创建实现类对象`**

- ```xml
  <bean class="com.sqm.service.StudentService" id="studentService" init-method="setUp" destroy-method="tearDown"/>
  ```

- ```java
  //...
  //测试类执行destroy-method前,容器需要被关闭
  ((FileSystemXmlApplicationContext)context).close();
  ```

### 2.2.7 bean的生命周期

1. 调用无参构造创建无参对象
2. 调用参数的setter,为属性注入值
3. 若bean实现了**BeanNameAware接口**,则会执行接口方法`setBeanName(String beanId)`,**获取bean在容器中的id**
4. 若bean实现了**BeanFactoryAware接口**,则会执行接口方法`setBeanFactory(BeanFactory beanFactory)`,获取bean的工厂对象Bean Factory
5. 若定义了Bean后处理器**BeanPostProcessor**,则执行接口方法初始化前执行方法`postProcessBeforeInitialization()`
6. 若bean实现了**InitializationBean接口**,则执行接口方法`afterPropertioesSet()`,该方法在所有bean的set方法执行后执行,是**bean初始化结束的标志**,表示bean已经实例化完成
7. 执行init-method方法,若设置
8. 若定义了Bean后处理器**BeanPostProcessor**,则执行接口方法初始化后执行方法`postProcessAfterInitialization()`
9. 执行业务方法,如some(),other()
10. 若bean实现了**DispoableBean接口**,则执行接口方法destroy()
11. 执行destroy-method方法,若设置

### 2.2.8 bean标签的id属性与name属性

- 通常用id命名bean标签,若没有id属性时,使用name命名bean标签;id属性值**遵循xml对id的命名规范**(字母开头,可包含字母,数字,下划线,连字符,句号,冒号),而name属性值可以是**各种字符**

## 2.3 基于XML的DI

###2.3.1 注入分类

- Dependency Injection
- **注入**:由容器向bean对象的**属性进行初始化**
  - **设值注入**:无参构造后再用setter设置对象属性
  - **构造注入**:带参构造直接实例化对象

#### 设值注入

- 简单直观,再spring的DI中大量使用

- bean的子标签**property**

  - 若属性的类型为其他beanId,则通过ref属性来指定另一个bean对象

- ```java
  public class Student{
  	private String name;
  	private int age;
  	private School school;
  	setter()...
  	toString()...
  }

  public class School{
  	private String name;
  	setter()...
  	toString()...
  }
  ```

- ```xml
  <bean class="com.sqm.pojo.Student" id="sqmStudent">
  	<property name = "name" value = "sqm"/>
  	<property name = "age" value = "21"/>
  	//当属性中的值为另一个bean的值时,通过ref指定另一个bean的对象
  	<property name = "school" ref = "sqmSchool"/>
    	//也可以使用ref子标签的bean属性来表示
    	<property name = "school">
  		<ref bean="sqmSchool"/>
  	</property>
  </bean>

  <bean class="com.sqm.pojo.School" id="sqmSchool">
  	<property name ="name" value="swjtu"/>
  </bean>
  ```

- ​

#### 构造注入

- 使用带参构造器设置依赖关系

- bean的子标签**constructor-arg**:要保证**赋值顺序与构造器中参数顺序一致**

  - 其特别属性如type用于指定参数数据类型,若为非基本类型要写**全限定性类名**,如com.sqm.pojo.School,可写可不写

- ```java
  public class Student{
  	private String name;
  	private int age;
  	private School school;
  	public Student(String name,int age,School school){
  		this.name = name;
  		this.age = age;
  		this.school = school;
  	}
  }
  ```

- ```xml
  <bean class = "com.sqm.pojo.Student" id = "sqmStudent">
  	<constructor-arg name = "name" value = "sqm"/>
  	<constructor-arg name = "age" value = "25"/>
  	<constructor-arg name = "school" ref = "sqmSchool"/>
  </bean>

  <bean class = "com.sqm.pojo.School" id = "sqmSchool"/>
  ```

- ​

### 2.3.2 命名空间注入(了解)

- namespace injection
- 分为**p命名空间注入**(设值注入方式,要有setter)与**c命名空间注入**(构造注入方式,要有带参构造器)

#### p命名空间注入-命名空间设值注入parameter namespace dependency injection

1. 在配置文件中添加p命名空间约束,其约束通过doc查找"p-namespace"

2. 在bean标签中使用p命名空间的属性

   1. ```xml
      <bean class = "com.sqm.pojo.Student" id = "sqmStudent" p:name="sqm" p:age="25" p:school-ref="sqmSchool"/>
      语法:
      p:bean属性="值"   一般属性
      p:bean属性-ref="值"	此属性为其他beanId
      ```

#### c命名空间注入-命名空间构造注入constructor namespace dependency injection

1. 在配置文件中添加c命名空间约束,其约束通过doc查找"c-namespace"

2. 在bean标签中使用c命名空间的属性

   1. ```xml
      <bean class = "com.sqm.pojo.Student" id = "sqmStudent" c:name="sqm" c:age="25" c:school-ref="sqmSchool"/>
      语法:
      c:bean属性="值"   一般属性
      c:bean属性-ref="值"	此属性为其他beanId
      ```

### 2.3.3 集合属性注入

```java
//集合统一类
public class MyCollections {
	//数组
	private String[] strings;
	//List集合
	private List<Student> students;
	//set集合
	private Set<String> mySet;
	//Map键值对
	private Map<String,Integer> myMap;
	//properties
	private Properties myProperties;
	
}
```

- 在配置文件中定义一个MyCollections的bean对象

- ```xml
  <bean class="com.sqm.collection.MyCollections" id = "myCollection"></bean>
  ```

- ​

#### 1.为数组注入

- bean标签的子标签property中,加入代表属性是数组的子标签array,array的子标签value则为数组中的元素

```xml
//在bean标签的子标签中加入子标签
<property name ="strings">
	<array>
      <value>a</value>
      <value>b</value>
      <value>c</value>
  	</array>
</property>
```

#### 2. 属性为list集合

- property子标签为**list**标签,list标签的子标签为每一个list对象

  - list中的对象类型若为bean对象,用`<ref bean="beanId"/>`表示
  - list中的对象类型若为基本类型,用`<value>xxx</value>`表示

- ```xml
  <property name ="students">
  	<list>
        <ref bean ="sqmStudent"/>
        <ref bean ="sqmStudent2"/>
        <ref bean ="liliStudent"/>
    	</list>
  </property>  
  ```

- ​

#### 3.属性为set集合

- 除property子标签为**set**标签外,其余与list集合注入相同,set中的元素不能重复

- ```xml
  <property name ="students">
  	<set>
        <value>小学</value>
        <value>中学</value>
        <value>大学</value>
   	</set>
  </property>  
  ```

- ​

#### 4.属性为Map键值对

- property子标签为**map**标签,map标签的子标签entry为每一个键值对(key,value)

- ```xml
  <property name ="students">
  	<map>
        <entry key="height" value="180"/>
        <entry key="weight" value="80"/>
    	</map>
  </property>  
  ```

- ​

#### 5. 属性为properties键值对

- property子标签为**props**标签,props标签的子标签prop为每一个元素,与map不同的是,prop的key为属性,而value为标签内值

- ```xml
  <property name ="students">
  	<props>
        <prop key="tel">180</prop>
        <prop key="address">成都</prop>
    	</props>
  </property>  
  ```

### 2.3.4 域属性自动注入

- 域属性可以通过bean标签设置**autowire**属性值,为域属性进行隐式自动注入,根据自动注入判断标准分为根据名称自动注入(byName)与根据类型自动注入(byType)

- 自动注入的id要与bean中的参数名相同,如school

- ```xml
  <bean class="com.sqm.pojo.School" id="school" p:name="school"/>

  <bean class = "com.sqm.pojo.Student" id = "autoWireStudent" autowire="byName" p:name="autowire" age="10"/>
  //无需设置被自动注入的域属性,如school
  ```

- 通过autowire="byType"自动注入域属性时,保证数据类型相同的同时**只能有一个bean对象**,否则不知道调用哪一个

### 2.3.5 使用SpEL注入

- Spring Expression Language,Spring表达式语言,在配置文件中为bean属性注入值时可使用SpEL表达式的计算结果

- SpEL表达式格式:**以#开头,接上一对大括号**:`#{...} `

- ```xml
  <bean id="sqmPerson" class="com.sqm.pojo.Person">
  	<property name = "personName" value="sqm"/>
  	//personAge的值是Math的静态方法计算后的值
  	<property name = "personAge" value="#{T(java.lang.Math).random()*30}"/>
  </bean>
   <bean id="sqmStudent" class="com.sqm.pojo.Student">
  	//studentName的值为sqmPerson对象的personName值
  	<property name = "studentName" value="#{sqmPerson.personName}"/>
  	//studentAge的值是sqmPerson对象的一个方法
  	<property name = "studentAge" value="#{sqmPerson.computeAge()}"/>
      <!--school的值是另一个bean的Id-->
      <property name="school" value="#{school}"/>
  </bean>
  ```

### 2.3.6 内部bean注入

- 将bean的定义放入要调用对象的内部,即可保证可调用bean,也可保证测试代码无法直接访问到此bean

- ```xml
      <bean id="sqmAsStudent2" class="com.sqm.pojo.Student2" p:studentName="sqm" p:studentAge="22">
          <property name="school">
              <!--属性采用内部注入bean-->
              <bean class="com.sqm.pojo.School">
                  <property name="name" value="swjtu"/>
              </bean>
          </property>
      </bean>
  ```

### 2.3.7 同类抽象Bean注入

- 若由多个bean对象属于**同一类**Class,可建立一个此类的抽象bean,此抽象bean不定义的属性可以由子bean标签继承更改,此抽象bean定义了属性值的属性则为公共属性,不可更改

- 抽象父bean: bean标签的属性**abstract="true"**时则为抽象bean

  |→子bean1	子bean的class属性不指出,而使用**parent属性指向父bean**:`parent="父beanId"`

  |→子bean2  等等

- 抽象bean不能由getBean()方法获取,一般用于被继承;即使不是抽象bean(abstract="false")也可以被继承

- ```xml
      <!--同类抽象继承bean-->
      <!--继承此学生类的bean的学校都是swjtu-->
      <bean class="com.sqm.pojo.Student2" id="studentBase" abstract="true" p:school="swjtu"/>
      <bean parent="studentBase" id="student1" p:studentName="sqm" p:studentAge="12"/>
      <bean parent="studentBase" id="student2" p:studentName="lili" p:studentAge="22"/>
  ```

### 2.3.8 异类抽象Bean注入

- 当多个bean对象不属于同一类,但有同样参数的属性(参数类型与参数名),也可使用抽象bean
- 此时抽象父bean的class不指出,子bean的class由自己负责

### 2.3.9 为应用指定多个spring配置文件

#### 1. 平等关系的配置文件

- 把这些配置文件的path放入String[]中,作为applicationContext的参数

- ```java
  String[] paths={"path1","path2","paht3"};
  ApplicationContext context = new FileSystemXmlApplicationContext(paths);
  ```

#### 2. 包含关系的配置文件

- 各个配置文件有一个总配置文件,总配置文件通过import标签引入其他子文件,这样只需初始化总文件的applicationContext

- ```xml
  <beans ...>
  	<import resource="classpath:com/sqm/applicationcontext/spring-student.xml"/>
  	<import resource="classpath:com/sqm/applicationcontext/spring-teacher.xml"/>
  </beans>
  ```


## 2.4 注解形式的DI

- 对DI依赖注入使用注解不需要在spring配置文件中声明bean实例

- 但需要多导入一个jar包:后台需要用到AOP的编程

  - spring-aop-x.x.x.RELEASE.jar

  - 配置文件头需要更改(这个不需要记.添加标签时会自动增加)

    - ```xml
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:p="http://www.springframework.org/schema/p" xmlns:context="http://www.springframework.org/schema/context"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
      ```

  - maven中pom.xml的设置

    - ```xml
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-aop</artifactId>
          <version>5.0.2.RELEASE</version>
      </dependency>
      ```

- 需要在spring配置文件中配置**组件扫描器**component-scan,用于扫描指定包中的注解

  - ```xml
     <context:component-scan base-package="com.sqm.pojo"/>
     ```

###2.4.1 定义bean注解@Component

- @Component注解的value属性用于指定该bean的id值,value可以省略

  - ```java
    @Component("student")
    public class Student{...}
    ```

- spring提供了其余三个等效注解,但**扩展后各自功能不同**

  - **@Repository**:用于对DAO实现类进行注解
  - **@Service**:用于对Service实现类进行注解
  - **@Controller**:用于对Controller实现类进行注解

### 2.4.2 bean的作用域@Scope

- ```java
  @Scope("prototype")
  @Component("userService")
  public class UserServciceImpl implements IUserService{...}
  ```

### 2.4.3 基本类型属性注入@Value

- 在基本数据类型的属性上使用,使用注解完成属性注入,无需setter,若有setter也可加到setter上

- ```java
  @Value("sqm")
  private String name;
  @Value("22")
  private int age;
  ```

### 2.4.4 按类型自动注入域属性@Autowired

- 在域属性上使用,相当于bean标签的autowire="byType"

- ```java
  @Autowired
  private School school;
  ```

### 2.4.5 按名称自动注入域属性@Autowired与@Qualifier

- 在域属性上同时使用两个注解,相当于bean标签的autowire="byName";@Qualifier注解指定了自动注入的@Componment的值

- ```java
  @Component("school")
  public class School {...}

  @Autowired
  @Qualifier("school")
  private School school
  ```

- @Autowired的一个属性为required,默认值为true,表示当匹配失败时终止程序运行;设为false会忽略匹配失败,未被匹配的属性值为null

- ```java
  @Autowired(required=false)
  ```

### 2.4.6 域属性注解@Resource

- @Resource注解用于bean的属性匹配其他bean,对于域属性来说,既可以按名称匹配bean,既可以按名称匹配bean,也可以按类型匹配bean

- 若@Resource注解不带任何参数,则会按类型匹配其他相同类型的bean来注入

- 若@Resource注解指定name属性,则按名称匹配域属性

- ```java
  @Resource
  private School school;

  @Resource("mySchool")
  private School school;
  ```

### 2.4.7 bean的生命始末注解@PostConstruct与@PreDestroy

- **@PostConstruct**等效于**init-method**属性,指定被修饰的方法在**初始化时自动加载**

- **@PreDestroy**等效于**destroy-method**属性,指定被修饰的方法在**销毁前加载**,前提是容器被关闭;

- ```java
  @PostConstruct
  public void setUp() {...}

  @PreDestroy
  public void tearDown() {...}
  ```

- ​

### 2.4.8 使用JavaConfig进行配置(了解)

- JavaConfig用于完成bean装配的spring配置文件,即spring容器;此时配置文件不是xml文件,而是自行编写的Java类
- 使用流程:
  1. 定义实体类:School类与Student类

  2. 定义JavaConfig类
     - 对于一个POJO类,在类上使用**@Configuration**注解,将此类作为一个spring容器,即applicationContext

     - 在此Java Config类的方法上使用**@Bean**注解,使被修饰的方法返回的结果变为指定名称的bean实例

     - Java Config类中,也可以完成域属性的自动注入

     - ```java
       //声明将此类作为applicationContext
       @Configuration
       public class MyJavaConfig {
       	
       	//声明将此方法的返回值作为一个bean
       	@Bean(name="sqmSchool")
           public School initialSchool(){
             	//直接使用带参构造
       		return new School("swjtu");
           }
         
       	//采用byType的自动注入,前提是要注入的bean对象不能有第二个同类bean
       	//若采用byName自动注入,要求被注入的bean对象的name要与类名相同,如school的类School
       	@Bean(name="sqmStudent",autowire=Autowire.BY_TYPE)
           public Student initialStudent{
       		return new Student("sqm",22);
           }
       }
       ```

     - ​

  3. 定义spring的配置文件

     -   将context:component-scan标签的 base-package属性指向所配置的JavaConfig类所在的包


     -   ```xml
         <beans...>
         	<context:component-scan base-package="com.sqm.config"
         </beans>
         ```
    
    4. 定义测试类


### 2.4.9 使用Spring的test包来简化测试类

-   spring的junit包来测试,无需在测试类中创建容器context,无需在测试类中通过容器getBean获取对象;这些工作**由junit注解配合域属性的自动注入注解**共同完成

-   使用流程:

    1.  导入jar包

        -   导入junit-4.x.jar

        -   导入sprin-test-x.x.x.RELEASE.jar,此包是spring与junit的整合jar包

        -   maven中pom.xml的配置

            -   ```xml
                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-test</artifactId>
                    <version>5.0.2.RELEASE</version>
                    <scope>test</scope>
                </dependency>
                ```

    2.  定义测试类

        -   在类头添加两个注解:
            1.  **@RunWith(SpringJUnit4ClassRunner.class)**:指定类的运行环境
            2.  **@ContextConfiguration(locations="地址")**:指定配置文件的位置
        -   对于需要从容其中获取的bean对象,使用byType或byName方式自动注入则可自动测试

### 2.4.10 注解与xml共同使用

-   注解直观配置方便,但是注解以硬编码集成到代码中,修改的话需要修改代码
-   xml修改则无需修改代码,只需重启服务器即可
-   注解与xml同时使用**xml的优先级高于注解**,使用xml时bean类要有带参构造器或者setter

