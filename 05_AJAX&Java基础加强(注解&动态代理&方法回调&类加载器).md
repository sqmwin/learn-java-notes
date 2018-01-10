[TOC]

---

# 1.AJAX

## 1.1 AJAX概述

- AJAX : Asynchronous JavaScript and XML : 异步JavaScript和XML
  - 创建**交互式**网页应用的**网页开发**技术
  - 用于创建**快速动态网页**的技术
  - 是一种流行的**web编程方式**
    - Better,Faster,User-Friendly
  - 不是一种新的编程语言
  - 目的是**提高用户感受**
- AJAX技术 = DHTML(HTML,CSS,JavaScript) + XMLHttpRequest
  - 基于标准的表示技术:XHTML,CSS
  - 动态显示和交互技术:Document Object Model
  - 数据交换和操作技术:XML,XSLT
  - **异步数据获取**技术:XMLHttpRequest(XHR)
  - **AJAX结合所有**

## 1.2 AJAX交互模型

###1.2.1 传统交互模型和ajax交互模型区别

- ![AJAX交互模型](D:\04_Java学习笔记\05_AJAX01.png)
- 传统web应用交互模型
  - 浏览器直接将请求发送给web服务器,服务器返回响应
- ajax的web应用交互模型
  - 浏览器将请求发送给ajax引擎(以XMLHttpRequest为核心),ajax引擎再将请求发送给服务器,服务器返回响应给ajax引擎,再由ajax引擎传回浏览器

### 1.2.2 同步与异步交互模式

- ![传统web交互模式---同步](D:\04_Java学习笔记\05_AJAX02.png)
- 同步交互模式:客户端提交请求,等待,在服务器的响应传回客户端前**,客户端无法进行其他操作**
- ![ajax支持web交互方式---异步](D:\04_Java学习笔记\05_AJAX03.png)
- 异步交互模式:客户端提交请求给ajax引擎,客户端可以继续操作(例如点击同一页面上的其他超链接),然后**由ajax引擎完成与服务器端的通信**,当服务器响应返回ajax引擎后,**ajax引擎会更新客户页面**.
  - **在客户端提交请求后,用户可以继续操作,无需等待**
    - **Better,Faster,User-Friendly**
- ajax google应用
  - Google Suggest / Gmail / Google Map
  - ![](D:\04_Java学习笔记\05_AJAX05.png)
- ​

## 1.3 AJAX快速入门

1. 创建XMLHttpRequest对象(**ajax核心就是XMLHttpRequest对象**)

   - XMLHttpRequest[文档](http://www.w3school.com.cn/xmldom/dom_http.asp)

     - 方法

       - **open(method,url,asynch,[username,password])**

         - 初始化 HTTP 请求参数(建立对服务器的调用)，例如 URL 和 HTTP 方法，但是并不发送请求。
           - method - http请求的方法,值包括GET,POST,HEAD
           - url - 请求的主体(请求服务器页面的地址)
           - asynch - 请求是否异步,若为true或省略则为异步;false,请求为同步
           - username,password - 可选,url所需的授权资格

       - **send(content)**

         - 发送 HTTP 请求，使用传递给 open() 方法的参数，以及传递给该方法的可选请求体。

         - content - 请求体,字符串或者[Document对象](http://www.w3school.com.cn/xmldom/dom_document.asp);若为null则请求体不是必须有

           - 例如:

           - ```javascript
             //send("key1=value1&key2=value2&...");
             xmlHttpRequest.send("name=xxx&pwd=xxx");
             ```

       - **setRequestHeader(name, value)**

         - 当readyState为1时调用,在open()之后,send()之前
         - 指定HTTP请求的头部信息
         - 若请求头已经指定,则这个方法时在原请求头加上这个的值
         - name - 请求头的名称
         - value - 请求头的值

     - 属性

       - **readyState**:HTTP请求的状态,在XMLHttpRequest对象创建后,这个属性值从0开始,直到接收到完整的HTTP响应,这个值增加到4
         - 0:Uninitialized:初始化状态
         - 1:Open:正在加载状态(调用open)
         - 2:Sent:已加载,http请求已发送(调用send)
         - 3:Receiving:交互中,响应头已接收完毕,响应体正在接收
         - 4:Loaded:完成交互,http响应已经完全接收
       - **responseText / responseXML**:服务器的响应字符串
         - 当readState小于3时,这个属性为空字符串
         - 当readState为3时,这个属性值为返回已经接收的响应
         - 当readState为4时,这个属性值是完整的响应体
         - Text为字符串,XML为[Document对象](http://www.w3school.com.cn/xmldom/dom_document.asp)
       - **status**:服务器返回的HTTP状态码
         - 200 - 成功
         - 404 - not found
         - 当readState小于3时读取这个属性会导致异常
       - **statusText**:服务器返回的HTTP状态信息
         - 当状态码为200时为"OK"
         - 当状态码为404时为"Not Found"
         - 当readState小于3时读取这个属性会导致异常

     - 事件句柄

       - **onreadystatechange**:状态回调函数
         - 每次readyState属性改变时调用的事件句柄函数
           - 当readState为3时,可能调用多次

   - **创建XMLHttpRequest对象**

     - 不同浏览器提供不同支持

     - IE5 , IE6

       - ```javascript
         var xmlHttp = null;
         xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
         xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
         ```

     - 其他大部分浏览器包括IE7以上

       - ```javascript
         var xmlHttp = null;
         xmlhttp = new XMLHttpRequest();
         ```

2. 将状态触发器绑定到一个函数(注册回调函数)

   1. ```javascript
      //注册回调函数
      xmlHttp.onreadystatechange = processor;

      //建立一个回调函数
      function processor(){
      //...	
      }
      ```

3. 使用**open()**方法建立与服务器的连接

4. 向服务器端发送(**send()**)数据

   1. ```JavaScript
      //get方式发送数据
      xmlHttp.open("GET","url?key=value"); // 参数在url上
      xmlHttp.send(null); 

      //post方式发送数据
      xmlHttp.open("POST","url"); // 不需要写参数
      xmlHttp.setRequestHeader("CONTENT-TYPE","application/x-www-form-urlencoded"); // post发送参数前，需要设置编码格式
      xmlHttp.send("name=xxx&pwd=xxx"); // 发送post数据

      ```

5. 在回调函数中返回数据进行处理

   1. ```JavaScript
      function 回调函数(){
      	if(xmlHttp.readyState == 4) { //如果响应完成
      	        if(xmlHttp.status == 200) {//如果返回成功
                      //… 
      	        }
      	}
      }
      ```

   2. 常用的服务器返回数据类型

      1. HTML片段
      2. JSON格式的数据
      3. XML格式的数据

## 1.4 针对ajax返回的三种服务器响应数据类型分别编程

### 1.4.1 返回HTML片段的案例

- **验证用户名是否有效**

  - 创建XMLHttpRequest对象

  - ```JavaScript
    //先创建XMLHttpRequest对象
    var xmlHttpRequest = null;

    if(window.XMLHttpRequest) {
    	//针对大部分浏览器
    	xmlHttpRequest = new XMLHttpRequest();
    } else if (window.ActiveXObject) {
    	//针对IE5,IE6
    	xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
    }
    ```

  - 获取用户名文本框对象,文本框内信息

  - **设置文本框失去焦点事件**:失去焦点后执行ajax引擎

  - 设置回调函数

    - 通过xmlHttp.responseText获得返回数据
    - **通过DOM查找获得元素span对象**
    - **调用元素的innerHTML方法进行操作**:将返回的数据放入span对象中,如下图:
    - ![](D:\04_Java学习笔记\05_AJAX06.png)
    - ![](D:\04_Java学习笔记\05_AJAX07.png)

  - 调用open()方法,请求一个服务器的判断servlet程序

    - ```JavaScript
      xmlHttpRequest.open("POST","${pageContext.request.contextPath}/ajaxservlet");
      ```

  - 调用setRequestHeader方法设置请求头

    - ```javascript
      xmlHttpRequest.setRequestHeader("content-type","application/x-www-form-urlencoded");
      ```

  - 调用send()方法发送post数据

    - ```javaScript
      xmlHttpRequest.send("username="+value);
      ```

- **点击链接获取数据库table数据**

  - ajax.jsp

  - ```jsp
    <head>
    <title>ajax开发---显示商品信息</title>
    <script type="text/javascript">
    	window.onload = function get() {
    		//得到id=p的超链接
    		var p = document.getElementById("p");
          	 //设置超链接的动作
    		p.onclick = function() {
    			//1.得到XMLHttpRequest对象.
    			var xmlHttpRequest = null;
    				if(window.XMLHttpRequest) {
    					//针对大部分浏览器
    					xmlHttpRequest = new XMLHttpRequest();
    				} else if (window.ActiveXObject) {
    					//针对IE5,IE6
    					xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
    				}
    			//2.设置回调函数
    			xmlhttp.onreadystatechange = function() {
    				//5.处理响应数据  当信息全部返回，并且是成功
    				if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
    					var msg = xmlhttp.responseText;
    					document.getElementById("d").innerHTML = msg;
    				}
    			};
    			//GET，参数设置
    			xmlhttp.open("GET", "${pageContext.request.contextPath}/ajaxservlet");
    			xmlhttp.send(null);
    		};
    	};
    </script>
    </head>
    <body>
      	//超链接为链接到动作javascript:void(0)
    	<a href="javascript:get()" id="p">显示商品信息</a>
     	<a onclick="javascript:get(); return false;" href="#">显示商品信息</a>  
    	<div id="d"></div>
    </body>
    ```

  - AjaxServlet.java

  - ```java
    import cn.itcast.domain.Product;

    response.setContentType("text/html;charset=utf-8");
    List<Product> products = new ArrayList<Product>();
    ps.add(new Product(1, "洗衣机", 1800));
    ps.add(new Product(2, "电视机", 3800));
    ps.add(new Product(3, "空调", 5800));

    request.setAttribute("products", products);

    request.getRequestDispatcher("/product.jsp").forward(request, response);
    ```

  - product.jsp

  - ```jsp
    <table>
    	<tr>
    		<td>商品编号</td>
    		<td>商品名称</td>
    		<td>商品价格</td>
    	</tr>
    	<c:forEach items="${products}" var="product">
    		<tr>
    			<td>${product.id}</td>
    			<td>${product.name}</td>
    			<td>${product.price}</td>
    		</tr>
    	</c:forEach>
    </table>
    ```

  - ​

### 1.4.2 返回JSON格式数据的案例

#### json介绍

- 返回json格式的数据

  - 去掉了多余的html标签元素,只返回有效的数据部分

- json是一种JavaScript轻量级数据交互格式,主要用于ajax编程

  - **JSON : JavaScript 对象表示法（JavaScript Object Notation）**

- 格式一: **{key:value,key:value,...}** 键值对直接用

  - {key:'value'} {key:'value'} 键值对分开用(二者效果相同)
  - 键本身必须是字符串常量
  - 值加引号代表字符串常量,不加引号为变量

- 格式二: [值1,值2,值3] **数组结构**

  - 值为{key:value}键值对或者是[value1,value2]数组形式,只有value没有key
    - 但两种格式可以嵌套
  - 组合后复杂格式:
    - [{name:'aaa'}, {name:'bbb'}, {name:ccc}] : 表示三个对象数组

- ```json
  [{'id':'1','name':'洗衣机','price':'1800'},{'id':'2','name':'电视机','price':'3800'}]
  //在JavaScript中{name:value,name2:value}就是一个JavaScript对象
  //[{},{}]代表有两个对象装入一个数组
  ```

#### json应用场景: AJAX请求参数和响应数据

- json-lib插件

  - jsonlib是Java类库,支持**JavaBean,Map,List,Array**转换成json格式字符串,支持将json字符串转换成javabean对象

- 导入jsonlib包(导入6个包)

  - commons-lang-x.x.x.jar
    - 提供了一些java.lang额外的方法
  - commons-beanutils-x.x.x..jar
    - 操作JavaBean的类库,依赖于commons-collections
  - commons-collections-x.x.x.jar
  - commons-logging-x.x.x.jar
  - ezmorph-x.x.x.jar
    - 将一种对象转换成另一种对象的类库,是json的转换器
  - json-lib-x.x.jar
    - json的类库

- 将Array数组,List集合转换成json

  - ```java
    public static JSONArray fromObject(Object object)
    //JSONGArray的方法:创建一个JSONArray对象
    ```

  - ```java
    String[] array = {"aaa","bbb","ccc"};
    JSONArray jsonArray = JSONArray.fromObject(array);
    ```

  - ```json
    //json则为:
    ["aaa","bbb","ccc"]
    ```

- 将JavaBean,Map集合转换成json

  - ```java
    public static JSONArray fromObject(Object object)
    //JSONGArray的方法:创建一个JSONArray对象
    ```

  - ```java
    Product product1 = new Product();
    product1.setName("aaa");
    product1.setPrice(1000);

    JSONArray jsonProduct1 = JSONArray.fromObject(product1);

    Map<int,product> products = new HashMap();
    products.put(1,product1);
    products.put(2,product2);

    JSONArray jsonProducts = JSONArray.formObject(products);
    ```

  - ```json
    //json为:
    {"aaa":1000}
    [{"aaa":1000},{"bbb":2000}]
    ```

  - 如果JavaBean中有一个属性不想生成在json中,如何处理:

    - ```java
      //JsonConfig中设置不包括的key
      public void setExcludes(String[] excludes)

      //JSONArray转换时带入JsonConifg参数
      public static JSONArray fromObject(Object object,JsonConfig jsonConfig)
      //JSONArray对象转换成json字符串
      public String toString()
      ```

    - ```java
      JsonConfig config = new JsonConfig();
      //设置不包含的属性
      config.setExcludes(new String[]{"type"});
      JSONArray jsonProducts = JSONArray.formObject(products,config).toString();
      ```

- **jsonservlet中返回json数据**

- ```java
  //获取响应的输出,到时将数据输出在jsp页面特定的位置
  PrintWriter output = response.getWriter();

  //将要获取的数据以json方式保存,转换成json字符串
  JSONArray jsonProducts = JSONArray.fromObject(products);
  String json = jsonProducts.toString();

  //将json字符串传入响应输出流
  output.print(json);
  output.flush();
  output.close();
  ```

- **客户端将json字符串转换成javascript对象**

  - **使用JavaScript函数:`eval(string)`,将接收的json字符串传入string参数,求值**

    - `var json = eval(xmlhttp.responseText);`
    - 有些情况下，转换会出问题,使用如下方法
      - `var json = eval("("+xmlhttp.responseText+")");`

  - ```JavaScript
    //JavaScript中
    //设置回调函数
    function func() {
      if(xmlHttpRequest.readyState == 4) {
        if(xmlHttpRequest.status == 200) {
          var json = eval("(" + xmlHttpRequest.responseText + ")");
          //拼接返回的http标签,到时将message传入特定的位置
          var message = "<table><tr><td>商品编号</td><td>商品名称</td><td>商品价格</td></tr>";
          for(var i = 0; i<json.length; i++) {
          	message += "<tr><td>"+json[i].id+"</td><td>"+json[i].name+"</td><td>"+json[i].price+"</td></tr>";
          }
          message += "</table>";
          
          //获取id=div的标签,将message添入div中
          document.getElementById("div").innerHTML = message;
        }
      }
    }        
    ```

  - ```jsp
    //jsp中
    <body>
    	<a href="javascript:void(0)" id="p">显示商品信息</a>
    	<div id="div"></div>
    </body>      
    ```

### 1.4.3 返回XML格式数据的案例

- 当使用xmlHttpRequest.resopnseText返回的是字符串类型

- **使用xmlHttpRequest.responseXML返回的是Document对象**

  - 将xml文件中的内容取出来,写回到浏览器端;
  - 也可以将请求转发到xml文件,将这个文件信息写回到浏览器端
    - 写回时设置**response.setContextType("text/xml;charset=utf-8");**
  - 当没有xml文件时,使用xml插件转换:XStream

- XStream:实现**将序列化对象与XML格式数据互相转换**

  - 导包

    - 如果实现 java对象 --- xml  只需要 xstream-1.x.x.jar
    - 如果实现 xml ---- java对象 需要 **xstream-1.x.x.jar、xpp3_min-1.1.4c.jar**
      - xpp3:是一个xml取出解析器
    - xstream\lib下的jar包为可选依赖包

  - 核心方法:

    - XStream类的方法

    - ```java
      //将class类型解析,定义一个别名name
      public void alias(String name, Class type)
      //name - 定义的别名
      //type - 解析的类
      ```

    - **Java→xml: toXML()方法**

    - ```java
      //将对象序列化成XML格式字符串
      public String toXML(Object obj)
      ```

    - **xml→java:fromXML()方法**

    - ```java
      //将XML格式字符串解析成对象
      public Object fromXML(String xml)
      //将XML数据的输入流解析成对象
      public Object formXML(Reader reader)
      public Object fromXML(InputStream input)
      ```

- xstream使用:

  1. 将java对象转换成xml数据

     - ```java
       XStream xStream = new XStream();
       String xml = xStream.toXML(javaObject);

       //将转换的xml数据传入响应数据流写回客户端
       response.getWriter().write(xml);
       ```

  2. 提供便捷注解

     - ```java
       @XSTreamAlias("preson") 
       public class Person { ... }
       //对类设置别名,在类之前设置
       //相当于:
       xStream.alias("person",Person.class);

       @XStreamAsAttribute
       private String name;
       //设置变量在XML数据中作为属性

       @XStreamOmitField
       private int age;
       //设置变量不生成到XML数据

       @XStreamImplicit
       pirvate List<City> citys;
       //设置集合类型的变量
       //带括号则设置其别名
       @XStreamImplicit(itemFieldName = "city")
       ```

     - 在使用时使注解生效**xStream.autodetectAnnotations(true);**

       - ```java
         xStream.autodetectAnnotations(true);
         ```

  3. 使用responseXML接收解析成document对象

     - JavaScript中的回调函数:


     - ```JavaScript
       if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
       	//解析响应数据为document对象
       	var xml = xmlhttp.responseXML;
       					
       	var div=document.getElementById("div");
       	
         	//从解析的响应XML数据中获取各个数据
       	var persons=xml.getElementsByTagName("person");
       					
       	for(var i=0;i<persons.length;i++){
       						
       		var id=persons[i].getElementsByTagName("id")[0].innerHTML;
       		var name=persons[i].getElementsByTagName("name")[0].innerHTML;
       		var age=persons[i].getElementsByTagName("age")[0].innerHTML;
       						
       		div.innerHTML += "id:"+id+"&nbsp;name:"+name+"&nbsp;age:"+age+"</br>";
       					
       	}				
       }
       ```
    
     - jsp页面的显示:点击button后触发ajax
    
     - ```jsp
       <input type="button" value="接收xml" id="receive">
       <div id="content"></div>
       ```

### 1.4.4 ajax应用:省市联动

- select选项框联动(使用ajax + json)

  - 在domain层的类中都添加注解`@XStreamAlias("province")`,集合类对象设置注解`@XStreamImplicit`

  - 在jsp中设置ajax,页面一加载则向服务器获取省信息,当选择省份信息后触发省份改变,在城市框中获取城市信息

  - ```javascript
    //JavaScript中,jsp页面一加载就执行JavaScript方法
    //因为jsonObj使所有方法都要用到,所以设置为全局变量
    var jsonObj = null;

    //jsp页面一加载就执行JavaScript方法
    window.onload = func();

    //定义调用ajax方法
    function func(){
    	var province = document.getElementById("province");
      
    	//1.得到XMLHttpRequest对象
      	var xmlHttpRequest = null;
      	if(window.XMLHttpRequest) {
    		//针对大部分浏览器
    		xmlHttpRequest = new XMLHttpRequest();
    	} else if (window.ActiveXObject) {
    		//针对IE5,IE6
    		xmlHttpRequest = new ActiveXObject("Microsoft.XMLHTTP");
    	}
      
     	//2.设置回调函数
      	xmlHttpRequest.onreadystatechange = back();
      	//5.回调函数中处理响应数据
      	function back(){
          if(xmlHttpRequest.readyState == 4){
            if(xmlHttpRequest.status == 200){
              	//JavaScript调用eval方法解析json字符串为java对象
              	jsonObj = eval("(" + xmlHttpRequest.responseTest + ")");
              	
              	//遍历得到的省份集合对象
              for (var i=0; i<jsonObj.length; i++) {
              	//province对象getName()
                var provinceName = jsonObj[i].name;
                
                //创建省份select标签中的option标签
                var option = document.creatElement("option");
                //在option标签中填入省份名称
                //text 属性可设置或返回选项的文本值。该属性设置或返回的是包含在 <option> 元素中的纯文本。这个文本会作为选项的标签出现。
    		   option.text(provinceName);
                
                //将option加入province子标签
                province.add(option);
              }
            }
          }
      	}
      	//3.open,GET方式获取参数
      	xmlHttpRequest.open("GET","${pageContext.request.contextPath}/ajaxservlet");
      
      	//4.send(null)
      	xmlHttpRequest.send(null);
    }
    ```


```js
//定义点击省份后,调取省份对应的城市的方法
function fillCitys() {
	var province = document.getElementById("province");
	var city = document.getElementById("city");
  	
  	//每次调用此方法将city下来菜单先重置
  	city.innerHTML="<option>--请选择城市--</option>";
	
  	//获取当前的省份信息
	var provinceName = province.options[province.selectIndex].text;
  	//根据省份信息与jsonObj中的省份信息判断,相同时获取城市信息,并填入下拉列表
  	for(var i = 0; i< jsonObj.length; i++) {
    	var provinceNameInObj = jsonObj[i].name;
      	if(provinceNameInObj === provinceName) {
        	var citys = jsonObj[i].cityList;
          for (var j = 0 ; j < citys.length; j++){
            var cityName = citys[j];
            var option = doucument.createElement("option");
            option.text(cityName);
            city.add(option);
          }
        }
    }
     
}  
```


  - ```jsp
    //jsp页面中
    <script type="text/javascript" src="${pageContext.request.contextPath}/provinceAndCity.js"></script>

    <body>
    <select id = "province" onchange = "fillCitys()">
    	<option>--请选择省份--</option>  
    </select>

    <select id = "city">
    	<option>--请选择城市--</option>  
    </select>
    </body>

    ```

  - 在服务器端servlet上设置java对象转换成json,将json字符串传回客户端

  - ```java
    province.setName()
      cityList.add();
    	province.setCityList();
    		provinces.add();

    String jsonObj = JSONArray.fromObject(provinces).toString();

    response.getWriter().write(jsonObj);
    response.getWriter().close();
    ```

  - ​

---

# 2. Java基础加强

## 2.1 注解(annotation)(次重点)

### 2.1.1 什么是注解

- 格式:@xxx
- 注释:用于描述当前代码功能,是给程序员使用
- 注解:用于描述程序如果运行,jvm虚拟机读取的关于类如何运行的信息,是给编译器(如javac.exe),解释器(java.exe),jvm使用
- 典型应用:使用注解取代配置文件,采用**反射**技术读取注解信息

### 2.1.2 JDK自带的三个注解

1. @Override(覆盖)

   - 是给编译器使用
   - 表示被描述的**方法**是对父类方法的**覆盖**(方法重写)
   - jdk6中即可以描述是对继承的方法重写,也可以描述对实现的方法重写

2. @Deprecated(弃用)

   - 表示被描述的方法已经过时


   - 新的API取代旧的API,或者有些方法存在安全问题

3. @SuppressWarnings(压制警告)

   - 用于在类编译时忽略警告信息
   - 警告信息:
     1. unused:		变量未使用
     2. deprecation:  使用了不赞成使用的类或方法的警告
     3. unchecked:     执行了未检查的转换的警告,如使用集合时未使用泛型来指定集合中保存的数据类型
     4. fallthrough:     当Switch程序块直接通往下一个case时没有break;的警告
     5. path:                 在类路径,源文件路径中有不存在路径时的警告
     6. serial:                当在可序列化的类上缺少serialVersionUID定义时的警告
     7. finally:               任何finally语句不能正常完成时的警告
     8. all:                      关于以上所有情况的警告

### 2.1.3 自定义注解

1. 注解类定义(Annotation类)

   - 格式:@interface 关键字

     - 在类,方法,属性上直接@name

   - @interface MyAnnotation本质为一个接口

     - ```java
       import java.lang.annotation.Annotation;

       interface MyAnnotation extends Annotation { ... }
       ```

     - 自定义注解是一个接口,继承了Annotation接口,但不能手动实现

2. 注解中的成员

   1. 接口中的成员:
      1. 属性:默认自带: public static final
      2. 方法:默认自带: public abstract
   2. 注解成员:
      1. 可以有属性,基本不使用
      2. 可以有方法
      3. 一般使用注解时只研究注解的方法,叫做注解中的属性

   - 声明注解的属性

     - 注解属性的做用:原来写在配置文件中的信息,可以通过注解的属性进行描述

     - 注解属性的返回值类型:

       1. 基本类型(8种)
       2. String
       3. Class
       4. enum
       5. Annotation
       6. 以上类型的一维数组

     - annotation的属性声明方式: String name(); 

       - 返回值 名称();

       - 若注解中有属性但没有默认值,应当给注解属性赋值

       - 属性语法(属性默认声明方式): 返回值 方法名() default 默认值;

         - String name() defalut "test";

       - 特殊属性value:注解中若只有一个value属性,可省略"value="

         - ```java
           @MyAnnotation(value="test")
           @MyAnnotation("test")
           ```

   - **元注解**

     - 是Java API提供专门用来**定义注解类**的注解,用元注解来修饰自定义注解类
     - **@Retention**
       - 指定注解的信息在哪个阶段存在(注解给谁用)
         1. @Retention(**RetentionPolicy.SOURCE**)
            - 注解在给**编译器使用后**抛弃
         2. @Retention(**RententionPolicy.CLASS**)
            - 注解给**解析器**使用(class文件中可用),当jvm加载完后抛弃
         3. @Retention(**RententionPolicy.RUNTIME**)****
            - **在jvm中存在**,**可用反射获取注解信息**
     - **@Target**
       - 定义注解使用的位置,可用属性值在ElemenetType中
       - 例如:@Target(ElemenetType.TYPE)
         1. ElemenetType.CONSTRUCTOR    构造器声明 
         2. ElemenetType.FIELD                      域声明（包括 enum 实例） 
         3. ElemenetType.LOCAL_VARIABLE 局部变量声明 
         4. ElemenetType.METHOD                方法声明 
         5. ElemenetType.PACKAGE                包声明 
         6. ElemenetType.PARAMETER           参数声明 
         7. ElemenetType.TYPE                        类，接口（包括注解类型）或enum声明 
     - **@Documented**
       - 指定被修饰的注解类被javadoc工具提取成文档
     - **@Inherited**
       - 指定被修饰的注解类具有**被继承性**;应用此注解类的子类都会自动继承该注解
     - @Retention和@Target是自定义注解类必须使用的元注解

   - 自定义注解类:

     - 使用注解替换配置文件

     - ```java
       @Retention(RetentionPolicy.RUNTIME)
       @Target(ElementType.METHOD)
       public @interface BankInfo {
       	double maxMoney();
       }
       ```

3. 在目标类应用注解

   - ```java
     @BankInfo(maxMoney = 50000)
     public void transfer(String outAccount,String inAccount,double money){ ... }
     ```

4. 通过反射获取注解信息,控制目标类如何运行

   - JDK 5.0 在java.lang.reflect 包下新增了AnnotatedElement接口, 该接口代表程序中可以接受注释的程序元素

   - 当一个Annotation 类型被定义为**运行时Annotation(RUNTIME)** 后, 该注释才是运行时可见, 当class 文件被载入时保存在class 文件中的Annotation 才会被虚拟机读取

     - AnnotatedElement接口提供的方法:

       1. 获取指定注解的信息

          - ```java
            <T extends Annotation> T getAnnotation(Class<T> annotationClass)
            ```

       2. 判断该对象是否存在目标注解

          - ```java
            boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)
            ```

     - 反射,Class,Constructor,Field,Method都实现了此方法

   - 通过反射获取注解信息:

     1. 获得注解修饰目标对象对应的反射对象(Class,Constructor,Field,Method)

        - ```java
          //修饰类的注解获取
          Class clazz = Class.forName("cn.test.annotation.MyAnnotation");
          //修饰方法的注解获取
          Method method = MyAnnotation.class.getDeclaredMethod(String methodName,Class... paramClass);
          ```

     2. 判断目标对象上是否存在该注解`boolean hasAnnotation = isAnnotationPresent(MyAnnotation.class)`

     3. 获取该注解信息`MyAnnotation annotation = getAnnotation(MyAnnotation.class)`

     4. 使用这些注解信息(可选)

        1. `Annotation.xxx()`:获取注解属性xx的值

### 2.1.4 注解替换配置文件

- 配置文件用于降低耦合(解耦合)


- 没有配置文件的时候:数据在程序代码中,不方便修改
- 有配置文件时:动态数据提取到配置文件中,通过修改配置文件来修改程序执行,方便修改
- 当系统业务复杂后:配置文件越来越大,不利于维护
  - **注解用于简化配置文件,解决了配置文件维护不便,可读性差的问题**
- 1. 要用好注解，必须熟悉java 的反射机制，**注解的解析完全依赖于反射**。
  2. 不要滥用注解。平常我们编程过程很少接触和使用注解，**只有做设计，且不想让设计有过多的配置时再考虑使用注解**。

## 2.2 Servlet3.0特性(了解)

### 2.2.1 注解代替配置文件

- JDK5及之前,servlet的配置文件web.xml中:

  - ```xml
    <servlet>
       <servlet-name></servlet-name>
       <serlvet-class></servlet-class>
    </servlet>

    <servlet-mapping>
       <servlet-name></servlet-name>
       <url-pattern></url-pattern>
    </servlet-mapping>

    ```

- JDK6之后,使用注解替代配置文件

  1. Servlet

     - @WebServlet(name = "xxx", urlPatterns = "/xxx")

     - ```java
       @javax.servlet.annotation.WebServlet(name = "UploadServlet", urlPatterns = "/uploadservlet")
       public Class UploadServlet extends javax.servlet.http.HttpServlet { ... }
       ```

  2. Filter

     - @WebFilter(name = "xxx", urlPatterns = "/xxx")

     - ```java
       @WebFilter(filterName = "TestFilter", urlPatterns = "/testfilter")
       public class TestFilter implements Filter { ... }
       ```

  3. Listener

     - @WebListener

     - ```java
       @WebListener()
       public class TestListener implements ServletContextListener,
               HttpSessionListener, HttpSessionAttributeListener {
       ```


  ​

### 2.2.2 异步支持

- Servlet3.0允许在服务器端接收到请求后,Servlet线程将耗时的操作委派给另一个线程来完成,在不影响响应的情况下返回至容器

- 优点:当业务处理高资源消耗时,将减少服务器资源的占用,提高并发处理速度(用"空间"换"时间")

- @WebServlet和@WebFilter两个注解都支持异步支持属性:asyncSupported

- **asyncSupported**:

  1. 对需要添加异步支持的servlet或filter添加异步支持

     - ```java
       @WebServlet(asyncSupported = true)
       @WebFilter(asyncSupported = ture)
       ```

  2. 开启异步线程

     - 需要一个异步上下文对象AsyncContext,启动一个新线程用于运行这个对象

       - request的startAsync()方法`AsyncContext startAsync()`:将原始的request和response设为异步模式

     - ```java
       AsyncContext asyncContext = request.startAsync();
       ```

     - 通过这个对象,可以获取request,response对象,进而进行其他操作

     - ```java
       //获取对象
       HttpServletRequest asyncRequest = asyncContext.getRequest();
       HttpServletResponse asyncResponse = asyncContext.getResponse();
       ```

     - 通过这个对象可以对异步上下文进行监听

       - AsyncListener的`void onComplete(AsyncEvent event)`方法用于判断异步结束,异步执行完成

       - AsyncListener的`void onStartAsync(AsyncEvent event)`方法用于判断异步开始

       - `void onError(AsyncEvent event)`:异步线程出错

       - `void onTimeout(AsyncEvent event)`:异步线程超时

       - ```java
         AsyncContext asyncContext = request.startAsync(); 
         asyncContext.addListener(new AsyncListener() { 
             public void onComplete(AsyncEvent asyncEvent) throws IOException { 
                 // 做一些清理工作或者其他
             } 
             ... 
         });
         ```

### 2.2.3 文件上传支持

- @MultipartConfig

  - 表示Servlet接收"multipart/form-data"请求
  - 此时request将根据multipart/form-data格式进行解析数据

- 使用request.getParameter()获得普通表单域内容(表单中的字段值)

  - 消除乱码:`request.setCharacterEncoding("utf-8");`
  - 注意:getParameter使用必须在getPart之前，文件乱码处理才会生效

- 获得文件上传域数据:使用**Part接口**

  - part对象代表接收的上传文件数据

  - 先获取表单数据后,再获取上传数据

  - ```java
    //request中的方法
    Part getPart(String name)
    //name - 表单中的name属性值
    Collection<Part> getParts()
    //获取所有的part对象
    ```

  - Part接口方法:

    - 获得上传文件的Content-Disposition头信息,包含了上传文件的真实文件名

    - ```java
      part.getHeader("Content-Disposition");
      //form-data; name="f";filename="C:\Users\Administrator\Desktop\abc.PNG"
      ```

    - 通过`substring(start,end)`和`lastIndexOf("\\")+1`截取到真实文件名

    - 上传part中的文件

    - ```java
      void write(String fileName)
      //fileName - 在服务器端的文件地址
      ```

    - ​

  - 浏览器端:

    1. method=post
    2. encType="multipart/form-data"
    3. 使用`<input type="file" name="f">` 

  - 服务器端:

    1. 添加注解@MultipartConfig
    2. 通过request获取Part对象,getPart(xxx)
    3. 上传文件,文件名通过part的getHeader("Content-Disposition")获取头数据后,截取到文件名
    4. 保存上传的文件,使用代表上传文件数据的part对象的write(name)方法,name为上传文件保存地址
    5. 若上传多个文件则使用request.getParts()返回part对象的集合

## 2.3 动态代理(重点)

### 2.3.1 动态代理概述

#### 代理模式

- 拦截对真实业务对象的访问,让程序更加安全

- 可以对真实行为的调用进行控制

- 实现:
  - 编写一个代理类,用于生成代理对象
  - 代理类中要实现和被代理类相同的接口
  - 代理类中持有被代理的对象
  - 代理类中调用被代理的方法

- 代理原理:程序获得真实业务对象,通过真实业务对象生成代理对象,**通过代理对象间接访问真实业务对象**

- ```mermaid
  graph TD;
  	用户-.真正想要访问.->UserImpl;
  		用户--真正可以访问-->UserProxy;
  	User接口--实现类-->UserProxy;
  	User接口--实现类-->UserImpl;
  	UserProxy-.代理实现.->UserImpl;

  	
  ```

- ​

#### 动态代理

- 在代理模式的基础上,对**任意一个实现了接口的类**的对象做代理
  - 代理类与被代理目标类实现同一个接口
- Java中提供了一个**Proxy类**,调用其**newInstance方法**可生成摸个对象的代理对象

### 2.3.2 动态代理的实现

- java.lang.reflect.Proxy类

  - Proxy 提供用于创建动态代理类和实例的**静态方法**，它还是由这些方法创建的所有动态代理类的超类

- **newProxyInstance方法**

  - ````java
    public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)
      
    loader - 定义代理类的类加载器
    interfaces - 代理类要实现的接口列表
    h - 指派方法调用的调用处理程序 
    ````

  - ClassLoader:被代理对象的类加载器

    - 通过被代理的对象的class对象,再通过Class类中的getClassLoader()方法

    - ```java
      public ClassLoader getClassLoader()
      ClassLoader loader = test.getClass().getClassLoader();
      ```

  - interfaces:被代理对象实现的所有接口

    - 通过被代理对象的class对象,再通过Class类中的getInterfaces()方法

    - ```java
      public Class<?>[] getInterfaces()
      Class[] interfaces = test.getClass().getClassLoader();
      ```

  - h:被代理对象方法的调用处理程序

    - InvocationHandler:是代理对象的**调用处理程序实现**的接口

    - InvocationHandler的方法invoke用于代理方法的执行

    - ```java
      Object invoke(Object proxy,Method method,Object[] args)
      proxy - 调用方法的代理对象
      method - 要调用的方法
      args - 要调用的方法要传入的参数，如果接口方法不使用参数，则为 null。基本类型的参数被包装在适当基本包装器类（如 java.lang.Integer 或 java.lang.Boolean）的实例中。 
      ```

    - invoke()方法的三个参数由代理对象自动调用,无需指定

    - 第二个参数method也有一个方法invoke(),同名但无关,时用于调用目标类的目标方法

      - ```java
        Method类对象调用的invoke()
        public Object invoke(Object obj,Object... args)
          obj - 被代理的目标对象
          args - 目标对象的方法,相当于上一层的invoke()方法的第三个参数Object[] args
        ```

      - 此方法调用目标对象的方法,在代码中一般的写法为:

      - ```java
        method.invoke(target,args);
        ```

      - ​

- 动态代理、包装、继承都是Java中 方法增强的手段 ,但是**动态代理和包装**灵活性要比继承更好

###2.3.3 动态代理案例

#### 动态代理实现编码过滤

- 创建一个过滤器用于统一编码

```java
// 创建一个request对象的代理对象requestProxy
ClassLoader loader = request.getClass().getClassLoader();
Class[] interfaces = request.getClass().getInterfaces();
HttpServletRequest requestProxy = (HttpServletRequest)Proxy.newProxyInstance(loader, interfaces,
	//使用匿名内部类
	new InvocationHandler() {
      	 @Override
		public Object invoke(Object proxy, Method method,
							Object[] args) throws Throwable {
			//得到方法名称
			String methodName = method.getName();
          	 //通过方法名称对比来执行相应的程序
			if ("getParameter".equals(methodName)) {
              	  //args[0]为此时的参数
				String param = request.getParameter((String) (args[0]));
				//将原先request.getParameter(xxx)方法的返回值更改为新的返回值
				return new String(param.getBytes("iso8859-1"),"utf-8");
			} else {
				// 不是getParameter方法，就执行其原来操作.
				return method.invoke(request, args);
			}
		}
	});
// 3.放行
chain.doFilter(requestProxy, response);
```

- 不使用匿名内部类而定义一个业务类实现InvocationHandler接口

- ```java
  public class MyExtension implements InvocationHandler {
  	//定义一个Object类型的代理目标对象
    	//定义成Object可适用于本项目中任何类型的目标类
    	private Object target; 
    	
    	public MyExtension {}
    	//带入被代理对象的构造
    	public MyExtension(Object target){
      	this.target = target;
      }

    	//重写invoke方法
    	@Override
      public Object invoke(Object proxy,Method method,Object[] args) throws Throwable{
  		...
            return method.invoke(target,args);
      }
  }
  ```

- 此时在客户类中**定义目标对象和代理对象,并用代理对象调用主业务方法**

- ```java
  //定义要代理的目标对象
  User target = new User();
  //定义代理对象,带入参数
  User proxy = (User) Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),new MyExtension(target));

  //用代理对象调用主业务方法
  proxy.xxx();
  ```

## 2.4 CGLIB 动态代理

### 2.4.1 CGLIB概述

- CGLIB : Code Generation Library : 代码生成类库
- JDK的Proxy实现代理是通过相同的接口,若目标类无接口则无法被代理
- CGLIB可以**对无接口的类实现代理**
- CGLIB原理:**生成目标类的子类**,这个子类就是被增强过的代理类
  - 所以目标类必须可以被继承,**不能是final类**

### 2.4.2 cglib实现代理

1. 导入cglib的[jar包](https://github.com/cglib/cglib/releases):cglib-nodep-3.2.5.jar

2. 定义目标类,不用实现接口

   1. ```java
      //目标类,实现银行客户功能
      public class AccountService {
      	//目标方法,转账和查询余额
        	public void transfer(){ 
          	//调用dao层完成转账 
          }
        	public void getBalance(){ 
          	//调用dao层完成查询余额
          }
      }
      ```

3. 创建代理类的工厂,用于"生产"代理类

   1. 该类要实现**MethodInterceptor**接口,方法拦截器

      1. 实现MethodInterceptor接口完成三个工作:
         1. 声明目标类的成员变量,创建带参构造,参数为此成员变量
         2. 定义方法用于生成代理类对象(创建增强器),设置代理类为目标类的子类
            1. 增强器用于生成代理类对象
               1. 创建增强器对象:`Enhancer enhancer = new Enhancer();`
               2. 初始化增强器
                  1. 增强器设置父类:`enhancer.setSuperclass(Class fatherClass);`
                  2. 增强器设置回调接口:`enhancer.setCallback(this);`
               3. 获取增强器创建的代理对象:`Object proxy = enhancer.create();`
            2. 增强器对象要设置回调接口对象为自己**setCallback(this)**
         3. 重写**回调方法**,在此完成对目标类的**增强**

   2. ```java
      //cglib代理类的工厂,用于生成目标类的子类,即代理类
      public class AccountServiceCglibProxyFactory implements MethodInterceptor {
      	//声明目标类的成员变量,私有
        	private AccountService target;
        	//带参构造,构造目标类的代理工厂
          public AccountServiceCglibProxyFactory(AccountService target){
      		this.target = target;
          }
        
      	//定义生成代理对象的方法
          public AccountService creatProxy(){
      		//创建增强器
          	Enhancer enhancer = new Enhancer();
          	//设置父类,即目标类
            	enhancer.setSuperclass(AccountService.class);
           	//设置回调
            	enhancer.setCallback(this);
            	//获取增强器生成的代理对象
            	return (AccountService)enhancer.create();
          }
        
        	//定义回掉接口方法→增强方法
        	@Override
          public Object intercept(Object proxy,Method method,Object[] args,MethodProxy methodProxy) throws Throwable {
      		//当方法名为transfer时,增强方法
              if("transfer".equals(method.getName()){
      		...
              	return xxx;
              }
                 
               //其他不增强的方法直接执行目标类方法
              return method.invoke(target,args);
              return methodProxy.invokeSuper(proxy,args);//两个方法效果相同,一个是反射执行目标类的方法,一个是通过执行代理类的父类的方法,也就是目标类的方法
          }
          //proxy - 代理对象
          //method - 代理对象的方法
          //args - method的参数
          //methodProxy - method的代理对象
          
      }
      ```

4. 创建测试类(实际使用时是客户类)

   1. ```java
      //创建目标对象
      AccountService target = new AccountService();
      //创建代理对象,通过工厂生成
      AccountService proxy = newAccountServiceCglibProxyFactory(target).createProxy();
      //执行代理对象的方法,其实实际上是执行增强过的目标对象的方法
      proxy.transfer();
      proxy.getBalance();
      ```

### 2.4.3 设计模式之方法回调设计模式

- 类A调用类B的方法b,之后类B反过来调用类A的方法a;这个被B调用的方法a即为类A的回掉方法
- Java接口可方便设置回调方法:
  - 定义一个回掉接口,在接口中定义一个希望回调的方法
  - 例如:上例的工厂类相当于类A,工厂类的intercept()方法是Enhancer类调用的,即intercept()方法是代理对象调用的,工厂类调用Enhancer类的setCallback(this)方法,把回调对象this作为实参传递给了Enchancer类,最终代理对象(即为Enchancer类)调用intercept()方法

## 2.5 类加载器(了解)

### 2.5.1 类加载器概述

- 用于读取.class字节码文件,将其加载到内存中,并生成对应的Class对象
- 当JVM启动时,会形成三个类加载器组成初始化类加载器:
  - JVM类加载器初始化层次结构
    1. BootStrap 引导类加载器
       - jre/lib/rt.jar及其他该目录下的jar包
    2. ExtClassLoader 扩展类加载器
       - jre/lib/ext/目录下的所有jar包
    3. AppClassLoader SystemClassLoader应用类加载器(系统类加载器)
       - CLASSPATH指定的所有jar或目录,当前工程所需的jar包
    4. 自定义类加载器:加载指定目录
  - 所有的类加载器都是ClassLoader的子类(BootStrap加载器除外)

### 2.5.2 初始化类加载器

#### BootStrapClassLoader

- **负责加载Java核心类**
- 是由JVM自身实现,而不是ClassLoader的子类

####Extension ClassLoader

- 负责加载jre的扩展目录的jar包,这个目录的jar包对所有的jvm和系统类加载器都可见

#### System ClassLoader

- 负责加载工程所需的jar包
- 可通过静态方法ClassLoader.getSystemClassLoader()获取此加载器
- 用户自定的类加载器默认继承此加载器

### 2.5.3 全盘负责委托机制

- 全盘负责:当一个classLoader加载一个class时,这个class所依赖和引用的其他class都由这个classLoader负责加载
- 委托机制:先用父类的类加载器加载,父类无法加载时再用自己的类加载器加载
- 类加载的高速缓存(cache)机制:若cache中保存了这个Class就直接使用,如果没有才加载并存入高速缓存
  - 因此一个类只会被加载一次

## 2.6 泛型反射(次重点)

