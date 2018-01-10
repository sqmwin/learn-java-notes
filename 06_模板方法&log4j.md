[TOC]

---

#1. 设计模式之模板方法设计模式

## 1.1 模板方法设计模式

- 通过指定一个模板类,这个模板类中定义一个执行步骤的方法,定义每一个步骤的方法,再对每一个步骤进行个性化定义
- 模板方法定义了一个操作中某种算法的框架,将实际步骤延迟到子类中实施;使算法结构不变的前提下,实现步骤相同而实际操作不同
  - 例如:购物流程模板为:登陆网站login→挑选商品choose→付款pay
    - 实际操作中登陆网站操作相同,挑选商品操作不同,付款操作只有几个选项,操作也不同

## 1.2 模板方法程序构成

- 模板方法中存在一个**模板父类,本身是一个抽象类**;其中包含两种方法:模板方法和步骤方法
  - 具体的实现由继承模板父类后子类来实现具体的方法
- **模板方法**:实现某种算法的方法步骤;此方法中依照算法步骤依次调用步骤方法.客户类实现功能时只需调用模板方法即可执行所有步骤;例如购物流程模板方法为执行购物的流程;
- **步骤方法:**完成模板方法的每一个阶段性的方法;每一个步骤方法完成某一特定的,完成总算法一部分的功能;例如登陆网站为一个步骤方法,挑选商品也为一个步骤方法
  - 步骤方法有三种类型:
    - **抽象步骤方法**:在模板父类中是抽象的,意思就是在子类实现时**必须实现的方法**,此步骤针对每个子类都要个性化定义;例如挑选商品,每个人挑选的都不一样
    - **最终步骤方法**:在模板父类中被final限定的方法,已经在父类中实现且在子类中无法重写,对于所有子类,执行模板方法时,执行到此步骤,每个对象执行的内容都一样;例如登陆网站的操作都一样
    - **钩子步骤方法**:在模板父类中此步骤方法给出了默认的实现方法,子类可以重写也可以不重写

## 1.3 模板方法程序举例

- 实现网上购物的模板方法设计:

  1. 定义模板父类:

     - ```java
       public abstract class ShoppingService() {
       	//定义模板方法
           public void shopping {
       		//依次调用步骤方法
            	login();
             	choose();
             	pay();
           }
         
         	//定义每一个步骤方法
         	//最终方法子类不能重写,对每一个子类都一样
         	public final void login() {
           	//从登陆框登陆
           }
         	
         	//抽象方法:子类必须重写,每一个子类都个性化定义
           public abstract void choose() { }
         
         	//钩子方法:子类可以选择是否重写,此方法有默认执行方法
           public void pay() {
       		//默认使用支付宝付款
           }	
       }
       ```

  2. 定义具体实现子类

     - ```java
       //买衣服类
       public class ShoesShoppingService() {
       	@Override
           public void choose() {
       		//选择到衣服
           }
         	//使用默认方法pay()
       }
         

       //买汽车类
       public class CarShoppingService() {
       	@Override
           public void choose() {
       		//选择到汽车
           }

         	@Override
           public void pay() {
       		//支付宝限额,使用银行卡分期付款
           }
       }
       ```

  3. 定义测试类(实际工程时是控制器类)

     - ```java
       ShoesShoppingService shoes = new ShoesShoppingService();
       //买鞋子
       shoes.shopping();
       CarShoppingService car = new CarShoppingService();
       //买汽车
       car.shopping()`;
       ```

     - ​


---

# 2 log4j 与log4j 2

- 软件的日志必不可少,日志用于记录软件的行为
  - 软件日志分为调试日志,运行日志,异常日志
- 用于日志记录的技术有:jdk的logger(很弱),apache的log4j(可用)和log4j 2(高性能)
- log4j : 全称 log for java

## 2.1 log4j基础

- log4j [下载](http://logging.apache.org/log4j/2.x/)
- 同时包含log4j 1.x和 log4j 2

### 2.1.1 日志级别

- 为了便于对日志信息进行管理显示,根据需要有的日志无需显示,有的日志必须先是,因而对日志进行分级管理
- 日志级别由高到低有6个级别
  - **fatal(致命),error,warn,info,debug,trace(堆栈)**
- 通过**日志级别控制**日志是否输出,通过**日志输出控制文件**设定日志的级别

### 2.1.2 日志输出控制文件

- log4j中的配置文件是properties格式,log4j 2 中的配置文件是xml或json格式
- 配置文件的路径在根目录即可,若使用Maven或Gradle,则是在resources文件夹下
- 日志输出控制文件组成:
  1. 日志信息的输出**位置**:输出位置是控制台还是文件
  2. 日志信息的输出**格式**:显示格式,是以怎样的字符串
  3. 日志信息的输出**级别**:日志信息显示哪些级别的日志

## 2.2 log4j

#### log4j在maven中的配置

- log4j

- ```xml
  //pom.xml
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

- 如果存在的组件中使用log4 1.x,而又想使用log4j 2 ,删除log4j 1.x的依赖使用如下的依赖

- ```xml
  //pom.xml
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-1.2-api</artifactId>
      <version>2.10.0</version>
    </dependency>
  ```

- log4j 2

- ```xml
  //pom.xml
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-api</artifactId>
      <version>2.10.0</version>
    </dependency>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.10.0</version>
    </dependency>
  ```

- ​

### 2.2.1 log4j 实现步骤

1. 导入jar包

   1. log4j依赖一个jar包 - log4j-1.2.jar

2. 创建配置文件log4j.properties,路径在src目录

3. 代码中实现日志记录

   1. **在要输出日志的类中**创建日志对象Logger(org.apache.log4j.Logger)

      1. ```java
         //Logger类的静态方法getLogger(Class thisClass)
         Logger logger = Logger.getLogger(Test.class);
         ```

   2. 通过Logger的方法设置日志输出语句

      1. ```java
         //logger对象的方法
         //logger.xxx(String xxxMessage)
         logger.debug("debug的信息");
         logger.info("info的信息");
         logger.error("error的信息");
         ```

      2. 这些输出语句根据配置文件中设定的日志级别决定输出在指定的位置,**输出结果会输出制定级别及更高级别的信息**,如指定info级别,会输出fatal,error,warn,info级别的信息,而不会执行debug()等方法

### 2.2.2 日志输出控制文件(日志配置文件)

- 日志输出控制文件组成:
  1. 日志信息的输出**位置**:输出位置是控制台还是文件
  2. 日志信息的输出**格式**:显示格式,是以怎样的字符串
  3. 日志信息的输出**级别**:日志信息显示哪些级别的日志


- log4j.properties:由两个对象组成:日志附加器与根日志
- **根日志**:Java代码的日志记录器Logger,主要由两个属性构成:日志输出级别和日志附加器
- **日志附加器**:由日志输出位置定义,由多种属性定义,如输出布局,文件位置,文件大小等

#### 定义日志附加器Apender

- 日志附加器:为日志记录器附加一些设置信息,Apender本质是一个接口

- 日志附加器定义语法:

  - ```properties
    //log4j.appender.日志附加器名 = 日志输出位置
    log4j.appender.AppenderName = 输出位置
    ```

  - AppenderName为自定义的名称

  - 输出位置为log4j指定的类型,是定义好的Appender接口实现类

  - 常用日志附加器:

    - ```properties
      //日志输出到控制台:
      org.apache.log4j.ConsoleAppender
      //日志输出到文件:
      org.apache.log4j.FileAppender
      //当日志文件达到指定文件大小时产生一个新的日志文件
      org.apache.log4j.DailyRollingFileAppender
      //每天产生一个日志文件
      ```

#### 修饰日志附加器

- 为已经定义好的附加器添加一些属性,用于控制输出位置,不同附加器修饰属性不同

- 如控制台日志附加器ConsoleAppender:

  - ```properties
    ##define an appender named console
    log4j.appender.console=org.apache.log4j.ConsoleAppender
    #The Target value is System.out or System.err
    log4j.appender.console.Target=System.err
    #set the layout type of the apperder
    log4j.appender.console.layout=org.apache.log4j.PatternLayout
    #set the layout format pattern
    log4j.appender.console.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}] %c %L %m%n
    ```

  - log4j.appender.console.**Target**:控制输出到控制台的使用目标,值为System.out或System.err;

    - System.out:控制台显示黑色字体
    - System.err:控制台显示红色字体

- 文件日志附加器FileAppender

  - ```properties
    ##define an appender named file
    log4j.appender.file=org.apache.log4j.FileAppender
    #define the file path and name
    log4j.appender.file.File=d:/logfile.txt
    #set the layout type of the apperder
    log4j.appender.file.layout=org.apache.log4j.PatternLayout
    #set the layout format pattern
    log4j.appender.file.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}] %c %L %m%n
    ```

  - log4j.appender.file.**File**:日志输出的文件位置和文件名称

    - File的值为绝对地址或项目相对地址,使用项目相对地址时
      - ${catalina.home}:指tomcat服务器目录下;
      - ${webRoot}:服务器根目录
      - ​

- 滚动文件日志附加器DailyRollingFileAppender

  - ```properties
    ##define an appender named rollfile
    log4j.appender.rollfile=org.apache.log4j.RollingFileAppender
    #define the file path and name
    log4j.appender.rollfile.File=d:/logrollfile.txt
    #set the log's size
    log4j.appender.rollfile.MaxFileSize=1024KB
    #set the layout type of the apperder
    log4j.appender.rollfile.layout=org.apache.log4j.PatternLayout
    #set the layout format pattern
    log4j.appender.rollfile.layout.ConversionPattern=[%-5p][%d{yyyy-MM-dd HH:mm:ss}] %c %L %m%n
    ```

  - log4j.appender.rollfile.**MaxFileSize**:每个日志文件的最大值,超过此值自动产生新的文件

- log4j.appender.xxx.**layout**:日志布局类型

  - log4j常用日至布局类型:
  - org.apache.log4j.**HTMLLayout**:网页布局,以HTML表格形式布局
  - org.apache.log4j.**SimpleLayout**::简单布局,只包含日志信息的字符串和级别
  - org.apache.log4j.**PatternLayout:**匹配器布局,可以通过设置PatternLayout的ConversionPatter属性值来控制具体的输出格式

- log4j.appender.xxx.layout.**ConversionPatter**:日志文件布局的输出字段

  - ConversionPattern取值:
    - %d{HH:mm:ss.SSS} 输出时间，精确度为毫秒。
    - %d{yyyy-MM-dd}:年月日
    - %-5p 输出日志优先级，-5表示左对齐并且固定占5个字符宽度，如果不足用空格补齐。
    - %c 列出logger的namespace
    - %m 显示输出消息
    - %n 指代换行符,一般放在句末
    - %L 显示调用logger的代码行
    - %M 输出调用logger的方法名

#### 配置根日志Root Logger

- 用于控制日志的输出,语法为:

  - ```properties
    ##define a logger
    #log4j.rootLogger=debug,console,file,rollfile
    #log4j.rootLogger= [level],appenderName,...
    log4j.rootLogger=debug,console
    ```

  - level - 日志记录的优先级:**OFF,FATAL,ERROR,WARN,INFO,DEBUG,ALL**

    - 只建议使用四个级别,优先级从低到高:
      - **ERROR＜WARN＜INFO＜DEBUG**
      - OFF为关闭日志功能
    - 级别低的可以显示高级别的,级别越高显示的信息越少

  - appenderName - 日志记录指定的输出位置

## 2.3 log4j 2

- 对log4j的升级,在配置与使用上不同

### 2.3.1 实现log4j 2 的日志

1. 导入jar包,有两个依赖包

   1. log4j-api-2.10.0.jar
   2. log4j-core-2.10.0.jar

2. 创建配置文件log4j2.xml,路径为src根目录,即为classpath中

   1. log4j 2的配置文件格式更改为**xml**,不再支持properties

3. 在java程序中实现日志记录

   1. **在要输出日志的类中**创建日志对象Logger**(org.apache.logging.log4j.Logger)**,与log4j的类不同

      1. Logger对象由LogManager的getLogger(Class thisClass)创建

   2. 通过logger对象的方法设置日志输出语句

      ```java
      Logger logger = LogManager.getLogger(Test.class);

      logger.debug("这是debug信息");
      logger.info("这是info信息");
      logger.warn("这是warn信息");
      logger.error("这是error信息");
      ```

### 2.3.2 log4j的日志输出控制文件

- **格式为xml**,不支持properties,默认文件名为log4j2.xml

- 若没有配置文件,log4j2会使用一个默认配置,按照默认匹配器布局输出error级别的信息到控制台,

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration status="WARN">
      <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
          <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
      </Appenders>
      <Loggers>
        <Root level="error">
          <AppenderRef ref="Console"/>
        </Root>
      </Loggers>
    </Configuration>
    ```

#### 配置文件标签说明

1. `<Configuration/>`标签

   1. 标签的status属性用于设置log4j2自身的日志显示级别

2. `<Appenders/>`标签

   1. 标签内部为日志输出的位置,`<Console/> `为输出到控制台,`<File/>`为输出到文件,`<RollingFile/>`为输出到滚动文件

3. `<Console/>`标签

   1. 标签的name属性指定了此日志附加器的name
   2. 标签的target属性用书设置日志输出的目标形式,值可以为SYSTEM_OUT(黑色)或SYSTEM_ERR(红色)

4. `<File/>`标签

   1. 标签的fileName属性用于设定日志保存的文件路径及文件名

   2. 标签的append属性用于设置是否已追加方式将日志写入指定文件,true为追加而不是替换

      ```xml
      <File name="myLogFile" fileName="log/test.log" append="true">
      ```

5. `<RollingFile/>`标签

   1. 标签的fileName属性指定日志的存放目录与**第一个文件名**

   2. filePattern属性指定新生成的日志文件的文件名

   3. `<SizeBasedTriggeringPolicy/>`子标签的size属性指定每一个日志文件的最大大小,到达这个值会创建新的日志文件

      ```xml
      <RollingFile name="myRollingFile" fileName="log/test2.log" filePattern="log/$${date:yyyy-MM}/test-%d{MM-dd-yyyy}-%i.log.gz">
      	<PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
      	<SizeBasedTriggerPolicy size="1024KB"/>
      </RollingFile>
      ```

6. `<loggers/>`标签

   1. 用于配置根日志对象RootLogger,指定使用的日志记录器,及显示级别

   2. 子标签`<root/>`指定所使用的日志记录器

      1. root标签的属性level指定日志显示级别
         1. root标签的子标签`<appender-ref/>`用于引用appenders标签中定义好的日志记录器
            1. appender-ref的ref属性值为要引用的日志附加器的name值

   3. 只要在appenders标签中定义了File标签,RollingFile标签,其中指定的文件夹目录会自动创建,无论loggers的root标签是否声明

      ```xml
      <loggers>
      	<!-- 
      	<logger name="全限定类名或接口名" level="info">
      		<appender-ref ref="myConsole" />
      	</logger>
      	 -->
      	<root level="trace">
      		<appender-ref ref="myConsole" />
      		<!-- <appender-ref ref="myLogFile" />
      		<appender-ref ref="myRollingFile" /> -->
      	</root>
      </loggers>
      ```

      ​