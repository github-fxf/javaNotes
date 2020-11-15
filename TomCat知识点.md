

# 第一部分 Tomcat系统架构与原理剖析



## 1. 浏览器访问服务器的流程

http请求的处理过程

![1600953418464](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600953418464.png)

![1600953459094](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600953459094.png)

注意：浏览器访问服务器使用的是Http协议，Http是应用层协议，用于定义数据通信的格式，具体的数据传输使用的是TCP/IP协议。

## 2. Tomcat系统总体架构

### 2.1 Tomcat请求处理大致过程

Tomcat是一个Http服务器（能够接收并且处理Http请求，所以Tomcat是一个Http服务器）

我们使用浏览器向某一个网站发送请求，发出的是Http请求，那么在远程，Http服务器接收到这个请求之后，会调用具体的程序（java类）进行处理，往往不同的请求由不同的java类完成处理。

![1600954435488](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600954435488.png)

![1600954453297](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600954453297.png)

Http服务器接收到请求之后，把请求交给servlet容器来处理，Servlet容器通过Servlet接口调用业务类。Servlet接口和Servlet容器这一整套内容叫做Servlet规范。

注意：Tomcat既按照Servlet规范的要求去实现了Servlet容器，同时它也具备Http服务器的功能。

Tomcat的两个重要身份

1）http服务器

2）Tomcat是一个Servlet容器

### 2.2 Tomcat Servlet 容器处理流程

当用户请求某个URL资源时 

1）HTTP服务器会把请求信息使用ServletRequest对象封装起来

2）进一步去调用Servlet容器中某一个具体的Servlet

3）在2）中，Servlet容器拿到请求后，根据URL和Servlet的映射关系，找到相应的Servlet

4）如果Servlet还没有被加载，就用反射机制创建这个Servlet，并调用Servlet的init方法来完成初始化

5）接着调用这个具体Servlet的service方法来处理请求，请求处理结果使用ServletResponse对象封装

6）把ServletResponse对象返回给HTTP服务器，HTTP服务器会把响应发送给客户端

![1600955343144](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600955343144.png)

![1600955349888](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1600955349888.png)

### 2.3 Tomcat 系统总体架构

通过上面的讲解，我们发现Tomcat有两个非常重要的功能需要完成

1) 和客户端浏览器进行交互，进行socket通信，将字节流和Request/Response等对象进行转换

2）servlet容器处理业务逻辑

![1603811357449](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1603811357449.png)

