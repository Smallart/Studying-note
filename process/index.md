# 请求流程

[-[HTTP URL]-]是前后端沟通的桥梁，其中包含了**报文头**以及**报文体**。报文头如下标识了请求地址，而报文体则带有请求的一些数据：

> http:// {ip} : {port} / {context-path} / {service-name} / {method-name}

* ip：[-[IP]-]地址信息，用于特定网络中不同计算机的寻址
* port：端口信息，用于同一台计算机上不同Web容器的寻址，一台计算机可以允许多个Web容器实例。
* context-path：上下文路径，用于同个Web容器内不同应用的寻址，一个Web容器实例可以部署多款应用。
* service-name：服务名称，用于同个应用内部不同服务的寻址，一个应用内部通常会有多个服务。
* method-name：方法名称，用于同个服务内部不同方法的序值，一个服务通常都会包含多个方法。

根据[-[HTTP]-]报文头，相关请求会基于[-[IP]-]地址找到特定的计算机，而计算机会根据端口信息找到具体的[-[Web]-]容器。容器会根据上下文路径找到具体的应用。之后应用会根据服务名称和方法名找到真正可以处理该请求的服务方法，最终方法处理完成返回相对应的应答。

![HTTP请求流程](/img/http-request.png)

在[-[Java Web]-]应用中，我们最常用的容器是[-[Tomcat]-]，其中最重要的结构如下：

````xml
<Server>     # 最顶层表示整个容器，其中可以包含多个Service组件                  
    <Service> # 将Connector和Engine外包一层，把它们组装在一起对外提供服务，可以包含多个Connector，但是只能包含一个Engine。
        <Connector /> #不同的Connector负责接收不同端口上相应协议的请求
        <Connector />
        <Engine> #处理请求
            <Host># 表示一个虚拟主机
                <Context /> #在隶属Host上运行的一个Web应用
            </Host>
        </Engine>        
    </Service>
</Server>
````

![Tomcat构造](/img/Tomcat-Request.png)

* 客户端发送请求至主机的端口，被该端口上对应协议的[-[Connector]-]接收
* Connector将该请求交给它所在的[-[Service]-]的[-[Engine]-]来负责处理，并等待[-[Engine]-]的回应
* [-[Engine]-]获得请求之后从报文中提取主机名称，在所有虚拟机[-[Host]-]中寻找匹配
* 在未匹配到同名虚拟主机的情况下，Engine将该请求交给名为[-[localhost]-]的虚拟主机[-[host]-]处理
* [-[Host]-]获得请求之后将根据[-[URL]-]中的[-[Context-path]-]去配置它所拥有的所有[-[Context]-]。
* [-[Context]-]构建[-[HttpServletRequest、HttpServletResponse]-]对象，将其作为参数调用应用[-[Spring-demo]-]，由应用完成业务逻辑执行、结果数据存储等过程，等待应答数据。
* [-[Context]-]接收到应用返回的[-[HttpServletResponse]-]对象之后将其返回给客户端

## [-[HTTP]-]请求在[-[Web]-]应用中的处理流程

![应用请求](/img/Context-Request.png)

[-[Web]-]应用处理[-[HTTP]-]请求的流程主要是穿越监听器[-[Listener]-]和过滤器链[-[Filters]-]，最终抵达[-[Servlet]-]的过程。


## [-[HTTP]-]请求在[-[Spring]-]框架中处理过程

![SpringMVC请求](/img/Spring-Request.png)

* 前置分发器[-[DispatcherServlet]-]接收到[-[HTTP]-]请求之后，将查找适当的控制器[-[Controller]-]来处理请求，它通过解析[-[HTTP]-]请求的[-[URL]-]从处理器映射器[-[HandlerMapping]-]中获取该请求对应的[-[Handler]-]处理器和处理器拦截器，最后以[-[HandlerExceutionChain]-]形式返回。
* 前置分发器[-[DispatcherServlet]-]根据获得的处理器[-[Handler]-]选择合适的适配器[-[HandlerAdapter]-]，在调用[-[handler]-]之前其拦截器的方法[-[preHandler]-]优先执行。
* 方法[-[preHandler]-]提取[-[HTTP]-]请求中的数据填充到处理器[-[Handler]-]的入参中，然后开始调用处理器[-[Handler]-]相关方法。
* 控制器[-[Controller]-]执行完成之后，向前置分发器[-[DispatcherServlet]-]返回一个模型与视图名对象[-[ModelAndView]-]。
* 前置分发器[-[DispatchServlet]-]根据模型与视图名对象[-[ModelAndView]-]选择适当的视图解析器[-[ViewResolver]-]。
* 视图解析器[-[ViewResolver]-]将根据[-[ModelAndView]-]里面指定的视图名称获得特定视图[-[View]-]。
* 前置分发器[-[DispatchServlet]-]将模型数据填充进视图当中，然后将渲染结果返回给客户端。

在填充处理器[-[Handler]-]入参的过程中，[-[Spring]-]还会根据配置做如下预处理：
* [-[HttpMessageConver]-]：将请求消息转换为[-[java]-]对象
* 数据转换
* 数据格式化
* 数据校验

## 请求


### 校验

[Spring Validation最佳实践及其实现原理](https://segmentfault.com/a/1190000023471742)


### Filter

[spring boot 定义filter的几种方式](https://blog.csdn.net/ykzimu/article/details/101345013)

### WebMVCConfig 和 EnableWebMVC

[自定义SpringBoot默认MVC配置](https://zhuanlan.zhihu.com/p/344541013)

### 全局异常处理

[全局异常处理](https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc)

### MessageSource

[MessageSource i18n国际化](https://www.jianshu.com/p/a354d3f849ec)


### 一次请求MVC做了什么

[一次请求 SpringMVC 到底做了什么](https://juejin.cn/post/6844904088853807112)


### Spring获得request的几种方法

[Spring获取Request的几种方法](https://www.cnblogs.com/kismetv/p/8757260.html)

# 资料

[请求流程-IT老兵哥](https://segmentfault.com/a/1190000021137583)

