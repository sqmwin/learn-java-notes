[TOC]

---

# 3 AOP与Spring

-   aop 面向切面编程 : Aspect-Oriented Programming

## 3.1 引入AOP流程

1.  项目1

    1.  定义好接口与实现类,实现类中除了实现接口方法外再写两个**非业务方法**

    2.  业务方法称为**主业务逻辑**

    3.  非业务方法称为**交叉业务逻辑**

        1.  doTransaction():用于事务处理
        2.  doLog():用于日志处理****
        3.  **主业务逻辑可以调用交叉业务逻辑**

        ```java
        public class StudentServiceImpl implements IService {
            //主业务逻辑,实现接口
            public void doSome() {
                doTransaction();
                System.out.println(this.getClass().getSimpleName() + " doSome()");
                doLog();
            }

            //主业务逻辑,实现接口
            public void doOther() {
                doTransaction();
                System.out.println(this.getClass().getSimpleName() + " doOther()");
                doLog();
            }

            //交叉业务逻辑,处理事务
            public void doTransaction() {
                System.out.println("事务代码");
            }

            //交叉业务逻辑,处理日志
            public void doLog(){
                System.out.println("输出日志代码");
            }
        }
        ```

2.  项目2

    -   若其他业务也要调用交叉业务逻辑,可以将这些交叉业务逻辑集中在一个类中,再在主业务类中**继承这个基类**,来实现这个类的这些交叉业务逻辑方法

    -   ```java
        public class BaseService {
            //交叉业务逻辑,处理事务
            public void doTransaction() {
                System.out.println("事务代码");
            }

            //交叉业务逻辑,处理日志
            public void doLog(){
                System.out.println("输出日志代码");
            }
        }

        //主业务类继承基类
        public class StudentServiceImpl extends BaseService implements IService {
            //主业务逻辑,实现接口
            public void doSome() {
                doTransaction();
                System.out.println(this.getClass().getSimpleName() + " doSome()");
                doLog();
            }

            //主业务逻辑,实现接口
            public void doOther() {
                doTransaction();
                System.out.println(this.getClass().getSimpleName() + " doOther()");
                doLog();
            }
        }
        ```

3.  项目3

    -   若主业务类要继承其他类则会与现在的基类冲突,所以将这些交叉业务逻辑方法放入专门的**工具类(util;tool)或处理类(processor)**中,再由主业务类调用

        -   交叉业务方法为静态,参数为调用的类的class

    -   ```java
        public class MyTransactionProcessor {
            //交叉业务逻辑,处理事务
            public static void doTransaction(Class<?> clazz) {
                System.out.println("我是事务代码,执行我的类为" + clazz.getName());
            }
        }

        public class MyLogProcessor {
            //交叉业务逻辑,处理日志
            public static void doLog(Class<?> clazz){
                System.out.println("我是日志代码,执行我的是" + clazz.getName());
            }
        }

        public class StudentServiceImpl implements IService {
            //主业务逻辑,实现接口
            public void doSome() {
                MyTransactionProcessor.doTransaction(this.getClass());
                System.out.println(this.getClass().getSimpleName() + " doSome()");
                MyLogProcessor.doLog(this.getClass());
            }

            //主业务逻辑,实现接口
            public void doOther() {
                MyTransactionProcessor.doTransaction(this.getClass());
                System.out.println(this.getClass().getSimpleName() + " doOther()");
                MyLogProcessor.doLog(this.getClass());
            }
        }
        ```

4.  项目4

    -   交叉业务与主业务的方法**耦合在一起**,当交叉业务增多时,影响主业务代码的可读性,降低了代码维护性

    -   所以采用动态代理方式,在不修改主业务逻辑时,使用动态代理扩展和增强功能

        -   新建一个动态代理类Handler,反射获取各个主业务方法,通过invoke方法扩展增强原方法
            -   此方法要**实现InvocationHandler接口**

    -   ```java
        public class MyInvocationHandler implements InvocationHandler{
            private Object target;

            public MyInvocationHandler(Object target) {
                this.target = target;
            }

            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //调用交叉业务逻辑
                MyTransactionProcessor.doTransaction(target.getClass());
                //调用主业务逻辑
                Object result = method.invoke(target, args);
                //调用交叉业务逻辑
                MyLogProcessor.doLog(target.getClass());
                return result;
            }
        }

            //动态代理加强方法
            @org.junit.Test
            public void test02() {
                IService service = new StudentServiceImpl();
                IService proxyService = (IService) Proxy.newProxyInstance(service.getClass().getClassLoader(), service.getClass().getInterfaces(), new MyInvocationHandler(service));
                proxyService.doSome();
                proxyService.doOther();
            }

        ```

    -   这样保证了主业务与交叉业务的解耦,代码整洁,便于维护

## 3.2 AOP概述

### 3.2.1 AOP简介

-   AOP（Aspect Orient Programming），面向切面编程，是面向对象编程 **OOP** 的一种补充。面向对象编程是从**静态**角度考虑程序的结构，而面向切面编程是从**动态**角度考虑程序运行过程。
-   AOP 底层，采用**动态代理**模式实现的。采用了两种代理：**JDK 的动态代理**,**CGLIB的动态代理**。
-   AOP中,交叉业务逻辑封装在**切面**中,利用AOP容器的功能将切面**织入**到主业务逻辑
    -   交叉业务逻辑包括但不限于:通用的、与主业务逻辑无关的代码，如安全检查、事务、日志等

### 3.2.2 AOP编程术语

>   #### （1）切面（Aspect）
>
>   ​	切面泛指**交叉业务逻辑**。上例中的事务处理、日志处理就可以理解为切面。常用的切面有通知与顾问。实际就是对主业务逻辑的一种增强。
>
>   #### （2）织入（Weaving）
>
>   ​	织入是指将切面代码插入到目标对象的过程。上例中MyInvocationHandler 类中的**invoke()**方法完成的工作，就可以称为织入。
>
>   #### （3）连接点（JoinPoint）
>
>   ​	连接点指可以被切面织入的方法。通常**业务接口中的方法**均为连接点。
>
>   #### （4）切入点（Pointcut）
>
>   ​	切入点指**切面具体织入的方法**。在 StudentServiceImpl 类中，若doSome()将被增强，而doOther()不被增强，则 doSome()为切入点，而 doOther()仅为连接点。被标记为 final 的方法是不能作为连接点与切入点的。因为最终的是不能被修改的，不能被增强的。
>
>   #### （5）目标对象（Target）
>
>   ​	目标对象指将要**被增强**的对象。 即包含主业务逻辑的类的对象。 上例中的StudentServiceImpl 的对象若被增强，则该类称为目标类，该类对象称为目标对象。当然，不被增强，也就无所谓目标不目标了。
>
>   #### （6）通知（Advice）
>
>   ​	通知是切面的一种实现，可以完成简单织入功能（织入功能就是在这里完成的）。上例中的 MyInvocationHandler 就可以理解为是一种通知。换个角度来说，**通知定义了增强代码切入到目标代码的时间点**，是目标方法执行之前执行，还是之后执行等。通知类型不同，切入时间不同。切入点定义切入的位置，通知定义切入的时间。
>
>   #### （7）顾问（Advisor）
>
>   ​	顾问是切面的另一种实现，能够将通知以更为复杂的方式织入到目标对象中，是**将通知包装为更复杂切面的装配器**。



### 3.2.3 AOP编程环境搭建

1.  导入jar包

    -   AOP 是由 AOP 联盟提出的一种编程思想，提出的一套编程规范。而Spring 是 AOP 这套规范的一种实现

    -   所以，需要导入AOP 联盟的规范（接口）包及 Spring对其的实现包

    -   AOP 联盟包aopalliance-1.0.jar

    -   Spring对 AOP 的实现包在 Spring 框架的解压包中：spring-aop-x.x.x.RELEASE.jar

    -   maven中的pom.xml设置

    -   ```xml
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        ```

2.  使用原spring-beans的约束

    -   spring框架模板的xml文件头

## 3.3 通知Advice

-   Advice:通知是切面的一种实现,可以完成简单织入功能（织入功能就是在这里完成的）
-   常用通知有：前置通知、后置通知、环绕通知、异常处理通知

### 3.3.1 通知的用法步骤

1.  定义目标类

    -   定义目标类，就是定义之前的普通 Bean 类，也就是即将被增强的

    Bean 

2.  定义通知类

    -   通知类是指，**实现了相应通知类型接口的类**。当前，实现了这些接口，就要**实现接口中的方法**，而这些方法的执行，则是根据不同类型的通知，其执行时机不同分为四种

    -   前置通知:在目标方法执行之**前**执行

    -   后置通知:在目标方法执行之**后**执行

    -   环绕通知：在目标方法执行**之前与之后均执行**

    -   异常处理通知：在目标方法执行过程中，若**发生指定异常**，则执行通知中的方法

    -   ```java
        public class MyMethodBeforeAdvice implements MethodBeforeAdvice{
            public void before(Method method, Object[] objects, Object o) throws Throwable {
                System.out.println("前置通知");
            }
        }
        ```

3.  注册目标类

    -   即在 Spring配置文件中注册定义的通知对象 Bean

    -   ```xml
         <!--注册目标类-->
         <bean class="com.sqm.service.StudentServiceImpl" id="studentServiceTarget"/>
         ```
      ```

      ```

4.  注册通知切面

    -   即在 Spring配置文件中注册定义的通知对象 Bean

    -   ```xml
         <!--注册通知切面:前置通知类的bean-->
         <bean class="com.sqm.advice.MyMethodBeforeAdvice" id="myMethodBeforeAdvice"/>
         ```
      ```

      ```

5.  注册代理工厂Bean类对象ProxyFactoryBean

    -   这里的代理使用的是 ProxyFactoryBean类,即注册此类的bean,全类名为org.springframework.aop.framework.ProxyFactoryBean

    -    ProxyFactoryBean:是与JDK的Proxy代理参数是一致的,都需要指定三部分,目标类,目标类的接口,切面(Proxy里即为Handler),只不过是在配置文件中配置

    -   \<bean/>标签的子标签\<property/>属性标签分别配置三个属性

    -   ```xml
         <!--注册代理工厂bean(ProxyFactoryBean类)的bean-->
         <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceProxy">
             <!--目标类属性name为target,ref指定目标对象bean的id-->
             <!--ref属性也可以改为value属性,值不变-->
             <property name="target" ref="studentServiceTarget"/>
             <!--目标对象实现的接口,要通过value指定全类名-->
             <!--此属性可不设置,ProxyFactoryBean类中可自动检测到目标对象的接口(通过jdk中的Proxy动态代理-->
             <!--若目标对象没有实现接口,此属性同样无需设置,此时使用的是cglib动态代理-->
             <!--<property name="interfaces" value="com.sqm.service.StudentService"/>-->
             <!--指定切面,这里是通知,这里通过value属性指定对应通知的bean的id,而不是通过ref属性指定-->
             <!--因为属性名为interceptorNames,是xxx的名称,其对应值是字符串,而不是对象-->
             <property name="interceptorNames" value="myMethodBeforeAdvice"/>
         </bean>
         ```
      ```

    -   name="target"

        -   指定目标类对象,所以对应的映射是ref的值,对象bean的引用(即bean的id)
        -   此属性name值可改为targetName,对应的映射是value,同样引用bean的id

    -   name="proxyInterfaces"

        -   指定目标类实现的接口,要求给出接口的全类名,并赋值给value属性
        -   此属性可不设置,当有实现接口时,通过jdk的Proxy动态代理自动获取此属性;当没有实现接口时,通过cglib动态代理自动检测

    -   name="interceptorNames"

        -   指定的切面,这里指通知;这里使用value属性指定切面的引用(即bean的id),而不是ref属性指定;因为property的名称为xxxNames,指定切面的名称,所以其值为字符串,而非对象
      ```

6.  客户端访问动态代理对象

    -   客户端访问的永远是动态代理对象,而不是原目标对象;通过代理对象将交叉业务逻辑动态织入到目标对象中,一起被访问

    -   ```java
            //AOP之切面:通知织入到目标对象中
            @Test
            public void test03() {
                ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
                StudentService service = (StudentService) context.getBean("studentServiceProxy");
                service.doSome();
                service.doOther();
            }
      ```

### 3.3.2 通知详解

#### 1. 前置通知接口 MethodBeforeAdvice

-   前置通知实现此接口,同时实现此接口的方法before()

-   前置通知特点:

    -   在目标方法**执行之前**先执行。
    -   **不改变**目标方法的**执行流程**，前置通知代码**不能阻止**目标方法执行。
    -   **不改变**目标方法执行的**结果**

-   ```java
    public class MyMethodBeforeAdvice implements MethodBeforeAdvice{
        /**
         *<p>
         *  方法在目标类的方法前执行,不影响目标类的方法
         *</p>
         *@param    method 代表业务方法
         *@param    objects 代表业务方法的参数列表
         *@param    o   目标对象
         */
        public void before(Method method, Object[] objects, Object o) throws Throwable {
            System.out.println("前置通知");
        }
    }
    ```

-   从容器中获取的对象永远是代理对象

    -   代理生成使用的是 JDK 代理机制

#### 2. 后置通知接口 AfterReturningAdvice

-   后置通知实现此接口,同时实现此接口的方法afterReturning()

-   后置通知特点:

    -   在目标方法**执行之后**先执行。
    -   **不改变**目标方法的**执行流程**，后置通知代码**不能阻止**目标方法执行。
    -   **不改变**目标方法执行的**结果**

-   ```java
    public class MyAfterReturningAdvice implements AfterReturningAdvice {
        /**
         *<p>后置通知方法，不影响代理方法的执行，代理方法的返回值会传入此方法中
         *
         *@param    o   代理方法有返回值；若无返回值则为null
         *@param    method  代理方法
         *@param    objects 代理目标的方法的参数列表
         *@param    o1  代理目标对象
         */
        public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
            System.out.println("后置通知+返回值" + o);
        }
    }
    ```

-   配置文件:

-   ```xml
        <!--注册目标类-->
        <bean class="com.sqm.service.StudentServiceImpl" id="studentServiceTarget"/>
    	<!--配置切面:注册后置通知-->
        <bean class="com.sqm.advice.MyAfterReturningAdvice" id="myAfterReturningAdvice"/>
        <!--配置代理bean-->
        <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceAfterProxy">
            <property name="target" ref="studentServiceTarget"/>
            <property name="interceptorNames" value="myAfterReturningAdvice"/>
        </bean>
  ```

#### 3. 环绕通知接口 MethodInterceptor

-    环绕通知实现此接口,同时实现此接口的方法invoke()

-   环绕通知,也叫方法拦截器,其功能有:

    -   可以在目标方法调用**之前及之后做处理**
    -   可以**改变**目标方法的**返回值**
    -   可以**改变**程序执行**流程**

-   注意:方法拦截器实现接口所在包为:**org.aopalliance.intercept.MethodInterceptor**

-   定义方法拦截器

-   ```java
    public class MyMethodInterceptor implements MethodInterceptor {
        public Object invoke(MethodInvocation methodInvocation) throws Throwable {
            //前置增强
            System.out.println("执行前的增强");
            //执行目标方法
            Object result = methodInvocation.proceed();
            //修改目标方法的返回值
            if (result != null) {
                result = (Integer)result + 10;
            }
            //后置增强
            System.out.println("执行后的增强");
            return result;
        }
    }
    ```

-   MethodInvocation:方法调用器

    -   proceed()方法返回代理的方法;如同Handler中的method.invoke(target, args)

-   定义配置文件:

-   ```xml
        <!--配置切面：方法拦截器-->
        <bean class="com.sqm.interceptor.MyMethodInterceptor" id="myMethodInterceptor"/>
        <!--配置代理bean-->
        <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceInterceptProxy">
            <property name="target" ref="studentServiceTarget"/>
            <property name="interceptorNames" value="myMethodInterceptor"/>
        </bean>
  ```

-   定义测试文件

-   ```java
     //aop切面:方法拦截器
      @Test
      public void test05() {
          StudentService studentService = (StudentService) context.getBean("studentServiceInterceptProxy");
          studentService.doSome();
          int result = studentService.doOther(5);
          System.out.println("改变方法结果为:" + result);
      }
     ```
#### 4. 异常通知接口 ThrowsAdvice

-    异常通知实现此接口,同时实现此接口的方法afterThrowing()

-   异常通知用于在目标方法**抛出异常后**,根据不同的异常做出**相应的处理**

-   afterThrowing()有四种方法重载,一般只使用public void afterThrowing(Exception e),方法参数为自定义的异常类

    -   e:**与具体业务相关的用户自定义的异常类对象**;容器根据异常类型不同自动选择不同方法执行

-   异常通知的方法在**目标方法执行结束后执行**

-   本例实现用户身份验证。当用户名不正确时，抛出用户名有误异常；当密码不正确时， 抛出密码有误异常。当然，在抛出这些异常后，都要做一些其它处理

    1.  定义异常类的父类

        ```java
        public class UserException extends Exception{
            public UserException() {
                super();
            }

            public UserException(String message) {
                super(message);
            }
        }
        ```

    2.  定义异常父类的子类,分别是用户名错误和密码错误

        ```java
        public class UsernameException extends UserException {
            public UsernameException() {
                super();
            }

            public UsernameException(String message) {
                super(message);
            }
        }

        public class PasswordException extends UserException {
            public PasswordException() {
                super();
            }

            public PasswordException(String message) {
                super(message);
            }
        }
        ```

    3.  定义业务接口,接口方法要抛出异常父类

        ```java
        public interface UserService {
            /**
             *<p>判断用户名和密码是否正确
             *@param    username 用户名
             *@param    password 密码
             *@return   boolean 是否正确
             *@throws  UserException 抛出异常父类
             */
            boolean check(String username,String password) throws UserException;
        }
        ```

    4.  定义目标类

        ```java
        public class UserServiceImpl implements UserService {
            public boolean check(String username, String password) throws UserException {
                if (!"admin".equals(username)) {
                    throw new UsernameException("用户名有误");
                }
                if (!"admin".equals(password)) {
                    throw new PasswordException("密码有误");
                }
                System.out.println("用户正确");
                return true;
            }
        }
        ```

    5.  定义异常通知

        ```java
        public class MyThrowsAdvice implements ThrowsAdvice {

            public void afterThrowing(UsernameException e) {
                System.out.println("异常通知捕获UsernameException," + e.getMessage());
            }

            public void afterThrowing(PasswordException e) {
                System.out.println("异常通知捕获PasswordException," + e.getMessage());
            }
        }
        ```

    6.  定义配置文件的bean

        ```xml
            <!--配置代理目标对象-->
            <bean class="test.sqm.login.service.impl.UserServiceImpl" id="userService"/>
            <!--配置切面:通知bean-->
            <bean class="test.sqm.login.advice.MyThrowsAdvice" id="myThrowsAdvice"/>
            <!--配置代理-->
            <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="userServiceThrowsProxy">
                <property name="target" ref="userService"/>
                <property name="interceptorNames" value="myThrowsAdvice"/>
            </bean>
        ```

    7.  定义测试类

        ```java
            //aop切面:异常通知
            @Test
            public void Test01(){
                String username = "admin";
                String password = "sqm";
                UserService service=(UserService)context.getBean("userServiceThrowsProxy");
                boolean result = false;
                try {
                    result = service.check(username, password);
                } catch (UserException e) {
                    e.printStackTrace();
                }
                System.out.println("result = " + result);
            }
        ```

        ​

### 3.3.1 通知的其它用法

1.  **给目标方法织入多个切面**

    -   代理对象的切面属性:拦截器名称InterceptorNames定义为**list**

    -   将需要的多个切面都添加入list

    -   ```xml
            <!--代理中织入多个切面 -->
            <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceAdviceProxy">
                <property name="target" ref="studentServiceTarget"/>
                <property name="interceptorNames">
                    <list>
                        <value>myMethodBeforeAdvice</value>
                        <value>myAfterReturningAdvice</value>
                        <value>myMethodInterceptor</value>
                    </list>
                </property>
            </bean>
      ```

    -   执行结果:

        -   ```console
            前置通知
            执行前的增强
            StudentServiceImpl doSome()
            执行后的增强
            后置通知+返回值null
            前置通知
            执行前的增强
            StudentServiceImpl doOther()
            执行后的增强
            后置通知+返回值16
            ```

2.  **无接口的 CGLIB代理生成**

    -   若不存在接口，则 ProxyFactoryBean会自动采用 CGLIB方式生成动态代理
    -   查看后台运行情况，可以看到代理生成使用的是CGLIB 代理机制

3.  **有接口的 CGLIB 代理生成 - proxyTargetClass 属性**

    -   若存在接口，但**又需要使用CGLIB 生成代理对象**时，只需要在配置文件中代理bean的属性增加一个**proxyTargetClass属性**,其值设为true，用于指定**强制使用 CGLIB 代理机制**

    -   ```xml
            <!--使用CGLIB生成代理对象:方式1:代理bean增加一个name为proxyTargetClass属性property,value为true-->
            <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceCglibProxy">
                <property name="target" ref="studentServiceTarget"/>
                <property name="interceptorNames" value="myMethodBeforeAdvice"/>
                <property name="proxyTargetClass" value="true"/>
            </bean>
      ```

    -   也可指定 optimize（优化）的值为true，强制使用 CGLIB 代理机制

    -   ```xml
            <!--方式2:property name="optimize" value="true"-->
        		<property name="optimize" value="true"/>
      ```

    -   查看后台运行情况，可以看到代理生成使用的是CGLIB 代理机制

## 3.4 顾问 Advisor

-   通知Advice在Spring的实现切面中功能过于简单只能将切面织入到目标类的所有目标方法中,而无法织入到指定目标方法
-   **顾问Advisor**是Spring的另一个切面,可完成复杂的切面织入功能
-   **PointcutAdvisor接口**是顾问的一种;切入点顾问将通知进行了包装，会根据**不同的通知类型**，将通知切面织入到**不同的切入点**(想要织入的目标方法)
-   PointcutAdvisor接口的两种常用实现类:
    -   NameMatchMethodPointcutAdvisor:名称匹配方法切入点顾问
    -   RegexpMethodPointcutAdvisor:正则表达式方法切入点顾问

### 3.4.1 名称匹配方法切入点顾问

-   **NameMatchMethodPointcutAdvisor**:名称方法切入点顾问

    -   顾问advisor可根据配置文件中指定的方法名来设置切入点
    -   全类名:org.springframework.aop.support.NameMatchMethodPointcutAdvisor

-   修改配置文件,顾问advisor通过**advice属性**包装想用的通知bean,通过**mapperNames或mapperName属性**指定要调用通知的匹配名称的方法,通过代理工厂bean将advisor织入目标类中

-   ```xml
        <!--配置切面:顾问:名称匹配方法切入点顾问-->
        <bean class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor" id="nameMatchMethodPointcutAdvisor">
            <!--顾问属性:advice:指定的通知bean
            <property name="advice" ref="myMethodBeforeAdvice"/>
            <!--顾问属性:要启用顾问的方法所匹配的名称:第一种:-->
            <property name="mappedNames" value="doSome,doValue"/>
            <!--指定方法名称匹配列表:第二种:-->
            <property name="mappedNames">
                <list>
                    <value>doSome</value>
                    <value>doOther</value>
                </list>
            </property>
            <!--顾问属性:通过指定方法名称包含的字符:第三种:如以do开头的所有方法-->
            <property name="mappedName" value="do*"/>
            <!--第四种:指定单个方法-->
            <property name="mappedName" value="doSome"/>
        </bean>
        <!--配置代理bean:将advisor织入目标类-->
        <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServicePointcutAdvisorProxy">
            <property name="target" ref="studentServiceTarget"/>
            <property name="interceptorNames" value="nameMatchMethodPointcutAdvisor"/>
        </bean>
  ```

### 3.4.1 正则表达式方法切入点顾问

-   **RegeddxpMethodPointcutAdvisor**:正则表达式切入点顾问

    -   顾问advisor根据正则表达式来检索**目标类的接口**中符合正则表达式方法名的**方法**
    -   全类名:org.springframework.aop.support.RegexpMethodPointcutAdvisor

-   修改配置文件,顾问通过**advice**属性包装要使用的通知bean,通过**pattern**属性或**patterns**(list形式)属性指定正则表达式

-   ```xml
        <!--配置切面:顾问:正则表达式顾问-->
        <bean class="org.springframework.aop.support.RegexpMethodPointcutAdvisor" id="regexpMethodPointcutAdvisor">
            <property name="advice" ref="myMethodBeforeAdvice"/>
            <!--<property name="pattern" value=".*do.*"/>-->
            <!--通过patterns指定正则表达式-->
            <property name="patterns">
                <list>
                    <value>.*doS.*</value>
                    <!--<value>.*doO.*</value>-->
                </list>
            </property>
        </bean>
        <!--配置正则表达式顾问代理-->
        <bean class="org.springframework.aop.framework.ProxyFactoryBean" id="studentServiceRegexpAdvisorProxy">
            <property name="target" ref="studentServiceTarget"/>
            <property name="interceptorNames" value="regexpMethodPointcutAdvisor"/>
        </bean>
  ```

#### 正则表达式常用运算符

| 运算符  | 名称   | 意义             |
| ---- | ---- | -------------- |
| .    | 点号   | 表示任意单个字符       |
| +    | 加号   | 表示前一个字符出现一次或多次 |
| *    | 星号   | 表示前一个字符出现0次或多次 |

-   \   转义字符 代表不解析此符号后的运算符
-   举例:
    -   `com\.sqm\.service\.StudentServiceImpl\.do.*`:表示全类名com.sqm.service.StudentServiceImpl类中的所有以do开头的方法
    -   `.*do.*`表示方法全名(包名,接口名,方法名)中包含do的方法
    -   `.*do.*|.*S.*`:表示方法全名中包含do或者S的方法
    -   `.*do.*S.*`:表示方法全名中既包含do又包含S的方法

## 3.5 自动代理生成器

-   之前所有的代理对象都是由ProxyFactoryBean代理工具类生成的,有缺点:
    1.  一个代理对象只能代理一个目标bean若有多个bean都要织入切面,则要配置多个代理对象
    2.  客户类(测试时)获取的bean是代理类的id,而不是目标对象的id,如果能直接用目标对象的id不更好吗
-   自动代理生成器:最终均继承BeanPostProcessor:bean后处理器:**当调用目标方法时,自动生成代理对象并织入切面,代理目标bean执行方法**
-   自动代理生成器中的所有bean在初始化时会自动执行bean后处理器的方法,无需id属性,客户类直接使用目标对象bean的id
-   常用的自动代理生成器:
    -   DefaultAdvisorAutoProxyCreator:默认顾问自动代理生成器
    -   BeanNameAutoProxyCreator:bean名称自动代理生成器

### 3.5.1 默认Advisor自动代理生成器

-   **DefaultAdvisorAutoProxyCreator**

    -   将**所有**目标对象与**所有Advisor** 自动结合,当应用程序上下文ApplicationContext获取bean时自动创建代理
    -   **无需**给生成器做任何的注入**配置**
    -   **只能与Advisor 配合使用**

-   配置文件中配置自动代理:

    -   ```xml
            <!--默认顾问自动代理生成器,直接获取目标bean-->
            <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"/>
      ```

### 3.5.2 Bean 名称自动代理生成器

-   DefaultAdvisorAutoProxyCreator会为**每个目标对象bean都织入advisor**,不具有选择性,并且**切面只能是Advisor**

-   BeanNameAutoProxyCreator是根据bean的id来为符合名称的类生成相应的代理对象,切面既可以是Advisor也可以是Advice

-   新增一个目标对象类

-   ```java
    public class TeacherServiceImpl implements StudentService
    ```

-   注册目标对象bean

-   ```xml
    <!--TeacherServiceTarget-->
    <bean class="com.sqm.service.TeacherService" id="teacherServiceTarget"/>
    ```

-   修改配置文件中的代理生成器

-   ```xml
        <!--bean名称自动代理生成器-->
        <bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
            <!--要代理的bean的id-->
            <property name="beanNames" value="studentServiceTarget,teacherServiceTarget"/>
            <!--要使用的切面,既可以是Advice,也可以是Advisor-->
            <property name="interceptorNames" value="myAfterReturningAdvice,nameMatchMethodPointcutAdvisor"/>
        </bean>
  ```

-   **beanNames**:指定要增强的目标类的bean id;即使只有一个目标对象,属性也为beanNames,beanNames的值**可以为 * 通配符号**

    -   对于多个beanNames,可以使用\<list/>标签的形式
    -   **实际上property标签的name值若为xxxs,都可使用\<list/>标签的形式**

-   **interceptorNames**:指定切面;既可以是Advice,也可以是Advisor

## 3.6  AspectJ 对 AOP 的实现

-   在 Spring 中使用 AOP 开发时，一般使用AspectJ 的实现方式

### 3.6.1 AspectJ 简介

-   AspectJ是一个**面向切面的框架**，它扩展了Java语言。**AspectJ 定义了 AOP 语法**，它有一个**专门的编译器**用来生成遵守 Java 字节编码规范的 Class 文件

### 3.6.2 AspectJ的通知类型

1.  前置通知
2.  后置通知
3.  环绕通知
4.  异常通知
5.  **最终通知:无论程序是否正常执行,该通知都会执行**,类似于finally代码块中的效果

### 3.6.3  AspectJ 的切入点表达式

-   AspectJ定义了专门的表达式用于指定切入点

-   切入点表达式原型为:

    -   ```
        execution	([modifiers-pattern]		访问权限类型
        			ret-type-pattern			返回值类型
        			[declaring-type-pattern].	全限定类名
        			name-pattern(param-pattern)	方法名(参数名)
        			[throws-pattern])			抛出异常类型
        ```

-   切入点表达式:用于匹配目标对象的**目标方法**

    -   所以execution表达式中是方法的签名

    -   表达式中[xxx]方括号的内容可以省略,各部分间用空格隔开

    -   表达式可使用如下的表达式符号:

        -   | 符号   | 意义                                       |
            | ---- | ---------------------------------------- |
            | *    | 0个字符或多个字符                                |
            | ..   | 用在**方法参数中**,表示任意多个参数<br/>用在**包名后**,表示包括此包及其子包的路径 |
            | +    | 用在**类名后**,表示当前类及其子类<br/>用在**接口后**,表示当前接口及其实现类 |

            例如

            -   `execution(public * *(..)) `
            -   指定切入点为：任意公共方法。
            -   `execution(* set *(..)) `
            -   指定切入点为：任何一个以“set”开始的方法。
            -   `execution(* com.xyz.service.*.*(..))`
            -   指定切入点为：定义在service 包里的任意类的任意方法。
            -   `execution(* com.xyz.service..*.*(..))`
            -   指定切入点为：定义在 service包或者子包里的任意类的任意方法。“..”出现在类名中时，后面必须跟“ * ”，表示包、子包下的所有类。
            -   `*execution(* *.service.*.doSome())`
            -   指定只有一级包下的 serivce子包下所有类中的 doSome()方法为切入点
            -   `execution(* *..service.*.doSome())`
            -   指定所有包下的 serivce子包下所有类中的 doSome()方法为切入点
            -   `execution(* com.xyz.service.IAccountService.*(..)) `
            -   指定切入点为：IAccountService 接口中的任意方法。
            -   `execution(* com.xyz.service.IAccountService+.*(..)) `
            -   指定切入点为： IAccountService 若为接口，则为接口中的任意方法及其所有实现类中的任意方法；若为类，则为该类及其子类中的任意方法。
            -   `execution(* joke(String,int))) `
            -   指定切入点为：所有的 joke(String,int)方法，且 joke()方法的第一个参数是 String，第二个参数是 int。如果方法中的参数类型是java.lang 包下的类，可以直接使用类名，否则必须使用全限定类名，如 joke(java.util.List, int)。
            -   `execution(* joke(String,*))) `
            -   指定切入点为：所有的 joke()方法，该方法第一个参数为 String，第二个参数可以是任意类型，如joke(String s1,String s2)和joke(Strings1,double d2)都是，但joke(Strings1,double d2,String s3)不是。
            -   `execution(* joke(String,..))) `
            -   指定切入点为：所有的 joke()方法，该方法第一个参数为String，后面可以有任意个参数且参数类型不限，如 joke(String s1)、joke(String s1,String s2)和 joke(String s1,double d2,String s3)都是
            -   `execution(* joke(Object))`
            -   指定切入点为：所有的 joke()方法，方法拥有一个参数，且参数是 Object 类型。 joke(Object ob)是，但，joke(String s)与 joke(User u)均不是。
            -   `execution(* joke(Object+)))`
            -   指定切入点为：所有的 joke()方法，方法拥有一个参数，且参数是 Object 类型或该类的子类。不仅 joke(Object ob)是，joke(String s)和 joke(User u)也是

### 3.6.4 AspectJ 的开发环境

1.  导入两个jar包

    1.  除了之前的两个aop包外,还需要AspertJ自身的jar包

        1.  aspectjweaver-1.8.9.jar

        2.  aspectjrt-1.8.9.jar(可选(aspectj runtime))

        3.  aspectjtools-1.8.9.jar(可选)

        4.  maven中的pom.xml

        5.  ```xml
                    <dependency>
                        <groupId>org.aspectj</groupId>
                        <artifactId>aspectjweaver</artifactId>
                        <version>1.8.9</version>
                    </dependency>
                    <dependency>
                        <groupId>org.aspectj</groupId>
                        <artifactId>aspectjtools</artifactId>
                        <version>1.8.9</version>
                    </dependency>
                    <dependency>
                        <groupId>org.aspectj</groupId>
                        <artifactId>aspectjrt</artifactId>
                        <version>1.8.9</version>
                    </dependency>
              ```
            ​```

    2.  spring与aspectj的整合jar包

        1.  spring-aspects-5.0.2.RELEASE.jar

        2.  maven中的pom.xml

        3.  ```xml
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aspects</artifactId>
                <version>5.0.2.RELEASE</version>
            </dependency>
            ```

2.  引入AOP约束

    -   在配置文件头部， 要引入关于 aop 的约束

    -   ```xml
        <?xml version="1.0" encoding="UTF-8"?>  
        <beans xmlns="http://www.springframework.org/schema/beans"  
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
            xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="  
                http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd  
                http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">  
        ```

    -   配置文件中使用的AOP 约束中的标签，**均是 AspectJ框架使用的**， 而非 Spring 框架本身在实现 AOP 时使用的

    -   AspectJ 对于 AOP 的实现有两种方式：

        1.  注解方式
        2.  xml方式

### 3.6.5  AspectJ 基于注解的 AOP 实现

#### 1. 实现步骤

1.  **定义业务接口与实现类**

    ```java
    public interface Service {
        void doSome();
        int doOther(int a, String b);
    }

    public interface UserService extends Service {
        String doThird();
    }

    public class UserServiceImpl implements UserService {
        public String doThird() {
            System.out.println(this.getClass().getSimpleName() + "doThrid()");
            return "3";
        }

        public void doSome() {
            System.out.println(this.getClass().getSimpleName() + "doSome()");
        }

        public int doOther(int a, String b) {
            System.out.println(this.getClass().getSimpleName() + "doOther(" + a +"," + b + ")");
            return a;
        }
    }
    ```

2.  **定义切面 POJO类,在切面类上添加@Aspect注解**

    ```java
    /**
     * <p>POJO类,通过注解Aspect指定为切面,方法作为不同的通知方法
     * @author sqm
     * @version 1.0
     */
    @Aspect
    public class MyAspect {
        public void beforeSome() { System.out.println("前置增强"); }
        public void afterReturning() { System.out.println("后置增强"); }
    }
    ```

3.  **在 POJO 类的普通方法上添加通知注解**

    -   切面类用于定义增强方法,这些方法用不同的**通知注解**,对应不同的通知类型,会在不同**时间点**完成织入

    -   通过execution表达式指定增强方法具体应用的目标类与目标方法,即**切入点**

    -   ```java
            @Before("execution(* *..UserServiceImpl.doSome())")
            public void beforeSome() {
                System.out.println("前置增强");
            }
      ```

4.  **注册目标对象与 POJO切面类**

    -   在配置文件中注册aspect类的bean和目标对象的bean

    -   ```xml
            <!--UserService bean-->
            <bean class="com.sqm.aspectj.annotation.service.impl.UserServiceImpl" id="userServiceTarget"/>
            <!--Aspect bean-->
            <bean class="com.sqm.aspectj.annotation.aspect.MyAspect" id="myAspect"/>
      ```

5.  **注册 AspectJ的自动代理**

    -   通过aspectj自动生成自动代理

    -   在AspectJ中自动代理由context容器自动生成,只需注册aspectj的自动代理生成器,会自动检索到@Aspect注解并按每个方法的通知注解类型与execution切入点将其织入并生成代理

    -   **\<aop:aspectj-autoproxy/>标签**:底层是由AnnotationAwareAspectJAutoProxyCreator实现

        -   是基于Aspect J的注解适配自动代理生成器

    -   ```xml
            <!--配置aspectj的自动代理-->
            <aop:aspectj-autoproxy/>
      ```

6.  **测试类中使用目标对象的 id**

    ```java
    public class AnnotationTest {
        private ApplicationContext context;
        @Before
        public void setUp() {
            context = new ClassPathXmlApplicationContext("aspectj/aspectj-spring-context.xml");
        }
        @Test
        public void test01() {
            UserService service = (UserService) context.getBean("userServiceTarget");
            service.doSome();
            service.doOther(1, "b");
            service.doThird();
        }
    }
    ```

#### 2. @Before 前置通知-增强方法有JoinPoint参数

-   **@Before**:在目标方法执行之前执行

-   注解为前置通知的方法，可以包含一个**JoinPoint** 类型参数

    -   ```java
            @Before("execution(* *..UserServiceImpl.doOther(..))")
            public void beforeOther(JoinPoint joinPoint) {
      ```

    -   JoinPoint类的对象是切入点表达式

    -   通过JoinPoint对象可以获取切入点表达式、方法签名、目标对象

        -   joinPoint:切入点表达式
        -   Signature getSignature():方法签名
        -   Object getTarget():目标对象
        -   Object[] getArgs():目标方法参数(若果有的话)

-   ```java
        //前置增强,advice中可设方法参数JoinPoint,为切入点表达式,通过joinPoint可获得切入点表达式,方法签名,目标对象,目标方法实际参数
    	@Before("execution(* *..UserServiceImpl.doOther(..))")
        public void beforeOther(JoinPoint joinPoint) {
            System.out.println("前置增强-切入点表达式:" + joinPoint);
            System.out.println("前置增强-方法签名:" + joinPoint.getSignature());
            System.out.println("前置增强-目标对象:" + joinPoint.getTarget());

            Object[] args = joinPoint.getArgs();
            if (args.length > 0) {
                System.out.print("前置增强-方法参数为:");
                for (Object arg : args) {
                    System.out.print(arg + " ");
                }
                System.out.println();
            }
        }
  ```

-   运行结果为:

-   ```java
    前置增强
    UserServiceImpl doSome()
    前置增强-切入点表达式:execution(int com.sqm.aspectj.annotation.service.Service.doOther(int,String))
    前置增强-方法签名:int com.sqm.aspectj.annotation.service.Service.doOther(int,String)
    前置增强-目标对象:com.sqm.aspectj.annotation.service.impl.UserServiceImpl@71c27ee8
    前置增强-方法参数为:1 b 
    UserServiceImpl doOther(1,b)
    UserServiceImpl doThrid()
    ```

#### 3. @AfterReturning 后置通知-注解有returning属性

-   @AfterReturning:在目标方法之后执行;**可以获取**到目标方法的**返回值**

-   后置通知的方法也可包含JoinPoint参数

-   注解的returning属性用于接收目标方法返回值的变量名

-   后置通知的方法还可以包含returning所指的参数,当不确定目标返回值类型时,该参数类型最好为Object类型

-   后置通知方法参数顺序为(JoinPoint joinpoint,Object result)

-   ```java
        @AfterReturning(value = "execution(* *..UserServiceImpl.doThird())", returning="result")
        public void afterReturning(JoinPoint joinPoint,Object result) {
            System.out.println("后置增强,目标方法的result:" + result);
            System.out.println("后置增强,切入点表达式:" + joinPoint);
        }
  ```

-   执行结果:

-   ```java
    UserServiceImpl doThird()
    后置增强,目标方法的result:3
    后置增强,切入点表达式:execution(String com.sqm.aspectj.annotation.service.UserService.doThird())
    ```

#### 4. @Around环绕通知-增强方法有 ProceedingJoinPoint参数

-   @Around:在目标方法之前之后都执行,被注解为**环绕通知的方法必须有返回值,Object类型**

-   环绕通知的方法可以包含一个**ProceedingJoinPoint类型的参数**

    -   ProceedingJoinPoint接口有一个方法**proceed()**,用于执行目标方法;该方法的返回值是目标方法的返回值

-   环绕通知相当于**方法拦截器**,实际是拦截了目标方法的执行

    -   proceed的方法执行过程和动态代理的method.invoke(target,args)方法类似

-   ```java
        @Around("execution(* *..UserServiceImpl.doThird())")
        public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
            System.out.println("环绕通知,前");
            Object result = proceedingJoinPoint.proceed();
            System.out.println("环绕通知,后");
            return result;
        }
  ```

-   执行结果:

-   ```java
    环绕通知,前
    UserServiceImpl doThird()
    环绕通知,后
    3
    ```

#### 5. @AfterThrowing异常通知-注解中有 throwing属性

-   @AfterThrowing:在目标方法抛出异常执行;

-   注解的**throwing**属性用于指定所发生的异常类对象

-   被注解为异常通知的方法可以包含一个参数类型为**Throwable**的参数,参数名为注解中thrwoing指定的名称,表示**发生的异常对象**

-   ```java
        @AfterThrowing(value = "execution(* *..annotation.*.*.UserServiceImpl.doThird())",throwing = "e")
        public void afterThrowing(Throwable e) {
            System.out.println("异常通知:" + e.getMessage());
        }
  ```

#### 6. @After最终通知

-   无论目标方法是否正常执行,被该注解修饰的方法一定执行,与finally代码块类似

-   ```java
        @After("execution( * *(..))")
        public void after() {
            System.out.println("finally");
        }
  ```

#### 7. @Pointcut定义切入点

-   当多个通知增强方法使用相同execution切入点表达式时,使用**@Pointcut直接定义execution切入点表达式**

-   用法:将@Pointcut 注解在一个方法之上，以后所有的executeion 的 value 属性值均可使用**该方法名**作为**切入点**,此方法就代表@Pointcut注解定义的切入点

-   这个使用@Pointcute注解的方法一般使用 **private** 的标识方法，方法体为空

-   ```java
        @Pointcut("execution(* *..UserServiceImpl.doSome())")
        private void doSomePointcut() { }

        @Before("doSomePointcut()")
        public void beforeSome() {
            System.out.println("前置增强");
        }
  ```

###3.6.6 AspectJ基于XML的AOP 实现

-   切面即为一个pojo类,通知增强方法和之前一样只不过没有注解,而是通过xml配置文件将切面中的功能织入到目标类的目标方法

#### 1. 实现步骤

1.  **定义业务接口与实现类**

    -   和之前的一样

2.  **定义切面 POJO类**

    -   和之前一样,只不过没有了注解

3.  **注册目标对象与 POJO切面类**

    -   ```xml
            <!--注册目标对象-->
            <bean class="com.sqm.aspectj.xml.service.impl.UserServiceImpl" id="userServiceTarget"/>
            <!--注册切面-->
            <bean class="com.sqm.aspectj.xml.aspect.MyAspect" id="myAspect"/>
      ```

4.  **在容器中定义 AOP配置**

    -   ```xml
            <!--配置aop-->
            <aop:config>
                <!--定义切入点-->
                <aop:pointcut id="doSomePointcut" expression="execution(* *..UserServiceImpl.doSome())"/>
                <aop:pointcut id="doOtherPointcut" expression="execution(* *..UserServiceImpl.doOther(..))"/>
                <aop:pointcut id="doThirdPointcut" expression="execution(* *..UserServiceImpl.doThird(..))"/>

                <!--定义切面-->
                <aop:aspect ref="myAspect">
                    <aop:before method="beforeSome" pointcut-ref="doSomePointcut"/>
                    <aop:after-returning method="afterReturning" pointcut-ref="doThirdPointcut" returning="result"/>
                    <aop:after-throwing method="afterThrowing" throwing="e" pointcut-ref="doOtherPointcut"/>
                    <aop:around method="around" pointcut-ref="doSomePointcut"/>
                    <aop:after method="after" pointcut-ref="doSomePointcut"/>
                </aop:aspect>
            </aop:config>
      ```

    -   **\<aop:config/>**标签中进行**aop 的配置**

        -   子标签**\<aop:pointcut/>定义切入点**
            -   experssion属性指定execution切入点表达式
            -   id属性指定切入点名称
        -   子标签**\<aop:aspect/>**定义具体的织入规则
            -   根据不同的通知类型定义切入时机,根据通知的**pointcut-ref属性**确定要使用的切入点,根据**method属性**确定要使用的增强方法
            -   通知类型:
                -   **\<aop:before/>**：前置通知
                -   **\<aop:after-returning/>**： 后置通知
                -   **\<aop:around/>**：环绕通知
                -   **\<aop:after-throwing/>**：异常通知
                -   **\<aop:after/>**：最终通知
                -   **\<aop:declare-parents/>**：引入通知

5.  **测试类中使用目标对象的 id**

    -   ```java
        public class xmlTest {
            private ApplicationContext context;

            @Before
            public void setUp() {
                context = new ClassPathXmlApplicationContext("aspectj/aspectj-xml-context.xml");
            }

            @Test
            public void test01() {
                UserService userService = (UserService) context.getBean("userServiceTarget");
                userService.doSome();
                int other = userService.doOther(1, "b");
                System.out.println(other);
                String third = userService.doThird();
                System.out.println(third);
            }
        }
        ```

#### 2. \<aop:before/>前置通知

-   将before()方法织入到切入点中进行对目标方法的增强
-   若方法有重载,如一个无参before(),一个有参before(JoinPoint jp)在 **method 属性**赋值时，不仅要放方法名，还要放入方法的参数类型**全类名**:
    -   `<aop:before method="before(org.aspectj.lang.JoinPoint)" pointcut-ref="doOtherPointcut"`
-   若只有带参方法,在method属性赋值时,无需放入方法参数:
    -   `<aop:before method="before" pointcut-ref="doOtherPointcut"`

#### 3. \<aop:after-returning/>后置通知

-   后置通知的XML 的配置中，有一个属性**returning**，指定用于接收**目标方法**的**返回值**所使用的**变量**名。其可作为增强方法的参数出现

#### 4. \<aop:around/>环绕通知

-   环绕通知的**增强方法**一般**返回类型为Object**，是目标方法的返回值。并且可以包含一个参数 **ProceedingJoinPoint**，其**方法proceed()**可**执行目标方法**

-   ```java
        public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
            System.out.println("环绕通知,前");
            Object result = proceedingJoinPoint.proceed();
            System.out.println("环绕通知,后");
            return result;
        }
  ```

#### 5. \<aop:after-throwing/>异常通知

-   其 XML 的配置中，有一个**属性 throwing**，指定用于接收目标方法所**抛出异常**的**变量**名。其可作为增强方法的参数出现，该参数为 **Throwable** 类型

#### 6. \<aop:after/>最终通知

-   同注解的最终通知







