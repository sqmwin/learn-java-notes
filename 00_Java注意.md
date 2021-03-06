[TOC]

本文摘抄自[编程随想的博客](https://program-think.blogspot.com/)

# 0. 学习技术的三部曲:WHAT,HOW,WHY

　　最近几天有些网友在邮件里面问我关于学习的问题。有好几个人觉得工作了几年，也学会了不少的类库、框架、甚至语言，但是感觉自己的能力没有太大的提高。因此今天来说一下我个人对这方面的体会，希望对大伙儿（尤其是新手）有帮助。

　　先声明一下，本帖子讨论的三部曲是指你已经选定了某个技术方向之后，该如何学习；至于如何选定技术方向，则属于另一个话题，不在今天的讨论之列。

　　我把学习归类为三个步骤：What、How、Why。经过我对周围同事和朋友的观察，大部分感觉自己技术没有提高的人，都仅仅停留在What阶段。下面我把这三个步骤解释一下。

## ★第一步：WHAT

　　所谓的“WHAT”也就是“What is it?”——这是最简单的层次。在这个层次，你要搞清楚某个东东是【什么】样子的？有【什么】用处？有【什么】特性？有【什么】语法？......

　　举例如下：

> 对于学习语言（比如 C++、Java、Python），大部分人都能够掌握基本的语法和标准库，然后用它写一些小程序（诸如二分查找、冒泡排序、简单文件操作等）。
> 对于学习类库（比如 JDBC 类库），大部分 Java 程序员都能明白 JDBC 主要包含哪些类，也能够用 JDBC 进行简单的数据库查询和增删改操作。

　　由于这个步骤是最基本的，假如你连这都做不到（可能你的理解力不够好），也别在IT界混了。

　　但是光会 What 是不够的。仅仅停留在这个步骤，导致了很多程序员【只知其然，不知其所以然】。这就是目前大部分开发人员的现状。

## ★第二步：HOW

　　所谓的“HOW”就是“How to do?”。在这个层次，你要搞清楚某个东西，其内部是【如何】运作的？【如何】实现的？......

　　举例如下：

> 假如你在学习 C++ 语言，你是否搞明白函数传参数的实现机制？虚函数是如何实现？抛出异常时的栈回退是怎么回事？......
> 假如你在学习 Java 语言，你是否搞清楚 GC 如何实现？反射是如何实现？......
> 假如你在学习 JDBC 库，你是否清楚 JDBC Driver 的4种类型？不同游标类型的实现机制？事务的机制？......

　　在这个阶段，你必须多想想类似这些问题。然后通过各种途径（参见“关于自学能力”的几个方法），把问题彻底搞清楚。自然而然，你的提高就会比较明显。而且如果碰到一些深层次的问题（比如性能优化），也就知道该如何去解决。

　　完成这个阶段之后，你基本上就属于该技术领域最优秀的20%的人（根据[二八原理](#4. 无处不在的二八原理)，80%的人不会去思考 HOW 的问题）。

## ★第三步：WHY

　　一般来说，只有想清楚 HOW 之后，才能继续去考虑 WHY。

　　所谓的“WHY”，就是搞清楚某个东西【为什么】设计成这样？【为什么】不是另外的样子？这样的设计有什么讲究？......

　　说实在的，善于问“为什么”有一定的天赋成分？好像某个科学大牛曾经说过“提出问题有时候比解决问题更难”。一般来说，只有当你【深刻理解】了某个东西，才能够针对这个东东的【设计】问出一些问题。所以，我前面强调过，要先把 HOW 的问题搞清楚，再来考虑 WHY 的问题。

　　举例如下：

> 对于C++语言：为什么 C++ 没有类似 Java 的 finally 关键字？为什么当初发明 C++ 的时候没有考虑 GC？......
> 对于Java语言：为什么 Java 没有类似 C++ 的类析构函数？为什么 Java 要同时提供 String 和 StringBuffer 两个似乎冗余的类？......
> 对于Python语言：为什么 Python 不提供类似 C++/Java 的访问控制机制？......

　　如果你能够【自己】问出诸如上述的“为什么”问题，并且能够通过各种途径找到解答，那你基本上已经吃透这个技术了，并且你已经【有可能】自己去【设计】一个类似的玩意儿了。到这时，你已经踏上了通向技术高手的康庄大道。

　　由于本博客偏重 IT 方面，所以今天举的这些例子多半都是 IT 相关的，但是这个三部曲在 IT 之外的行业和领域，其实也能适用。如何举一反三，就看各位的悟性了。

# 1. Java新手的通病

## 1.1 对算法和数据结构不熟悉

- 为什么我先拿“数据结构和算法”说事捏？这玩意是写程序最最基本的东东。不管你使用 Java 还是其它的什么语言，都离不开它。而且这玩意是跨语言的，学好之后不管在哪门语言中都能用得上。


- 既然“数据结构和算法”这么重要，为什么很多 Java 新手却很不熟悉捏？我琢磨了一下，估计有两种可能。有些人虽然是计算机系毕业的，但是当初压根没好好学过这门课程，到工作时早都还给老师了；还有一些人是中途转行干编程，转行后又没有好好地打基础（都指望速成）。
- 下面我列出几个很基本的问题，如果你每一个问题都搞得很清楚，那说明你过了这关，可以去看看下一个帖子了。否则的话，你赶紧去找本算法和数据结构的书恶补一下吧。

### ★什么时候该用数组型容器、什么时候该用链表型容器？



### ★什么是散列函数？HashMap 的实现原理是什么？



### ★什么是递归？如果你以前从来没写过递归函数，尝试着写一个（比如用递归函数进行目录树遍历）。



###★什么是算法复杂度？



### ★你是否理解空间换时间的思想？



### ★写一个针对整数数组的冒泡排序函数，看看你要修改几次才能跑通。



### ★写一个针对整数数组的二分查找函数，看看你要修改几次才能跑通。



## 1.2 缺乏面向对象的基本功

- 按理说 Java 是一个很 OO 的语言，Java 社区也一向是充满了“对象”的氛围。但俺在面试 Java 程序员时，却屡屡碰到令人大跌眼镜的事情。俺碰到不止一个求职者，连什么是“多态”都讲不清楚。很多人号称用过设计模式，但一半以上都仅限于单键模式和抽象工厂模式。当我深入问他/她抽象工厂模式到底有什么好处时，很多人语焉不详。
- 为什么很多 Java 程序员会缺乏面向对象基本功？这得怪那些 Java 框架。现在 Java 的各种框架太发达、太傻瓜化了，导致很多程序员只需要按部就班、照着框架进行代码填空，基本已经丧失了 OOA 和 OOD 的能力。我手下有些个 Java 程序员，对 Spring、Hibernate 等框架了如指掌；但如果给他一个简单需求，让他写一个脱离 Web 框架的独立 Application，他就不知所措了。这样的开发人员，将来只能成为所谓的“软件蓝领”，岗位很难得到提升。

###★基于接口的继承和基于实现的继承各有什么优缺点？

###★继承（包括 extend 和 implement）有什么【缺点】？

###★多态（polymorphism）有什么【缺点】？

###★为什么 Java 可以多继承 interface，而不可以多继承 class？

###★假如让你写一个小游戏（比如人机对战的五子棋），你会如何设计类结构？

###★类结构设计时，如何考虑可扩展性？

- 如果上述这些问题你都能够搞得比较清楚，说明你的 OO 基础还过得去。否则的话，我建议你一边找些 [OOAD](https://en.wikipedia.org/wiki/Object-oriented_analysis_and_design) 和设计模式的书看看，同时自己动手写些简单的小程序（不依赖那些框架），把学到的模式理论结合到实践中。通过这种方式来提高自己 OOAD 的能力，效果会比较好。

## 1.3 缺少良好的编程习惯

### ★随意地命名

　　有些新手写程序，当需要定义某个变量名（也可能是函数名、类名、包名等）时，随意地一敲键盘，名字就起好了......若干星期后，碰到某 bug，再来看自己写的代码时，心中暗自嘀咕：“这代码是我写的吗？咋都看不懂捏？”

　　所以我常跟新来的菜鸟说，命名不规范害死人啊！鉴于该问题相当普遍，我整理了几种典型的作为反面教材，具体如下：使用单字母命名变量；使用一些没太大意义的变量名（例如 s1、s2、s3）；对同一个业务概念使用不同的术语/缩写（容易让读代码的人神经分裂）；使用拼音命名（如果你团队中有港台人士或者老外，就惨了）。

### ★习惯于代码的 copy & paste

　　这是一个很普遍的问题。很多新手写代码的时候，如果发现要写的某个函数和前几天写的某个函数差不多，就把原来的那个函数贴过来，然后稍微改几下，心中还暗喜：“又快速搞定了一个功能”......

　　同学，如果你也喜欢这么干，可要注意了。这种做法是代码臭味（借用《重构 - 改善既有代码的设计》的提法）的主要来源，导致代码可维护性大大下降。当你将来需要增加功能或修改 bug 的时候，要同时改动多个地方，而那时你估计已经想不起来这砣代码有几个克隆了。

### ★Magic Number 满天飞

　　如果你没有听说过“Magic Number”，先看[这里](https://en.wikipedia.org/wiki/Magic_number_(programming)#Unnamed_numerical_constant)了解一下。

​	为了说明Magic Number的问题，咱找个例子来说事儿：假设有个业务逻辑中需要进行10秒的超时等待，你会怎么写这个sleep语句？我估计大部分人不外乎下面三种写法：

1、直接写上 `sleep(10*1000);` 了事

2、定义一个常量 `TIMEOUT_XXX = 10*1000;`

 然后 `sleep(TIMEOUT_XXX);`

3、在配制文件中加入一个超时项，然后程序读取配制文件获得超时值，然后调用 sleep。（此处提到的“配置文件”是广义的，泛指各种可用于存储配置信息的机制，如：xml 文件、ini 文件、数据库 ...）

　　如果你的做法类似于写法1，你多半喜欢随手硬编码。硬编码不光缺乏可读性，而且具有和“代码拷贝粘贴”类似的代码臭味（可能会存在多个 Magic Number 克隆），不利于日后维护。

　　至于写法2，比写法1稍好（至少可读性好了）。但是，将来一旦发生需求变更，要求在【运行时】调整超时间隔（甚至要求让用户来配制超时间隔），则写法2的缺点立马暴露无遗。

### ★代码耦合度太大

　　每当说到 MVC 或者设计模式，几乎每个 Java 开发人员都能说得头头是道？但是说归说，真正写代码的时候，鲜有人写出的代码是层次清楚的。至于说到代码耦合分别由哪些情况引起？什么是正交的设计？（关于耦合与正交设计，我后面会专门讨论一下）能完全搞明白的人就更少了。

　　所以很多 Java 新手的代码耦合度大也就不足为奇了。我曾经抽查过试用期员工的代码，各种业务逻辑纠缠在一起，代码臭味都要熏死人。想重构都无从下手，只好让他推倒重写。

### ★被 GC 宠坏

　　由于 Java 在语言层面提供了内存的垃圾回收机制，程序员只管申请内存，不需要再关心释放的问题。因此很多新手养成了坏习惯，对于其它资源（比如数据库连接）也只申请不释放（有些人甚至天真地以为 JVM 会帮你搞定资源回收）。

　　还有些人虽然知道资源需要释放，但是常常忘记（比如写了打开数据库连接和相关代码，【即将】写关闭数据库连接时，突然有人叫你去吃中饭，回来后就把这茬给忘了）。

　　这个坏习惯会导致资源的泄露，而资源泄露往往比内存泄露更要命。如果你写的程序是长时间运行的（比如运行在 Web Server 上），时间长了会由于资源耗尽而导致整个进程出问题。

## 1.4 异常处理使用不当

 ### ★空的 catch 语句块

　　犯这种错误的人比较少，一般发生在刚学会 Java 或者刚参加工作不久的人身上。

　　所谓“空 catch 语句块”就是在 catch 语句块中没有对异常作任何处理（比如记错误日志），导致异常信息被丢弃/忽略。一旦程序不能正确运行，由于查不到任何 log 信息，只好从头看代码，靠肉眼找 bug。

### ★没有使用 finally

　　很多人在 catch 语句之后不使用 finally 语句。由于在 try 语句中可能会涉及资源的申请和释放。如果在资源申请之后、资源释放之前抛出异常，就会发生资源泄露。

 ### ★笼统的 catch 语句块

　　有些人为了省事，只在自己模块的最外层代码包一个 try 语句块，然后 catch(Exception)。不管捕获到什么异常，都作统一 log 了事。这种做法比“空 catch 语句块”稍好，但由于不能对具体的异常进行具体处理，对一些可恢复的异常（下面会提到），丧失了恢复的机会。而且也可能导致上述提到的资源泄露的问题。

 ### ★使用函数返回值进行错误处理

　　有些人放着 Java 的异常机制不用，而用函数返回值来表示成功/失败（比如：返回 true 表示成功、false 表示失败），简直是“捧着金碗要饭”。个人感觉，从 C 转到 Java 的人比较容易有此毛病。这种做法会导致如下几个问题：

1. 返回值一般用整数值或布尔值表示，传递的信息过于简陋；


2. 一旦调用者忽略了错误返回码，就会导致和“空 catch 语句块”类似的问题；
3. 对同一个函数的多处调用，都需要对返回值进行重复判断，导致代码冗余（代码冗余的坏处，

上一个帖子

也已经聊过了）。

### ★不清楚“Checked Exception”和“Runtime Exception”的区别

　　这个现象比较普遍，俺发现很多2年以上 Java 工作经验的人尚未完全搞明白两者的区别。看来这个问题得详细说一下。

当初Java的设计者有意区分这两种异常，是别有深意的。其中“Checked Exception”用于表示可恢复的异常（也就是你必须检查的异常）；而“Runtime Exception”表示不可恢复的异常（也就是运行时异常，主要是程序 bug 和致命错误，你【不需要】检查）。不过这种做法引来了很多争议（包括很多 Java 大牛），鉴于本帖子主要针对新手，以后再专门来聊这个争议的话题。

　　为了便于理解，下面我举一个例子来说明。假设你要写一个 Download 函数，根据传入的 URL（String 参数）返回对应网页的内容文本。这时候有两种情况你需要处理：

1. 如果传入的 URL 参数是 null，这表明该函数的调用者出 bug 了，而程序本身的 bug 是很难在运行时自我恢复的。这时候 Download 函数必须抛出 Runtime Exception。并且 Download 函数的调用者【不应该】尝试去处理这个异常，必须让它【尽早】暴露出来（比如让 JVM 自己终止运行）。
2. 如果传入的 URL 参数非 null，但是它包含的字符串不是一个合法的URL格式（可能由于用户输入错误导致）。这时候 Download 函数必须抛出 Checked Exception。并且 Download 函数的调用者必须捕获该异常并进行相应的处理（比如提示用户重新输入 URL）。

## 1.5 对虚拟机了解不足

​	上次的帖子讨论了Java异常机制的几种误用，今天咱们来说说 JVM（以及 Java 编译器）相关的话题。为啥要聊 JVM 捏？因为有很多 Java 程序员，由于对 JVM 缺乏了解，在碰到某些技术问题时无从下手；另外，由于缺乏对 JVM 的了解，可能导致写出来的代码性能巨差或者有严重的 Bug。所以俺在之前的帖子[学习技术的三部曲：WHAT、HOW、WHY](#0. 学习技术的三部曲:WHAT,HOW,WHY)中，强调了掌握内部机制的重要性。对于一个 Java 程序员来说，你不一定要非常清楚 JVM 的细节，但是对于一些关键的运作机制，还是要掌握大致的概念。

　　按照本系列的惯例，俺会问几个和 JVM 相关的问题，你如果对这些问题不是很明白，那得考虑花点时间去了解一下了。另外，鉴于有网友批评本系列帖子：光诊断毛病，不开出药方。（说得很形象，也很中肯）俺会针对下面提出的问题，写一些帖子来解答。

### ★关于基本类型和引用类型

很多新手不理解Java的基本类型和引用类型在本质上有什么区别。请看如下的问题：

◇这两种类型在内存存储上有什么区别？

◇这两种类型在性能上有什么区别？

◇这两种类型对于 GC 有什么区别？

关于前两个问题，请看帖子[Java性能优化[1]：基本类型 vs 引用类型](##2.1 基本类型 vs 引用类型)。

### ★关于垃圾回收（Garbage Collection）

　　很多新手不理解 GC 的实现机制。请看如下的问题：

◇GC 是如何判断哪些对象已经失效？

◇GC 对性能会有哪些影响？

◇如何通过 JVM 的参数调优 GC 的性能？

　　关于 GC 的问题，可以参见之前的帖子[Java性能优化[3]：关于垃圾回收（GC）](## 2.3 垃圾回收(GC))。

### ★关于字符串

　　对于 Java 提供的 String 和 StringBuilder，想必很多人都知道：String 用于常量字符串，StringBuilder 用于可变字符串。那 Java 当初为什么要这样设计捏？为啥不用一个类来统一搞定捏？

### ★关于范型（Generic Programming）

　　从 JDK 1.5开始，Java 引入了一个重量级的语法：范型。不过捏，很多新手仅仅知道范型的皮毛，而对于很多本质的东东，不甚了解。

◇GP 是在编译时实现的还是在运行时实现的？为什么要这么实现？

◇GP 的类型擦除机制是咋回事？有啥优点/缺点？

◇使用范型容器（相对于传统容器）在性能上有啥影响？为什么？

### ★关于多线程

　　另外，多线程也是大部分 Java 新手的短板。所以俺最后再来提几个关于多线程的问题。

◇synchronized 关键字是怎么起作用滴？

◇synchronized 的颗粒度（或者说作用域）如何？是针对某个类还是针对某个类对象实例？

◇synchronized 对性能有没有影响？为什么？

◇volatile 关键字又是派啥用滴？啥时候需要用这个关键字捏？

# 2. Java性能优化

## 2.1 基本类型 vs 引用类型

​	在 Java 性能优化系列中，内存管理是一个要优先考虑的关键因素。而说到内存分配，就必然会涉及到基本类型和引用类型。所以我们今天就先来介绍一下这两种类型在性能方面各自有什么奥妙（关于引用类型的其它奥妙，请看“[这里](#3. Java新手进阶:细说引用类型)”）。

###★名词定义

　　先明确一下什么是“基本类型”，什么是“引用类型”。

　　简单地说，所谓基本类型就是 Java 语言中如下的8种内置类型：

> boolean
> char
> byte
> short
> int
> long
> float
> double

　　而引用类型就是那些可以通过 new 来创建对象的类型（基本上都是派生自 Object）。

### ★两种类型的存储方式

　　这两种类型的差异，首先体现在存储方式上。

#### ◇引用类型的创建

　　当你在**函数中**创建一个引用类型的对象时，比如下面这句：

```java
StringBuffer str = new StringBuffer();
```

　　该 StringBuffer 【对象】的内容是存储在堆（Heap）上的，需要申请堆内存。而变量 str 只不过是针对该 StringBuffer 对象的一个引用（或者叫地址）。变量 str 的【值】（也就是 StringBuffer 对象的地址）是存储在【栈】上的。

#### ◇基本类型的创建

　　当你在【函数中】创建一个基本类型的变量时，比如下面这句：

```
int n = 123;
```

　　这个变量 n 的【值】也是存储在栈（Stack）上的，但是这个语句不需要再从堆中申请内存了。

　　为了更加形象，便于大伙儿理解，简单画了一个示意图如下：

![不见图 请翻墙](00_Java注意.jpg)

### ★堆和栈的性能差异

　　可能有同学会小声问：堆和栈有啥区别捏？

　　要说堆和栈的差别，那可就大了去了。如果你对这两个概念还是不太明白或者经常混淆，建议先找本操作系统的书拜读一下。

　　由于本系列是介绍性能，所以来讨论一下堆和栈在性能方面的差别（这个差异是很大滴）。堆相对进程来说是全局的，能够被所有线程访问；而栈是线程局部的，只能本线程访问。打个比方，栈就好比个人小金库，堆就好比国库。你从个人小金库拿钱去花，不需要办什么手续，拿了就花，但是钱数有限；而国库里面的钱虽然很多，但是每次申请花钱要打报告、盖图章、办 N 多手续，耗时又费力。

　　同样道理，由于堆是所有线程共有的，从堆里面申请内存要进行相关的加锁操作，因此申请堆内存的复杂度和时间开销比栈要大很多；从栈里面申请内存，虽然又简单又快，但是栈的大小有限，分配不了太多内存。

### ★当初为啥这样设计？

　　可能有同学又问了，干嘛把两种类型分开存储，干嘛不放到一起捏？这个问题问得好！下面我们就来揣测一下，当初 Java 为啥设计成这样。

　　当年 Java 它爹（James Gosling）设计语言的时候，对于这个问题有点进退两难。如果把各种东东都放置到栈中，显然不现实，一来栈是线程私有的（不便于共享），二来栈的大小是有限的，三来栈的结构也间接限制了它的用途。那为啥不把各种东东都放置到堆里面捏？都放堆里面，倒是能绕过上述问题，但是刚才也提到了，申请堆内存要办很多手续，太繁琐。如果仅仅在函数中写一个简单的“int n = 0;”，也要到堆里面去分配内存，那性能就大大滴差了（要知道 Java 是1995年生出来的，那年头俺买了台 PC 配【4兆内存】就属豪华配置了）。

　　左思右想之后，Java 它爹只好做了一个折中：把类型分为“基本类型”和“引用类型”，两者使用不同的创建方式。这种差异从 Java 语法上也可以看出来：引用类型总是用 new 创建对象（提醒一下：某些单键对象/单例对象，表面上没用 new，但是在 getInstance() 内部也还是用 new 创建的）；而基本类型则【不需要】用 new 来创建。

### ★这样设计的弊端

　　顺便跑题一下，斗胆评价 Java 它爹这种设计的弊端（希望 Java Fans 不要跟我急）。我个人认为：这个折中的决策，带来了许多深远的影响，随手举出几个例子：

1、由于基本类型不是派生自 Object，因此不能算是纯种的对象。这导致了 Java 的“【纯】面向对象”招牌打了折扣（当年 Sun 老是吹嘘 Java 是“纯”OO 的语言，其实 Java 的 OO 是不够纯粹滴）。

2、由于基本类型不是派生自 Object，出于某些场合（比如容器类）的考虑，不得不为每个基本类型加上对应的包装类（比如 Integer、Byte 等），使得语言变得有点冗余。

### ★结论

　　从上述的介绍，我们应该明白，使用 new 创建对象的开销是【不小】的。在程序中能避免就应该尽量避免。另外，使用 new 创建对象，不光是创建时开销大，将来垃圾回收时，销毁对象也是有开销的（关于 GC 的开销，咱们会在后面的帖子细谈）。下一个帖子，我们找一个例子来实战一下。

## 2.2 字符串过滤实战

### ★一个简单的需求

　　首先描述一下需求：

给定一个 String 对象，过滤掉除了**数字**字符'0'到'9'以外的其它字符。要求时间开销尽可能小。过滤函数的原型如下：

```java
String filter(String str);
```

　　针对上述需求，俺写了5个不同的过滤函数。为了叙述方便，函数名分别定为 filter1 到 filter5。其中 filter1 性能最差、filter5 性能最好。在看后续的内容之前，你先暗自思考一下，如果由你来实现该函数，大概会写成什么样？最好把你想好的函数写下来，便于跟俺给出的例子作对比。

### ★代码——循序渐进的5种实现方式

#### ◇测试代码

　　为了方便测试性能，先准备好一坨测试代码，具体如下：

```java
class Test
{
    public static void main(String[] args)
    {
        if(args.length != 1)
        {
            return;
        }

        String str = "";
        long nBegin = System.currentTimeMillis();
        for(int i=0; i<1024*1024; i++)
        {
            str = filterN(args[0]);  // 此处调用某个具体的过滤函数
        }
        long nEnd = System.currentTimeMillis();

        System.out.println(nEnd-nBegin);
        System.out.println(str);
    }
};
```

　　在没有想好你的实现方式之前，先别偷看后续内容哦！另外，先注明一下，俺的 Java 环境是 JDK 1.5.0-09，使用的测试字符串是随机生成的，长度32个 char，只含字母和数字。由于 JDK 版本和机器性能不尽相同，你在自己机器上测试的结果可能跟俺下面给出的数值不太一样。

#### ◇版本1

　　先来揭晓性能最差的filter1，代码如下：

```java
private static String filter1(String strOld)
{
    String strNew = new String();
    for(int i=0; i<strOld.length(); i++)
    {
        if('0'<=strOld.charAt(i) && strOld.charAt(i)<='9')
        {
            strNew += strOld.charAt(i);
        }
    }
    return strNew;
}
```

　　如果你的代码不幸和 filter1 雷同，那你的 Java 功底可就是相当糟糕了，连字符串拼接需要用 StringBuffer 来优化都没搞明白。

　　为了和后续对比，先记下 filter1 的处理时间，大约在 8.81-8.90秒 之间。

#### ◇版本2

　　再来看看 filter2，代码如下：

```java
private static String filter2(String strOld)
{
    StringBuffer strNew = new StringBuffer();
    for(int i=0; i<strOld.length(); i++)
    {
        if('0'<=strOld.charAt(i) && strOld.charAt(i)<='9')
        {
            strNew.append(strOld.charAt(i));
        }
    }
    return strNew.toString();
}
```

　　其实刚才在评价 filter1 的时候，已经泄露了 filter2 的天机。filter2 通过使用 StringBuffer 来优化连接字符串的性能。为什么 StringBuffer 连接字符串的性能比 String 好，这个已经是老生常谈，俺在这儿就不细说啦。尚不清楚的同学自己上 Google 一查便知。估计应该有挺多同学会写出类似 filter2 的代码。

　　有些同学可能会问：为啥不用 StringBuilder？

　　确实，在 JDK 1.5 新增加了 StringBuilder 这个类，其性能会比 StringBuffer 更好。不过捏，考虑到有可能要拿到其它版本的 JDK 上作对比测试，而且 StringBuilder 和 StringBuffer 之间的差异【不是】本文讨论的重点，所以后面的例子都使用 StringBuffer 来实现。

　　filter2 的处理时间大约为 2.14-2.18秒，提升了大约4倍。

#### ◇版本3

　　接着看看 filter3，代码如下：

```java
private static String filter3(String strOld)
{
    StringBuffer strNew = new StringBuffer();
    int nLen = strOld.length();
    for(int i=0; i<nLen; i++)
    {
        char ch = strOld.charAt(i);
        if('0'<=ch && ch<='9')
        {
            strNew.append(ch);
        }
    }
    return strNew.toString();
}
```

　　乍一看，filter3 和 filter2 的代码差不多嘛！再仔细瞧一瞧，原来先把 strOld.charAt(i) 赋值给 char 变量，节省了重复调用 charAt() 方法的开销；另外把 strOld.length() 先保存为 nLen，也节省了重复调用 length() 的开销。能想到这一步的同学，估计是比较细心的。

　　经过此一优化，处理时间节省为 1.48-1.52秒，提升了约30%。由于 charAt() 和 length() 的内部实现都挺简单的，所以提升的性能不太明显。

　　另外补充一下，经网友反馈，在 JDK 1.6 上，filter3 和 filter2 的性能基本相同。俺估计：可能是因为 JDK 1.6 在编译时已经进行了相关的优化。

#### ◇版本4

　　然后看看 filter4，代码如下：

```java
private static String filter4(String strOld)
{
    int nLen = strOld.length();
    StringBuffer strNew = new StringBuffer(nLen);
    for(int i=0; i<nLen; i++)
    {
        char ch = strOld.charAt(i);
        if('0'<=ch && ch<='9')
        {
            strNew.append(ch);
        }
    }
    return strNew.toString();
}
```

　　filter4 和 filter3 差别也很小，唯一差别就在于调用了 StringBuffer 带参数的构造函数。通过 StringBuffer 的构造函数设置初始的容量大小，可以有效避免 append() 追加字符时重新分配内存，从而提高性能。

　　filter4 的处理时间大约在 1.33-1.39秒，约提高10%左右。可惜提升的幅度有点小 :-(

#### ◇版本5

　　最后来看看“终极版本”——性能最好的 filter5。

```java
private static String filter5(String strOld)
{
    int nLen = strOld.length();
    char[] chArray = new char[nLen];
    int nPos = 0;
    for(int i=0; i<nLen; i++)
    {
        char ch = strOld.charAt(i);
        if('0'<=ch && ch<='9')
        {
            chArray[nPos] = ch;
            nPos++;
        }
    }
    return new String(chArray, 0, nPos);
}
```

　　猛一看，你可能会想：这个 filter5 和前几个版本的差别也忒大了吧！filter5 既没有用 String 也没有用 StringBuffer，而是拿字符数组进行中间处理。

　　filter5 的处理时间，只用了0.72-0.78秒，相对于 filter4 提升了将近50%。为啥捏？是不是因为直接操作字符数组，节省了 append(char) 的调用？通过查看 append(char) 的源代码，内部的实现很简单，应该不至于提升这么多。

　　那是什么原因捏？

　　首先，虽然 filter5 有一个字符数组的创建开销，但是相对于 filter4 来说，StringBuffer 的构造函数内部也会有字符数组的创建开销。两相抵消。所以 filter5 比 filter4 还多节省了 StringBuffer 对象本身的创建开销。（在俺的 JDK 1.5 环境中，这个因素比较明显）

　　其次，由于 StringBuffer 是线程安全的（它的方法都是 synchronized），因此调用它的方法有一定的同步开销，而字符数组则没有，这又是一个性能提升的地方。（经热心读者反馈，此因素在 JDK 1.6 中比较明显）

　　基于上述两个因素，所以 filter5 比 filter4 又有较大幅度的提升。

###★对于5个版本的总结

　　上述5个版本，filter1 和 filter5 的性能相差约12倍（已经超过一个数量级）。除了 filter3 相对于 filter2 是通过消除函数重复调用来提升性能，其它的几个版本都是通过节省内存分配，降低了时间开销。可见内存分配对于性能的影响有多大啊！如果你是看了

上一个帖子

才写出 filter4 或者 filter5，那说明你已经领会了个中奥妙，俺那个帖子也就没白写了。

###★一点补充说明，关于时间和空间的平衡

　　另外，需要补充说明一下。版本4和版本5使用了空间换时间的手法来提升性能。假如被过滤的字符串【很大】，并且数字字符的比例【很低】，这种方式就不太合算了。

　　举个例子：被处理的字符串中，绝大部分都只含有不到10%的数字字符，只有少数字符串包含较多的数字字符。这时候该怎么办捏？

　　对于 filter4 来说，可以把 new StringBuffer(nLen); 修改为 new StringBuffer(nLen/10); 来节约空间开销。但是 filter5 就没法这么玩了。

　　所以，具体该用“版本4”还是“版本5”，要看具体情况了。只有在你【非常】看重时间开销，且数字字符比例很高（至少大于50%）的情况下，用 filter5 才合算。否则的话，建议用 filter4。



## 2.3 垃圾回收(GC)

### ★JVM 的内存空间

　　在 Java 虚拟机规范中（具体章节请看“这里”），提及了如下几种类型的内存空间：

> ◇栈内存（Stack）：每个线程私有的。
> ◇堆内存（Heap）：所有线程公用的。
> ◇方法区（Method Area）：这里面存放了每个**加载类的反射信息**、**类函数的代码**、**编译时常量**等信息。
> ◇原生方法栈（Native Method Stack）：主要用于 JNI 中的原生代码，平时很少涉及。

　　关于栈内存（Stack）和堆内存（Heap），已经在上次的帖子中扫盲过了，大伙儿应该有点印象。由于今天咱们要讨论的“垃圾回收”话题，主要是和堆内存（Heap）有关。其它的几个玩意儿不是今天讨论的重点。等以后有空了，或许可以单独聊一下。

###★垃圾回收机制简介

　　其实 Java 虚拟机规范中并未规定垃圾回收的相关细节。垃圾回收具体该怎么搞，完全取决于各个 JVM 的设计者。所以，不同的 JVM 之间，GC 的行为可能会有一定的差异。下面咱拿 SUN 官方的 JVM 来**简单介绍**一下 GC 的机制。

####◇啥时候进行垃圾回收？

　　一般情况下，当 JVM 发现堆内存比较紧张、不太够用时，它就会着手进行垃圾回收工作。但是大伙儿要认清这样一个残酷的事实：JVM 进行 GC 的时间点是无法准确预知的。因为 GC 启动的时刻会受到各种运行环境因素的影响，随机性太大。

　　虽说咱们无法准确预知，但如果你想知道每次垃圾回收执行的情况，还是蛮方便的。可以通过 JVM 的命令行参数“-XX:+PrintGC”把相关信息打印出来。

　　另外，调用 System.gc() 只是建议 JVM 进行 GC。至于 JVM 到底会不会真的去做，只有天晓得。所以，通常不建议自己手动调用 System.gc()，还是让 JVM 自行决定比较好。另外，使用 JVM 命令行参数“-XX:+DisableExplicitGC”可以让 System.gc() 不起作用。

#### ◇谁来负责垃圾回收？

　　一般情况下，JVM 会有一个或多个专门的垃圾回收线程，由它们负责清理回收垃圾内存。

#### ◇如何发现垃圾对象？

　　垃圾回收线程会从“根集（Root Set）”开始进行对象引用的遍历。所谓的“根集”，就是正在运行的线程中，可以访问的【引用变量】的集合（比如所有线程当前函数的参数和局部变量、当前类的成员变量等等）。垃圾回收线程先找出被根集直接引用的所有对象（不妨叫集合1），然后再找出被集合1直接引用的所有对象（不妨叫集合2），然后再找出被集合2直接引用的所有对象......如此循环往复，直到把能遍历到的对象都遍历完。

　　凡是从“根集”通过上述遍历可以到达的对象，都称为可达对象或有效对象；反之，则是不可达对象或失效对象（也就是垃圾）。

####◇如何清理/回收垃圾？

　　通过上述阶段，就把垃圾对象都找出来。然后垃圾回收线程会进行相应的清理和回收工作，包括：把垃圾内存重新变为可用内存、进行内存的整理以消除内存碎片、等等。这个过程会涉及到若干算法，有兴趣的同学可以参见[这里](https://en.wikipedia.org/wiki/Garbage_collection_%28computer_science%29#Basic_algorithm)。限于篇幅，咱就不深入聊了。

#### ◇分代

​	早期的 JVM 是不采用分代技术的，所有被 GC 管理的对象都存放在同一个堆里面。这么做的缺点比较明显：每次进行GC都要遍历所有对象，开销很大。其实大部分的对象生命周期都很短（短命对象），只有少数对象比较长寿；在这些短命对象中，又只有少数对象占用的内存空间大；其它大量的短命对象都属于小对象（很符合[二八原理](#4. 无处不在的二八原理)）。

　　有鉴于此，从 JDK 1.2 之后，JVM 开始使用分代的垃圾回收（Generational Garbage Collection）。JVM 把 GC 相关的内存分为“年老代”（Tenured）和“年轻代”（Nursery）、“持久代”（Permanent，对应于 JVM 规范的“方法区”）。【大部分】对象在刚创建时，都位于“年轻代”。如果某对象经历了几轮 GC 还活着（大龄对象），就把它移到“年老代”。另外，如果某个对象在创建时比较大，可能就直接被丢到年老代。经过这种策略，使得年轻代总是保存那些短命的小对象。在空间尺寸上，“年轻代”相对较小，而“年老代”相对较大。

　　因为有了分代技术，JVM 的 GC 也相应分为两种——主要收集（Major Collection）和次要收集（Minor Collection）。“主要收集”同时清理年老代和年轻代，因此开销很大，不常进行；“次要收集”仅仅清理年轻代，开销很小，经常进行。

### ★GC对性能会有啥影响？

　　刚才介绍了GC的大致原理，那GC对性能会造成哪些影响捏？主要有如下几个方面：

#### ◇造成当前运行线程的停顿

　　早期的 GC 比较弱智。在它工作期间，所有其它的线程都被暂停（以免影响垃圾回收工作）。等到 GC 干完活，其它线程再继续运行。所以，早期 JDK 的 GC 一旦开始工作，整个程序就会陷入假死状态，失去各种响应。

　　经过这些年的技术改进（包括采用分代技术），从 JDK 1.4 开始，GC 已经比较精明了。在它干活期间，只是偶尔暂停一下其它线程的运行（从长时间假死变为暂时性休克）。

#### ◇遍历对象引用的开销

　　试想如果JVM中的对象很多，那遍历完所有可达对象肯定是比较费劲的工作，这个开销可不小。

#### ◇清理和回收垃圾的开销

　　遍历完对象引用之后，对垃圾的清理和回收也有较大的开销。这部分开销可能包括复制内存块、更新对象引用等等。

### ★几种收集器

#### ◇两个性能指标

　　因为今天聊的是性能的话题，必然会提到衡量 GC 性能的两个重要指标：**吞吐量**（Throughput）和**停顿时间**（Pause Time）。**吞吐量这个词不是很直观，解释一下：就是 JVM【不用于】GC 的时间占总时间的比率**。“吞吐量”是越大越好，“停顿时间”是越小越好。

　　不同的应用程序对这两个指标的关注点不一样（后面具体会说），也就是所谓的“众口难调”。很多 JVM 厂商为了迎合“众口”，不得不提供多种几种垃圾收集器供使用者选择。不同的收集器，采用的收集策略是不一样的，下面具体介绍。

#### ◇串行收集器（Serial Collector）

　　使用命令行选项“-XX:+UseSerialGC”指定。

　　这种收集器是最传统的收集器。它使用**单线程**进行垃圾回收，对于“单 CPU 机器”比较合适。另外，小型应用或者对上述两个指标没有特殊要求的，可以使用串行收集器。

#### ◇并行收集器（Parallel Throughput Collector）

　　顾名思义，这种收集器使用**多线程**进行垃圾回收以达到高吞吐量。垃圾回收线程的数量通过命令行选项“-XX:ParallelGCThreads=n”指定。可以设置该数值以便充分利用“多CPU 或 多核”。

　　当使用命令行选项“-XX:+UseParallelGC”时：它会针对年轻代使用多个垃圾回收线程，对年老代依然使用单个线程的串行方式。此选项最早在JDK 1.5引入。

　　当使用命令行选项“-XX:+UseParallelOldGC”时：它针对年轻代和年老代都使用多个垃圾回收线程的方式。不过此选项从 JDK 1.6 才开始引入。

#### ◇并发收集器（Concurrent Low Pause Collector）

　　使用命令行选项“-XX:+UseConcMarkSweepGC”指定。

　　这种收集器优先保证程序的响应。它会尽量让垃圾回收线程和应用自身的**线程同时运行**，从而**降低停顿时间**。此选项从JDK 1.4.1开始支持。

#### ◇增量收集器（Incremental Collector）

　　自从 JDK 1.4.2 以来，SUN 官方就停止维护该收集器了。所以俺就节省点口水，不多说了。

### ★如何降低GC的影响？

####◇尽量减少堆内存的使用

　　由于 GC 是针对存储在堆内存的对象进行的。咱们如果在程序中减少引用对象的分配（也就相应降低堆内存分配），那对于提高 GC 的性能是很有帮助滴。上次“字符串过滤实战”的帖子给出了一个例子，示范了如何通过降低堆内存的分配次数来提升性能。

#### ◇设置合适的堆内存大小

　　JVM 的堆内存是有讲究的，不能太大也不能太小。如果堆内存太小，JVM 老是感觉内存不够用，可能会导致频繁进行垃圾回收，影响了性能；如果堆内存太大，以至于操作系统的大部分物理内存都被 JVM 自个儿霸占了，那可能会影响其它应用程序甚至操作系统本身的性能。

　　另外，年轻代的大小（或者说“年轻代”与“年老代”的比值）对于 GC 的性能也有明显影响。如果年轻代太小，可能导致次要收集很频繁；如果年轻代太大，导致次要收集的停顿很明显。

　　JVM 提供了若干和堆内存大小相关的命令行选项，具体如下：

－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－

-Xms　　设置初始堆内存

-Xmx　　设置最大堆内存

-Xmn　　设置年轻代的大小

-XX:NewRatio=n　　设置年轻代与年老代的比例为“n”

-XX:NewSize=n　　设置年轻代大小为“n”

－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－

　　一般情况下，**JVM 的默认参数值已经够用。所以没事儿别轻易动用上述选项**。如果你非调整不可，一定要做深入的性能对比测试，保证调整后的性能确实优于默认参数值。

#### ◇吞吐量和停顿的取舍

　　前面提到了不同应用的众口难调。常见的口味有两种：(1)看重吞吐量，对停顿时间无所谓；(2)侧重于停顿时间。

　　对于某些在后台的、单纯运算密集型的应用，属于第一种。比如某些科学计算的应用。这时候建议使用并行收集器。

　　对于涉及用户 UI 交互的、实时性要求比较高、程序需要快速响应的，属于第二种。比如某些桌面游戏、某些电信交换系统。这时候建议使用并发收集器。

### ★相关的参考资料

####◇GC调优资料

　　SUN 官方提供了若干关于 JVM 垃圾回收调优的说明文档：

​	JDK 1.6请看[这里](http://www.oracle.com/technetwork/java/javase/gc-tuning-6-140523.html)。

#### ◇虚拟机规范

　　[这里](https://docs.oracle.com/javase/specs/)是 SUN 官方提供的 JVM 规范。

## 2.4 关于finalize函数

​	上次的帖子聊了垃圾回收器的调优，当时啰嗦了比较长的篇幅，就没再继续提 finalize 的事儿（其实这玩意儿和 GC 是沾点儿边的）。今天咱就把 finalize 函数相关的性能话题拿来说一下。

### ★finalize 函数的调用机制

　　俺经常啰嗦：“了解本质机制的重要性”。所以今天也得先谈谈 finalize 函数的调用机制。在聊之前，先声明一下：Java虚拟机规范（见[这里](https://docs.oracle.com/javase/specs/)），并没有硬性规定垃圾回收该不该搞，以及该如何搞。所以俺这里提到的 finalize 函数的调用机制，或许适用于大多数 JVM，但【不保证】适用于所有的 JVM。

####◇何时被调用？

　　finalize 啥时候才会被调用捏？一般来说，要等到JVM开始进行垃圾回收的时候，它才【**有可能**】被调用。而 JVM 进行垃圾回收的时间点是【非常】不确定的，依赖于各种运行时的环境因素。具体细节可以参见“本系列前一帖”。正是由于 finalize 函数调用时间点的不确定，导致了后面提到的某些缺点。

####◇谁来调用？

　　说完何时调用，咱接着来聊一下被谁调用？

　　常见的 JVM 会通过 GC 的垃圾回收线程来进行 finalize 函数的调用。由于垃圾回收线程比较重要（人家好歹也是 JVM 的一个组成部分嘛），为了防止 finalize 函数抛出的异常影响到垃圾回收线程的运作，垃圾回收线程会在调用每一个 finalize 函数时进行 try/catch，如果捕获到异常，就直接丢弃，然后接着处理下一个失效对象的 finalize 函数。

###★对 finalize 函数的误解和误用

####◇依靠 finalize 来释放资源

　　很多同学寄希望于通过 finalize() 来完成类对象中某些资源的释放（比如关闭数据库连接之类）。

　　有这种企图的同学，请注意看本文后面的“finalize 函数的缺点”！

###★使用 finalize 函数的注意事项

　　下面介绍的注意事项，有些可能和性能优化关系不大，俺也一并列出来。

####◇调用时间不确定——有资源浪费的风险

　　前面已经介绍了调用机制。同学们应该认清【**finalize 的调用时机是很不确定的**】这样一个事实。所以，假如你把某些稀缺资源放到 finalize() 中释放，可能会导致该稀缺资源等上很久很久很久以后才被释放。这可是资源的浪费啊！

　　另外，某些类对象所携带的资源（比如某些 JDBC 的类）可能本身就很耗费内存，这些资源的延迟释放会造成很大的性能问题。

#### ◇可能不被调用——有资源泄漏的风险

　　很多同学误以为 finalize() 总是会被调用，【其实不然】。在某些情况下，finalize() 压根儿不被调用。比如在 JVM 退出的当口，内存中那些对象的 finalize 函数可能就不会被调用了。

　　俺估摸着：还有同学在打 “runFinalizersOnExit” 的主意，来确保所有的 finalize 在 JVM 退出前被调用。但是，很可惜也很遗憾，该方法从 JDK 1.2 开始，就已经被废弃了。即使该方法不被废弃，也是有很大的线程安全隐患滴！企图打这个主意的同学，趁早死了这条心吧！

　　从上述可以看出，一旦你依赖 finalize() 来帮你释放资源，那可是很不妙啊（【有资源泄漏的危险】）！关于资源泄漏的严重性，俺在[这里](##1.3 缺少良好的编程习惯)曾经提到过。很多时候，资源泄露导致的性能问题更加严重，万万不可小看。

####◇对象可能在 finalize 函数调用时复活——有诈尸的风险

　　诈尸的情况比较少见，不过俺还是稍微提一下。

　　本来，只有当某个对象已经失效（没有引用），垃圾回收器才会调用该对象的 finalize 函数。但是，万一碰上某个变态的程序员，在 finalize() 函数内部再把对象自身的引用（也就是 this）重新保存在某处，也就相当于把自己复活了（因为这个对象重新有了引用，不再处于失效状态）。这种做法是不是够变态啊 :-)

　　为了防止发生这种诡异的事情，垃圾回收器只能在每次调用完 finalize() 之后再次去检查该对象是否还处于失效状态。这无形中又增加了 JVM 的开销。

　　随便提一下。由于 JDK 的文档中规定了（具体参见[这里](https://docs.oracle.com/javase/1.5.0/docs/api/java/lang/Object.html#finalize%28%29)），JVM 对于每一个类对象实例最多只会调用一次 finalize()。所以，对于那些诈尸的实例，当它们真正死亡时，finalize() 反而不会被调用了。这看起来是不是很奇怪？

####◇要记得自己做异常捕获

　　刚才在介绍 finalize() 调用机制时提到，一旦有异常抛出到 finalize 函数外面，会被垃圾回收线程捕获并丢弃。也就是说，异常被忽略掉了（异常被忽略的危害，[这里](## 1.4 异常处理使用不当)有提到）。为了防止这种事儿，凡是 finalize() 中有可能抛出异常的代码，你都得写上 try catch 语句，自己进行捕获。

####◇要小心线程安全

　　由于调用 finalize() 的是垃圾回收线程，和你自己代码的线程不是同一个线程；甚至不同对象的 finalize() 可能会被不同的垃圾回收线程调用（比如使用“并行收集器”的时候）。所以，当你在 finalize() 里面访问某些数据的时候，还得时刻留心线程安全的问题。

###★结论

　　前面废了这么多话，最后稍微总结一下。俺窃以为：finalize 实在是 Java 的**鸡肋**。或许它对于【极少数】程序员有用，但对于大多数人（包括俺自个儿），这玩意儿压根儿没啥好处。大伙儿还是尽量**不用为妙**。

# 3. Java新手进阶:细说引用类型

　　在前几天的帖子《Java性能优化[1]：基本类型 vs 引用类型》里，俺大概介绍了“引用类型”与“基本类型”在存储上的区别。昨天有网友在评论中批评说“引用类型变量和它所引用的对象”没区分清楚，容易混淆。所以今天专门来说一下引用类型的相关细节。

　　另外，顺便也把[原先的帖子](##2.1 基本类型 vs 引用类型)中，关于“两种类型的存储方式”这节补充了一下，加点插图，有助于大伙儿的理解。

　　其实，引用类型的变量非常类似于 C/C++ 的指针。为了形象起见，也为了打字方便，本文后面的内容，都把“引用类型的变量”称为【**指针**】。所以，如果你原先有 C/C++ 背景，今天讲的内容对你来说应该很好理解；否则的话，可能要多琢磨琢磨了。

### ★创建问题

　　假设咱们在【函数中】写了如下这个简单的语句：

```java
StringBuffer str = new StringBuffer("Hello world");
```

　　别看这个语句简单，其实包含了如下三个步骤：

　　首先，new 操作符会在【堆】（Heap）里申请了一坨内存，把创建好的 StringBuffer 对象放进去。

　　其次，StringBuffer str 声明了一个指针。这个指针本身是存储在【栈】（Stack）上的（因为前面俺说了：此语句写在【函数中】），用来指向某个 StringBuffer 类型的对象。或者换一种说法，这个指针可以用来保存某个 StringBuffer 对象的地址。

　　最后，当中这个 等于号（赋值符号）把两者关联起来，也就是把刚申请的那一坨内存的地址保存成 str 的值。

　　为了加深列位看官的印象，把图片再拿出来秀一下：

![不见图 请翻墙](00_Java注意.jpg)

###★引用对象之间的赋值、判相等

　　通过上述的图解，大伙儿应该明白指针变量和该指针变量指向的【对象】是一个什么关系了吧？

　　还是接着刚才的例子，再来看赋值的问题。对于如下语句：

```java
StringBuffer str2 = str;
```

　　这个赋值语句是啥意思捏？实际上就是把 str 的地址复制给 str2；记住，是地址的复制，StringBuffer 对象本身并【没有】复制。所以两个指针指向的是同一个东东。

　　再搞一张示意图，如下（今天画这些图可把俺累坏了）：

![不见图 请翻墙](00_Java注意02.jpg)

　　明白了赋值，判断相等的问题（就是==操作符）也就简单了。当我们写如下语句时，只是判断两个指针的【值】（也就是对象的地址）是否相等，并【不是】判断“被指向的对象”是否内容相同。

```java
if(str2 == str)
```

　　实际上两个指针的值相同，则肯定是指向同一个对象（所以对象内容必定相同）。但是两个内容相同的对象，它们的地址可能不一样（比如克隆出来的多个对象之间，地址就不同）。

### ★final 常量的问题

　　针对引用类型变量的 final 修饰符也是很多人搞混淆的地方。实际上 final 只是修饰指针的值（也就是限定指针保存的地址不能变）。至于该指针指向的对象，内容是否能变，那就管不着了。所以，对于如下语句：

```java
final StringBuffer strConst = new StringBuffer();
```

　　你可以修改它指向的对象的【内容】，比如：

```java
strConst.append("hello world");
```

　　但是【不能】修改它的【值】，比如：

```java
strConst = null;
```

###★传参的问题

　　引用类型（在函数调用中）的传参问题，是一个相当扯的问题。有些书上说是传值，有些书上说是传引用。搞得 Java 程序员都快成神经分裂了。所以，我们最后来谈一下“引用类型参数传递”的问题。

　　还是拿刚才的例子，假设现在要把刚才创建的那一坨字符串打印出来，我们会使用如下语句：

```java
System.out.println(str);
```

　　这个语句又是什么意思捏？这时候就两说了。

　　第一种理解：

　　可以认为传进函数的是 str 这个指针，指针说白了就是一个地址的值，说得再白一点，就是个整数。按照这种理解，就是传值的方式。也就是说，参数传递的是指针本身，所以是传值的。

　　第二种理解：

　　可以认为传进去的是 StringBuffer 对象，按照这种理解，就是传引用方式了。因为我们确实是把对象的地址（也就是引用）给传了进去。

　　费了这么多口水，其实不论是“传引用”还是“传值”，都可以讲得通，关键取决于你是【如何看待】参数所传递的【东西】。这就好比量子力学中“光的波粒二象性”，如果你以粒子的方式去测量它，它看起来像粒子；如果你以波动的方式去观测它，它看起来像波动。假如你不太懂量子力学，前面这话就当我没说 :-)



# 4. 无处不在的二八原理

## 4.1 什么是二八原理

　　估计看俺博客的人里面，应该有很多人听说过二八原理（如果你之前从来没听说过，那你的知识面有太窄的嫌疑）。但是知道二八原理的人有很多却不会（或者不善于）运用。直接的后果就是你在各种事情上付出了很多时间与精力，但是回报却很少。鉴于该原理非常非常的实用，俺打算专门写一个系列来聊聊和它相关的话题。

###★什么是二八原理

　　按照惯例，先说说什么是二八原理（如果你已经知道二八原理，可以跳过这部分）。二八原理（也称为“80/20原理”或“帕雷托原理”）最早是由意大利的经济学家帕雷托提出来的，关于这个原理的详细介绍请看[这里](https://en.wikipedia.org/wiki/Pareto_principle)。通俗地说，就是【因果的不对称】。20% 的原因导致了 80% 的结果；而其它 80% 的原因只产生 20% 的结果。当然，这里提到的“二八开”的比例不是绝对的，从“三七开”到“一九开”都有可能。

　　为了给不了解它的人一点感性认识，俺举几个例子：

> 少数有钱人掌握了社会的大部分财富（帕雷托就是从财富分布中发现该原理的）
> 公司里大部分的销售业绩由小部分的销售人员创造
> 地毯的大部分磨损集中在少数几个地方
> 大部分开发人员只使用少数的编程语言，而大量的编程语言几乎没人用
> 软件的大部分 bug 是由少数开发人员产生的

### ★如何运用

　　首先，这个原理的关键处就在于它挑战了传统的理念。比如：小时候老师经常教导说：“一分付出一分回报”。用数学的术语讲，就是付出和回报是呈线性相关的。很多人受此影响（可能是潜意识的影响），都习惯于平均分配时间、精力来处理问题。结果就导致我前面所说的投入很多，回报很少。因此，要运用二八原理之前，你先得改变自己头脑中的思维定势，要以【**不平等**】的观点来看周围的问题。（关于改变思维定势，关键还是靠自己，俺帮不上太大忙）

　　其次，你得学会如何区分重要与次要。这个就属于方法论的问题。俺之前写过几个帖子都和这方面有关，比如：《[如何选择 IT 技术书籍](https://program-think.blogspot.com/2009/01/choose-it-book.html)》和《[做正确的事](https://program-think.blogspot.com/2009/01/4.html)》，以后还会继续写相关的帖子，希望对大伙儿能有帮助。

## 4.2 在软件开发中的应用

　　上次聊了《什么是二八原理》，接下来得说说如何运用了。由于本博客主要谈 IT 技术，显然要先来说说和程序员有关的那些事。为了不至于太抽象，咱们以“开发文本编辑器”为例（这玩意大伙儿都熟悉，省得费口水解释），来说说不同职责的开发人员在开发过程中该如何具体运用二八原理。

###★需求分析

　　需求分析在整个开发过程中占的工作量不大，但是产生的影响巨大（这又是一个二八原理的例子）。既然需求分析如此重要，照理说应该安排最强的人来搞。但实际情况往往不是如此：很多公司负责需求分析的人并不胜任这项工作。俺经历过几个不太成功的项目，其问题的根源都和需求分析有关。

　　需求分析最要紧的是：搞清楚用户【到底】想要什么？如果这个问题搞错了、搞偏了，后面的步骤做得再好也是白搭（比如客户想要一个文本编辑器，结果你搞了个图形编辑器给他）。这方面其实有很多的道道，限于篇幅就不展开了，大伙儿如果有兴趣，以后可以单独说一下。

　　在搞清楚“用户想要什么”之后，接着要整理出功能列表（洋文叫“Feature List”），并筛选出大约20%的重点功能。这个步骤是俺今天想重点聊的，因为这个步骤和后续的各项开发密切相关。一般来说，功能筛选的依据有如下几个：

1、用户经常用的功能（比如：save、copy、cut、paste）

2、宣传的卖点（要能够超出同类软件，吸引眼球）

3、和用户利益密切相关的功能（这种功能不允许出错，比如存盘功能）

　　这个筛选的过程要尽早完成，而且最好是产品人员、开发人员、测试人员三方的头头一起讨论，以保证立场客观、观点全面。筛选出重要功能点后，其他人员的工作安排要"以重点功能为纲"，有所侧重。

###★项目管理

　　如果你是个项目经理，在排项目计划时，就得尽量优先安排【重点功能】的开发/测试，而且要安排能力强的人员来完成。照俺以前的做法，重点功能排计划至少得留出1/3的时间余量，以防万一（事实证明，几乎每个稍大点的项目都会出现【万一】）。至于【非】重点功能，尽量排到后面，安排能力一般的人开发/测试。

　　然后，在项目进行过程中，肯定要有定期的例会。作为项目经理，你应该主要关注重点功能的进度情况和风险情况。

　　一旦项目有延期的风险，就从非重点功能开始裁减（俗称砍功能）。由于是裁减**非**重点功能，不至于产生致命的影响。

###★设计界面

　　设计界面时，你得保证【所有的】常用功能都放在显著的位置（比如工具条）；还得保证它们用起来方便（比如提供快捷键和右键菜单支持）。

　　对于卖点，它不一定是常用功能，它的目的是激起用户的购买欲望和使用欲望。因此你要把它们设计得比较酷，有噱头。

　　对于利益相关的功能，大部分情况下都是侧重于业务逻辑实现。如果它既不是常用功能、也不是卖点，那么界面设计方面倒不一定要额外花大力气。

　　其它的【非】重点功能，只要按照常规方法设计，不用花太大精力。

###★编写代码

　　俺发现很多开发人员有几个通病：先做有趣或容易的功能，然后再做无聊或者繁琐的功能；对自己有兴趣的功能投入精力多，对自己没兴趣的简单应付。

　　以上这些都是开发的大忌。作为一个职业的开发人员，不应该以自己的兴趣和喜好来决定开发的轻重缓急。正确做法应该如下：

　　你首先得用主要精力完成上述所说的重点功能，而且要保证它们的代码质量尽可能好，尽可能方便维护（重点功能往往是经常有需求变更，经常被修改的）。

　　对于重点功能中的【常用功能】，要保证“时间性能够好”（通俗地说，就是快速响应）。对于【用户利益相关的功能】，要保证 bug 尽可能少（尤其是安全性、稳定性、健壮性的 bug）。

　　至于其它的【非】重点功能，只要不出明显 bug，有点小缺陷无伤大雅。

###★测试

　　如果你是个测试人员，你同样要把主要精力用于测试那些重点功能。对于"用户利益相关的功能"，多进行一些健壮性测试、稳定性、安全性等测试（比如测试保存大文件是否会出错）。对于常用功能，主要进行易用性和性能测试（比如拷贝、粘贴是否易用）。

　　至于其它功能，只要进行普通的测试，保证它不出现明显和严重 bug 即可。要知道当年微软发布 Windows 2000 的时候，尚遗留上千个未修复的 bug（当然都是低优先级的），人家不也照样发布嘛。

###★产品演示

　　有些软件开发完之后，会搞一些 Demo 进行宣传。如果你是负责进行 Demo 的人，一定要把主要的 Demo 时间用来秀软件的卖点，这样给客户的印象最深刻，效果最好；至于【非】卖点的功能，甚至都不用提及。

　　几种和开发相关工作就介绍到这里，最后送给大伙一句话：【**Work Smart, Not Hard!**】

##4.3 在管理方面的应用

[链接](https://program-think.blogspot.com/2009/03/80-20-principle-2-management-overview.html)

