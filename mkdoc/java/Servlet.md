# Servlet

Java Servlet 是运行在 Web 服务器或者应用服务器上的程序，处理 HTTP 请求并返回响应，实现用户交互。

## Servlet 生命周期

Servlet 的生命周期大致可以分为以下三个阶段：

- 加载和初始化
- 处理服务
- 销毁和垃圾回收

### 加载和初始化

服务器第一次访问 Servlet 时会创建 Servlet 实例。之后服务器调用`init()`方法初始化 Servlet 对象，创建或者加载初始化数据。`init()`方法被设计成只调用一次。它在第一次创建 Servlet 的时候被调用，在后续用户每次用户请求时不再调用。

Servlet 创建于用户第一次调用对应于该 Servlet 的 URL 时，但是也可以指定 Servlet 在服务器第一次启动时加载。

当用户调用一个 Servlet 时，就会创建一个 Servlet 实例，每个用户请求都会产生一个新的线程，在适当时移交给`doGet`或者`doPost`方法。`init()`方法简单地创建或者加载一些数据，这些数据将被用于 Servlet 的整个生命周期。

### 处理服务

#### service()

`service()`方法是执行实际任务的主要方法。Servlet 容器调用`service()`方法来处理客户端的请求，并把格式化的响应写回给客户端。

每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。`service()`方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当时调用 doGET、doPost、doPut、doDelete 等方法。

`service()`方法定义如下：

```java
public void service(ServletRequest request,
                    ServletResponse response)
      throws ServletException, IOException{
}
```

`service()`方法由容器调用，所以开发人员无需对`service()`方法做任何动作，只需要根据来自客户端的请求类型来重写 doGet()或者 doPost()方法即可。

#### doGet()
GET请求来自于一个URL的正常请求，或者来自于一个未指定METHOD的HTML表单，它由doGet()方法处理。
```java
public void doPost(HttpServletRequest request,
                   HttpServletResponse response)
    throws ServletException, IOException {
    // Servlet 代码
}
```