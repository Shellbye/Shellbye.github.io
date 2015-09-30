---
author: shellbye
comments: true
date: 2015-09-05 11:47:08+00:00
layout: post
slug: java_note_web
title: Java note web
categories:
- tech_world
tags:
- java
- job
---


### java web
1. JSP 相对 Servlet 的优势：  
1.JSP 是 servlet 的扩展  
2.容易维护  
3.部署快速:不需要重新编译和部署  
4.代码量较少  

1. servlet 内部交互的方式 ?
1.RequestDispatchers include() 和 forward() 方法.  
2.使用 Response 的 sendRedirect()  
3.使用 servlet Context methods.  

1. Servlet 的 Constructor vs Init 方法的异同  
Servlet 的实现类都会有构造方法，但是它们最好都使用`init()`方法去初始化Servlet，
这有以下两个原因：  
1)java中不允许在接口中定义构造函数，这意味着无法通过构造函数来约束任何实现Servlet的类  
2)Servlet需要ServletConfig对象作为初始化参数，该参数由web容器创建，
并且其持有对ServletContext的引用。ServletContext也是由web容器创建的。
想Tomcat一样的web容器是通过java的反射技术来创建Servlet的实例的，因此需要无参的构造函数。
即使有带参数的构造函数，web容器也无法调用。  

1.  Servlet常用接口  
这个接口是所有Servlet必须直接或间接实现的接口。它定义了以下方法：  
`init(ServletConfig config)`  用于初始化Servlet  
`destory()`  销毁Servlet  
`getServletInfo()`  获得Servlet的信息  
`getServletConfig()` 获得Servlet配置相关信息  
`service(ServletRequest req,ServletResponse res)`  运行应用程序逻辑的入口点。  
1）`public abstract class GenericServlet implements Servlet,ServletConfig,java.io.Serializable`  
`GenericServlet`提供了对Servlet接口的基本实现。它是一个抽象类，它的service()方法是一个抽象的方法，
GenericServlet的派生类必须直接或间接实现这个方法。  
2）`public abstract class HttpServlet extends GenericServlet implements java.io.Serializable`  
`HttpServlet`类是针对使用HTTP协议的WEB服务器的Servlet类。HttpServlet类通过执行Servlet接口，能够提供HTTP协议的功能。  
`HttpServlet`的子类必须实现以下方法中的一个。  
`doGet` 如果Servlet支持HTTP GET请求，用于HTTP GET请求。  
`doPost` 如果Servlet支持HTTP POST请求，用于HTTP POST请求。  
`doPut` 如果Servlet支持HTTP PUT请求，用于 HTTP PUT请求。  
`doDelete` 如果Servlet支持HTTP DELETE请求，用于HTTP DELETE请求。  
`init`和`destory` 管理Servlet占用的资源。如果需要管理Servlet生命周期内所持有资源，可以重载这二个方法  
`getServletInfo`  获得Servlet自身的信息。  

1. [HttpServlet详解](http://blog.csdn.net/zdwzzu2006/article/details/5147018){:target="_blank"}  
HttpServlet容器响应Web客户请求流程如下：  
1）Web客户向Servlet容器发出Http请求；  
2）Servlet容器解析Web客户的Http请求；  
3）Servlet容器创建一个HttpRequest对象，在这个对象中封装Http请求信息；  
4）Servlet容器创建一个HttpResponse对象；  
5）Servlet容器调用HttpServlet的service方法，把HttpRequest和HttpResponse对象作为service方法的参数传给HttpServlet对象；  
6）HttpServlet调用HttpRequest的有关方法，获取HTTP请求信息；  
7）HttpServlet调用HttpResponse的有关方法，生成响应数据；  
8）Servlet容器把HttpServlet的响应结果传给Web客户。  

1. 创建HttpServlet的步骤——“四部曲”  
1）扩展HttpServlet抽象类；  
2）覆盖HttpServlet的部分方法，如覆盖doGet()或doPost()方法；  
3）获取HTTP请求信息。通过HttpServletRequest对象来检索HTML表单所提交的数据或URL上的查询字符串；  
4）生成HTTP响应结果。通过HttpServletResponse对象生成响应结果，它有一个getWriter()方法，该方法返回一个PrintWriter对象。 

1. 实现会话跟踪的技术有哪些？  
由于HTTP协议本身是无状态的，服务器为了区分不同的用户，就需要对用户会话进行跟踪，简单的说就是为用户进行登记，为用户分配唯一的ID，下一次用户在请求中包含此ID，服务器据此判断到底是哪一个用户。  
①URL 重写：在URL中添加用户会话的信息作为请求的参数，或者将唯一的会话ID添加到URL结尾以标识一个会话。  
②设置表单隐藏域：将和会话跟踪相关的字段添加到隐式表单域中，这些信息不会在浏览器中显示但是提交表单时会提交给服务器。  
这两种方式很难处理跨越多个页面的信息传递，因为如果每次都要修改URL或在页面中添加隐式表单域来存储用户会话相关信息，事情将变得非常麻烦。  
③cookie：cookie有两种，一种是基于窗口的，浏览器窗口关闭后，cookie就没有了；另一种是将信息存储在一个临时文件中，并设置存在的时间。当用户通过浏览器和服务器建立一次会话后，会话ID就会随响应信息返回存储在基于窗口的cookie中，那就意味着只要浏览器没有关闭，会话没有超时，下一次请求时这个会话ID又会提交给服务器让服务器识别用户身份。会话中可以为用户保存信息。会话对象是在服务器内存中的，而基于窗口的cookie是在客户端内存中的。如果浏览器禁用了cookie，那么就需要通过下面两种方式进行会话跟踪。当然，在使用cookie时要注意几点：首先不要在cookie中存放敏感信息；其次cookie存储的数据量有限（4k），不能将过多的内容存储cookie中；再者浏览器通常只允许一个站点最多存放20个cookie。当然，和用户会话相关的其他信息（除了会话ID）也可以存在cookie方便进行会话跟踪。  
④HttpSession：在所有会话跟踪技术中，HttpSession对象是最强大也是功能最多的。当一个用户第一次访问某个网站时会自动创建HttpSession，每个用户可以访问他自己的HttpSession。可以通过HttpServletRequest对象的getSession方法获得HttpSession，通过HttpSession的setAttribute方法可以将一个值放在HttpSession中，通过调用HttpSession对象的getAttribute方法，同时传入属性名就可以获取保存在HttpSession中的对象。与上面三种方式不同的是，HttpSession放在服务器的内存中，因此不要将过大的对象放在里面，即使目前的Servlet容器可以在内存将满时将HttpSession中的对象移到其他存储设备中，但是这样势必影响性能。添加到HttpSession中的值可以是任意Java对象，这个对象最好实现了Serializable接口，这样Servlet容器在必要的时候可以将其序列化到文件中，否则在序列化时就会出现异常。  

1. JSP有哪些内置对象？作用分别是什么？  
JSP有9个内置对象：  
request：封装客户端的请求，其中包含来自GET或POST请求的参数；  
response：封装服务器对客户端的响应；  
pageContext：通过该对象可以获取其他对象；  
session：封装用户会话的对象；  
application：封装服务器运行环境的对象；  
out：输出服务器响应的输出流对象；  
config：Web应用的配置对象；  
page：JSP页面本身（相当于Java程序中的this）；  
exception：封装页面抛出异常的对象。  

1. 如何实现JSP或Servlet的单线程模式？  
`<%@page isThreadSafe=”false”%>`  
Servlet默认的工作模式是单实例多线程，如果Servlet实现了标识接口SingleThreadModel又或是JSP页面通过page指令设置isThreadSafe属性为false，那么它们生成的Java代码会以单线程多实例方式工作。显然，这样做会导致每个请求创建一个Servlet实例，这种实践将导致严重的性能问题。  

1. Web服务器一般要解决如下几个问题：  
1）部署在同一个服务器上的两个Web应用程序所使用的java类库可以实现相互隔离  
2）部署在同一个服务器上的两个Web应用程序所使用的java类库可以互相共享  
3）服务器需要尽可能的保证自身的安全不受部署的web应用程序影响  
4）支持jsp应用的web服务器，大多需要hotswap功能  

1. Tomcat（5.x）目录结构：  
1）/common/*:类库可被Tomcat和所有的web应用共同使用  
2）/server/*:类库可被Tomcat使用，对所有的web应用程序都不可见  
3）/shared/*:类库可被所有web应用程序使用，对tomcat不可见  
4）/WebApp/WEB-INF/：类库仅仅可以被此web应用使用，对tomcat和其他web应用不可见  