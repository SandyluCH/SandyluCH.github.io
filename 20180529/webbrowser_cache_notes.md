参考：
[https://my.oschina.net/zhangstephen/blog/591575](https://my.oschina.net/zhangstephen/blog/591575)
[http://www.techweb.com.cn/network/system/2016-01-05/2252395.shtml](http://www.techweb.com.cn/network/system/2016-01-05/2252395.shtml)

### http缓存
 http缓存是基于HTTP协议的浏览器文件级缓存机制。即针对文件的重复请求情况下，浏览器可以根据协议头判断从服务器端请求文件还是从本地读取文件，chrome控制台下的Frames即展示的是浏览器的http文件级缓存。以下是浏览器缓存的整个机制流程。主要是针对重复的http请求，在有缓存的情况下判断过程如下：
 - 第一步：判断expires，如果未过期，直接读取http缓存文件，不发http请求，否则进入下一步。
 - 第二步： 判断是否含有etag, 有则带上if-none-match发送请求，未修改返回304，修改返回200，否则进入下一步
 - 第三步： 判断是否含有last-modified,有则带上if-modified-since发送请求，无效返回200，有效返回304，否则直接向服务器请求

 如图所示：
![步骤图](http://7tszky.com1.z0.glb.clouddn.com/Fg9HGSm1K7AlSY_67ZwhTbKWG7QA)

如果通过etag和last-modified判断，即使返回304有至少有一次http请求，只不过返回的是304的返回内容，而不是文件内容。

[浏览器缓存详解](https://blog.csdn.net/eroswang/article/details/8302191)
[浏览器缓存机制](https://juejin.im/post/5b014aa66fb9a07ac23b04c8)




### websql

......

### indexDB

......

### cookie

### localstorage

### sessionstorage

### application cache
 application cache 是将大部分图片资源、js、css等静态资源放在mainfest文件配置中。当页面打开时，通过manifest文件来读取本地文件或是请求服务
器文件。

  离线访问对基于网络的应用而言越来越重要。虽然所有浏览器都有缓存机制，但它们不可靠，也不一定总能起到预期的作用。HTML5使用ApplicationCache接口可以解决由离线带来的部分难题。前提是你需要访问的web页面至少被在线访问过一次。





### CacheStorage

CacheStorage是在ServiceWorker的规范中定义的。CacheStorage可以保存每个ServerWorker申明的cache对象。