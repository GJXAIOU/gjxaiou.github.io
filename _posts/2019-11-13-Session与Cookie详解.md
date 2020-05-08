---
layout:     post
title:      Session 和 Cookie 详解
subtitle:   计算机网络系列博客
date:       2019-11-13
author:     GJXAIOU 
header-img: img/post-bg-computerNetwork.jpg
catalog: true
tags:
    - 计算机网络
---

## Session 和 Cookie 详解

### （一）Session 详解

**Session 是一种在服务器端保持客户端和服务器间保持会话连接状态的机制。用 Session 可以在网站的上下文不同页面间传递变量、用户身份认证、程序状态记录等。常见的形式就是配合Cookie使用，实现保存用户登录状态功能。**

**Session 是通过 SessionID（即 session 文件名）判断客户端用户的。SessionID 按照一定的算法生成保证唯一性和随机性，在 HTTP Request 里面会在 Cookie 中记录一个 Session ID。如果没有设置 Session 的生成周期， sessionID 存储在内存中，关闭浏览器后该ID自动注销；重新请求该页面，会重新注册一个sessionID。**

假设客户端禁用 Cookie ，可以通过URL或者隐藏表单传递 sessionID；

### （二）Cookie 详解

**Cookie 是通过在浏览器端保存文本数据并以此跟踪和识别用户的机制。Cookie 信息是有键值对组成的，并且附加在 HTTP 消息头中进行传递。**

下面是一个HTTP头中cookie的例子`Set-Cookie: key = value; Path=/`
Cookie中存放的信息包含cookie本身属性和用户自定义属性，一个cookie只能包含一个自定义键/值对。Cookie本身属性有”Comment” 、”Domain”、”Max-Age”、”Path”、”Secure”、”Version”。

>Comment 属性是cookie的产生着对该cookie的描述；
>
>**Domain 属性定义可访问该cookie的域名，对一些大的网站，如果希望cookie可以在子网站中共享，可以使用该属性。例如设置Domain为 .bigsite.com ,则sub1.bigsite.com和sub2.bigsite.com都可以访问已保存在客户端的cookie，这时还需要将Path设置为/。**
>
>Max-Age 属性定义cookie的有效时间，用秒计数，当超过有效期后，cookie的信息不会从客户端附加在HTTP消息头中发送到服务端。
>
>Path 属性定义网站上可以访问cookie的页面的路径，缺省状态下Path为产生cookie时的路径，此时cookie可以被该路径以及其子路径下的页面访问；可以将Path设置为/，使cookie可以被网站下所有页面访问。
>
>Secure 属性值定义cookie的安全性，当该值为true时必须是HTTPS状态下cookie才从客户端附加在HTTP消息中发送到服务端，在HTTP时cookie是不发送的；Secure为false时则可在HTTP状态下传递cookie，Secure缺省为false。
>
>Version 属性定义cookie的版本，由cookie的创建者定义。

#### Cookie 的创建

**Cookie 可以在服务器端创建，然后cookie信息附加在HTTP消息头中传到客户端，如果cookie定义了有效期，则保存在客户端本地磁盘**。当客户端再次访问服务器端时，从本机磁盘上读出原来保存的信息，附加到HTTP消息中发送给服务器端，服务器端从HTTP消息中读取信息，根据实际应用的需求进行进一步的处理。

创建cookie时如果不指定生存有效时间，则cookie只在浏览器关闭前有效，cookie会在服务器端和客户端传输，但是不会保存在客户机的磁盘上，打开新的浏览器将不能获得原先创建的cookie信息。

> Cookie信息保存在本地时会保存到当前登录用户专门目录下，保存的cookie文件名中会包含创建cookie所在页面网站的域名，当浏览器再次连接该网站时，会从本机cookie存放目录下选出该网站的有效cookie，将保存在其中的信息附加在HTTP消息头中发送到服务器端，服务器端程序就可根据上次保存在cookie的信息为访问客户提供“记忆”或个性化服务。

Cookie 也可以在客户端的浏览器中用客户端脚本(如 javascript )创建。客户端创建的 cookie 的性质和服务器端创建的cookie一样，可以保存在本地，也可以被传送到服务器端被服务器程序读取。



#### Cookie 基础知识

- cookie 是有大小限制的，大多数浏览器支持最大为 4096 字节的 Cookie

- cookie 有域和路径这个概念。域就是domain的概念，因为浏览器是个注意安全的环境，所以不同的域之间是不能互相访问 cookie 的(当然可以通过特殊设置的达到 cookie 跨域访问)。**路径就是routing的概念，一个网页所创建的 cookie 只能被与这个网页在同一目录或子目录下得所有网页访问，而不能被其他目录下得网页访问。**

- 同个网站可以创建多个 cookie ，而多个 cookie 可以存放在同一个cookie 文件中。

- cookie 存在两种类型：①:你浏览的当前网站本身设置的 cookie ②来自在网页上嵌入广告或图片等其他域来源的 第三方 cookie (网站可通过使用这些 cookie 跟踪你的使用信息)

- cookie 有两种清除方式：①:通过浏览器工具清除 cookie  ②通过设置 cookie 的有效期来清除 cookie。

- 浏览器可以通过设置来接受和拒绝访问 cookie。出于功能和性能的原因考虑，建议尽量降低 cookie 的使用数量，并且要尽量使用小 cookie。

- Session 比 Cookie 更安全吗？ 不应该大量使用Cookie吗？

    错误。Cookie确实可能存在一些不安全因素，但和JavaScript一样，即使突破前端验证，还有后端保障安全。一切都还要看设计，尤其是涉及提权的时候，特别需要注意。通常情况下，Cookie和Session是绑定的，获得Cookie就相当于获得了Session，客户端把劫持的Cookie原封不动地传给服务器，服务器收到后，原封不动地验证Session，若Session存在，就实现了Cookie和Session的绑定过程。因此，不存在Session比Cookie更安全这种说法。如果说不安全，也是由于代码不安全，错误地把用作身份验证的Cookie作为权限验证来使用。

- Session是创建在服务器上的，应该少用Session而多用Cookie，对吗？

    错。Cookie可以提高用户体验，但**会加大网络之间的数据传输量**，应尽量在Cookie中仅保存必要的数据。

- 如果把别人机器上的Cookie文件复制到我的电脑上（假设使用相同的浏览器），是不是能够登录别人的帐号呢？如何防范？

    是的。这属于Cookie劫持的一种做法。要避免这种情况，需要在Cookie中针对IP、UA等加上特殊的校验信息，然后和服务器端进行比对。

- 在IE浏览器下登录某网站，换成Firefox浏览器是否仍然是未登录状态？使用IE登录了腾讯网站后，为什么使用Firefox能保持登录状态？

    不同浏览器使用不同的Cookie管理机制，无法实现公用Cookie。如果使用IE登录腾讯网站，使用Firefox也能登录，这是由于在安装腾讯QQ软件时，你的电脑上同时安装了针对这两个浏览器的插件，可以识别本地已登录QQ号码进而自动登录。本质上，不属于共用Cookie的范畴。
