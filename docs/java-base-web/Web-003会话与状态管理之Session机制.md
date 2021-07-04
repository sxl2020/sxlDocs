# 会话与状态管理之Session机制

由于http是一种无状态的协议。

在实际开发中，为了记录在连续多次访问中http的访问信息，常见的方式有两种。

- 一种是Cookie
- 另一种是Session。

## 1.Session理论知识

### 1.1 session的定义

session，中文经常翻译为会话，其本来的含义是指有始有终的一系列动作/消息，比如打电话是从拿起电话拨号到挂断电话这中间的一系列过程可以称之为一个session。

session在Web开发环境下的语义又有了新的扩展，它的含义是指一类用来在客户端与服务器端之间保持状态的解决方案。有时候Session也用来指这种解决方案的存储结构。

### 1.2 session机制

session机制采用的是**在服务器端保持 HTTP 状态信息的方案** 。

服务器使用一种类似于散列表的结构(也可能就是使用散列表)来保存信息。

当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否包含了一个session标识(即sessionId),如果已经包含一个sessionId则说明以前已经为此客户创建过session，服务器就按照session id把这个session检索出来使用(如果检索不到，可能会新建一个，这种情况可能出现在服务端已经删除了该用户对应的session对象，但用户人为地在请求的URL后面附加上一个JSESSION的参数)。如果客户请求不包含sessionId，则为此客户创建一个session并且生成一个与此session相关联的sessionId，这个session id将在本次响应中返回给客户端保存。

### 1.3 保存session id 的 几种方式

**① cookie方式**

保存session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。

**②URL重写方式**

由于cookie可以被人为的禁用，必须有其它的机制以便在cookie被禁用时仍然能够把session id传递回服务器，经常采用的一种技术叫做URL重写，就是把session id附加在URL路径的后面，附加的方式也有两种，一种是作为URL路径的附加信息，另一种是作为查询字符串附加在URL后面。网络在整个交互过程中始终保持状态，就必须在每个客户端可能请求的路径后面都包含这个session id。

### 1.4 Session Cookie

session通过SessionID来区分不同的客户, session是以cookie或URL重写为基础的，默认使用cookie来实现，系统会创造一个名为JSESSIONID的输出cookie，这称之为session cookie,以区别persistent cookies(也就是我们通常所说的cookie)。

session cookie是存储于浏览器内存中的，并不是写到硬盘上的，通常看不到JSESSIONID，但是当把浏览器的cookie禁止后，web服务器会采用URL重写的方式传递Sessionid，这时在地址栏可以看到。

session cookie针对某一次会话而言，会话结束session cookie也就随着消失了，而persistent cookie只是存在于客户端硬盘上的一段文本。

关闭浏览器，只会使浏览器端内存里的session cookie消失，但不会使保存在服务器端的session对象消失，同样也不会使已经保存到硬盘上的持久化cookie消失。



## 2. Session的使用

### 2.1 Session的创建与删除

- **创建**

在含有jsp的页面中，Session的创建时机受到以下几种情况的约束：

由page 指定的 session 属性来决定： 

1). 默认情况下, 第一次访问一个 WEB 应用的一个 JSP 页面时, 该页面都必须有一个和这个请求相关联的 Session 对象. 
因为 page 指定的 session 属性默认为 true

2). 若把 session 属性改为 false, JSP 页面不会要求一定有一个 Session 对象和当前的 JSP 页面相关联
所以若第一次访问当前 WEB 应用的 JSP 页面时, 就不会创建一个 Session 对象. 

3). 创建一个 Session 对象: 若 page 指定的 session 设置为 false 或 在 Servlet 中可以通过以下 API 获取 Session 对象. 

request.getSession(flag): 若 flag 为 true, 则一定会返回一个 HttpSession 对象, 如果已经有和当前 JSP 页面关联的 HttpSession
对象, 直接返回; 如果没有, 则创建一个新的返回. flag 为 false: 若有关联的, 则返回; 若没有, 则返回 null

request.getSession(): 相当于 request.getSession(true);

- **删除**

session在下列情况下被删除：
A．程序调用HttpSession.invalidate()
B．距离上一次收到客户端发送的session id时间间隔超过了session的最大有效时间
C．服务器进程被停止

关闭浏览器只会使存储在客户端浏览器内存中的session cookie失效，不会使服务器端的session对象失效。

### 2.2 跨窗口的会话跟踪

通常session cookie是不能跨窗口使用的(IE 8 版本以前)，当你新开了一个浏览器窗口进入相同页面时，系统会赋予你一个新的session id，这样信息共享的目的就达不到了。

此时可以先把session id保存在persistent cookie中(通过设置cookie的最大有效时间)，然后在新窗口中读出来，就可以得到上一个窗口的session id了，这样通过session cookie和persistent cookie的结合就可以实现了跨窗口的会话跟踪。

### 2.3 Session的超时管理

WEB服务器无法判断当前的客户端浏览器是否还会继续访问，也无法检测客户端浏览器是否关闭。所以，即使客户已经离开或关闭了浏览器，WEB服务器还要保留与之对应的HttpSession对象。 

随着时间的推移而不断增加新的访问客户端，WEB服务器内存中将会因此积累起大量的不再被使用的HttpSession对象，并将最终导致服务器内存耗尽。 

**WEB服务器采用“超时限制”的办法来判断客户端是否还在继续访问**。如果某个客户端在一定的时间之内没有发出后续请求，WEB服务器则认为客户端已经停止了活动，结束与该客户端的会话并将与之对应的HttpSession对象变成垃圾。

如果客户端浏览器超时后再次发出访问请求，WEB服务器则认为这是一个新的会话的开始，将为之创建新的HttpSession对象和分配新的会话标识号。 

会话的超时间隔可以在web.xml文件中设置，其默认值由Servlet容器定义。

```xml
<session-config>
		<session-timeout>30</session-timeout>
</session-config>
```



### 2.4 使用URL重现实现Session跟踪

Servlet规范中引入了一种补充的会话管理机制，它允许不支持Cookie的浏览器也可以与WEB服务器保持连续的会话。这种补充机制要求在响应消息的实体内容中必须包含下一次请求的超链接，并将会话标识号作为超链接的URL地址的一个特殊参数。 

将会话标识号以参数形式附加在超链接的URL地址后面的技术称为URL重写。如果在浏览器不支持Cookie或者关闭了Cookie功能的情况下，WEB服务器还要能够与浏览器实现有状态的会话，就必须对所有可能被客户端访问的请求路径（包括超链接、form表单的action属性设置和重定向的URL）进行URL重写。 

HttpServletResponse接口中定义了两个用于完成URL重写方法：

- **encodeURL方法** 
- **encodeRedirectURL方法**



### 2.5 HttpSession 接口中的常用方法

在HttpSession 接口中，提供了如下方法供开发人员使用，具体的方法如下：

```
getId方法
getCreationTime方法
getLastAccessedTime方法
setMaxInactiveInterval方法
getMaxInactiveInterval方法
isNew方法
如果客户端请求消息中返回了一个与Servlet程序当前获得的HttpSession对象的会话标识号相同的会话标识号，则认为这个HttpSession对象不是新建的。
invalidate方法
getServletContext方法
setAttribute方法
getAttribute方法
removeAttribute方法
getAttributeNames方法
```



### 2.6 Session 的典型应用

在Web开发中，使用Session来实现的典型应用有：

- 使用Session实现购物车 
- 利用Session防止表单重复提交 
- 利用Session实现一次性验证码 