---
layout: post   	
catalog: true 	
tags:
    - Web
---





## 前言

web1.0	强调的是系统资源的共享，是无状态的

web2.0	有了交互，请求和请求之间存在依赖，引入了session和cookie机制来实现状态的记录

## Session和cookie的特征

* session和cookie都是由服务器生成的，都是用来存储特定的值（键值对应）。

* 一般来说，SessionID会以类似于cookie的方式返回给客户端。SessionID是服务器用来识别、操作存储session值的对象的。
  一般来说，在服务器端，session的存储方式有文件方式、数据库方式，
  sessionID就是用来识别这个文件的（文件名相关)、识别数据库的某一条记录。
  sessionID并不是session值。
* 客户端（浏览器）在发送请求的时候，会自动将存活、可用的cookie封装在请求头中和请求一起发送。
* cookie和session都是有其生命周期的。
  - cookie的生命周期，一般来说，cookie的生命周期受到两个因素的影响
    - cookie自身的存活时间：是服务器生成cookie时去设定的。
    - 客户端是否保留了cookie。客户端是否保留cookie，只对客户
    端自身有影响，对其它封包工具是没有影响的。
  - session的生命周期，一般来说，session的生命周期也是受到两个因素的影响
    - 服务器对于session对象的保存的最大时间的设置。
    - 客户端进程是否关闭。客户端进程关闭，只对客户端自身有影
      响，对其它封包工具是没有影响的。
* cookie和session都是有其作用域的。

## 为什么session比cookie安全?

* cooke是存储在客户端的，是可见的，是可以改变的。

- session是存储在服务器端的，是不可见的，是不可改变的。

## Token

假设登录请求打到了 A 机器，A 机器生成了 session 并在 cookie 里添加 sessionId 返回给了浏览器，那么问题来了：下次添加购物车时如果请求打到了 B 或者 C，由于 session 是在 A 机器生成的，此时的 B,C 是找不到 session 的，那么就会发生无法添加购物车的错误，就得重新登录了，此时请问该怎么办。主要有以下三种方式

* session 复制

A 生成 session 后复制到 B, C，这样每台机器都有一份 session，无论添加购物车的请求打到哪台机器，由于 session 都能找到

* session 粘连

让每个客户端请求只打到固定的一台机器上，比如浏览器登录请求打到 A 机器后，后续所有的添加购物车请求也都打到 A 机器上

* session 共享

目前各大公司普遍采用的方案，将 session 保存在 redis，memcached 等中间件中，请求到来时，各个机器去这些中间件取一下 session



首先请求方输入自己的用户名，密码，然后 server 据此生成 token，客户端拿到 token 后会保存到本地（以cookie的形式或 local storage），之后向 server 请求时在请求头带上此 token 即可。

token 只存储在浏览器中，服务端却没有存储，这样的话我随便搞个 token 传给 server 也行？

```
答：server 会有一套校验机制，校验这个 token 是否合法。
```

怎么不像 session 那样根据 sessionId 找到 userid 呢，这样的话怎么知道是哪个用户？

```
答：token 本身携带 uid 信息
```

如何校验 token 呢？

```
答：我们可以借鉴 HTTPS 的签名机制来校验
```

token 主要由三部分组成

```
header：指定了签名算法

payload：可以指定用户 id，过期时间等非敏感数据

Signature: 签名，server 根据 header 知道它该用哪种签名算法，再用密钥根据此签名算法对 head + payload 生成签名，这样一个 token 就生成了。
```



当 server 收到浏览器传过来的 token 时，它会首先取出 token 中的 header + payload，根据密钥生成签名，然后再与 token 中的签名比对，如果成功则说明签名是合法的，即 token 是合法的。而且你会发现 payload 中存有我们的 userId，所以拿到 token 后直接在 payload 中就可获取 userid，避免了像 session 那样要从 redis 去取的开销

你会发现这种方式确实很妙，只要 server 保证密钥不泄露，那么生成的 token 就是安全的，因为如果伪造 token 的话在签名验证环节是无法通过的，就此即可判定 token 非法。

不过需要注意的是，token 一旦由 server 生成，它就是有效的，直到过期，无法让 token 失效，除非在 server 为 token 设立一个黑名单，在校验 token 前先过一遍此黑名单，如果在黑名单里则此  token 失效，但一旦这样做的话，那就意味着黑名单就必须保存在 server，这又回到了 session 的模式，那直接用 session 不香吗。所以一般的做法是当客户端登出要让 token 失效时，直接在本地移除 token 即可，下次登录重新生成 token 就好。

另外需要注意的是 token 一般是放在 header 的 Authorization 自定义头里，不是放在 Cookie 里的，这主要是为了解决跨域不能共享 Cookie 的问题

## Cookie 有哪些局限性

Cookie 跨站是不能共享的，难以实现多应用（多系统）的单点登录（SSO）

**所谓单点登录，是指在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。**

但如果用 token 来实现 SSO 会非常简单

只要在 header 中的 authorize 字段（或其他自定义）加上 token 即可完成所有跨域站点的认证。

在移动端原生请求是没有 cookie 之说的，而 sessionid 依赖于 cookie，sessionid 就不能用 cookie 来传了，如果用 token 的话，由于它是随着 header 的 authoriize 传过来的，也就不存在此问题，换句话说token 天生支持移动平台，可扩展性好

综上所述，token 具有存储实现简单，扩展性好这些特点

## token 缺点

* token 太长了

token 是 header, payload 编码后的样式，所以一般要比 sessionId 长很多，很有可能超出 cookie 的大小限制（cookie 一般有大小限制的，如 4kb）

* 不安全

token 是存在浏览器的，它太长放在 cookie 里可能导致 cookie 超限，那就只好放在 local storage 里，这样会造成安全隐患，因为 local storage 这类的本地存储是可以被 JS 直接读取的，另外由上文也提到，token 一旦生成无法让其失效，必须等到其过期才行，这样的话如果服务端检测到了一个安全威胁，也无法使相关的 token 失效。

**所以 token 更适合一次性的命令认证，设置一个比较短的有效期**

## CSRF 攻击

攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。由于浏览器曾经认证过（cookie 里带来 sessionId 等身份认证的信息），所以被访问的网站会认为是真正的用户操作而去运行

```
比如用户登录了某银行网站（假设为 http://www.examplebank.com/，并且转账地址为 http://www.examplebank.com/withdraw?amount=1000&transferTo=PayeeName），登录后 cookie 里会包含登录用户的 sessionid，攻击者可以在另一个网站上放置如下代码
<img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">
```

那么如果正常的用户误点了上面这张图片，**由于相同域名的请求会自动带上 cookie**，而 cookie 里带有正常登录用户的 sessionid，类似上面这样的转账操作在 server 就会成功，会造成极大的安全风险

**CSRF 攻击的根本原因在于对于同样域名的每个请求来说，它的 cookie 都会被自动带上，这个是浏览器的机制决定的**