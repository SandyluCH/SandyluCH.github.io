## 性能优化笔记1

### 对js和css等资源文件进行加载优化

#### 先来了解preload与prefetch  [摘自](http://www.alloyteam.com/2016/05/preload-what-is-it-good-for-part1/)
[preload](https://w3c.github.io/preload/) 是一项新的 Web 标准，旨在提升性能，让 Web 开发者对加载的控制更加粒度化。它让开发者有自定义加载逻辑的能力，免受基于脚本的 loader 所带来的性能损耗。
<link rel="preload"> 它是一种声明式的获取（fetch）指令。

preload与prefetch区别：
- <link rel="prefetch"> 是一种告诉浏览器获取一项可能被下一页访问所需要的资源方式。这意味着资源将以较低优先级地获取（因为浏览器知道当前页面所需要的资源，要比我们猜测在下一页访问所需资源更重要）。这意味着 prefetch 的主要用途是加速下一页访问速度，而不是当前页面的速度。

- <link rel="preload"> 为当前页面服务， 另外它有一个as属性（as的作用：a. 浏览器可以设置正确的资源优先级，，使得资源可以被正确地加载，重要的资源不再会被延迟，不再被不重要的资源阻塞； b.浏览器会保证请求对应正确的内容安全策略（Content-Security-Policy ）指令，不会发起非法请求; c.浏览器会基于资源类型发送正确的 Accept 首部。（比如获取图片时指定对“image/webp” 的支持）; d.浏览器知道资源的类型，所以可以稍后决定资源是否在后续请求中保持可重用。）
- preload另一个不同之处， 他有onload事件。 Preload 不阻塞 window 的 onload 事件，除非该资源是被一个阻塞该事件的资源请求的。

preload 的as 可能的取值有：
“script”
“style”
“image”
“media”
“document”

[参考](https://fetch.spec.whatwg.org/#concept-request-destination)

如果省略 as 属性，或者给定一个非法值，将等同发起一个 XHR 请求，因为浏览器不知道将要获取的是什么，并且加载的优先级也会很低。


preload使用,例如：
````
<link href="css/app.8220fbcd.css" rel="preload" as="style">
<link href="css/app.8220fbcd.css" rel="stylesheet">

````


prefetch和preload不影响window.onload事件，也就是说，window.onload事件并不会去管prefetch和preload的资源有没有下载完毕，就当prefetch和preload的资源不存在。

prefetch 和preload资源在disk cache

不要对同一个资源同时使用preload和prefetch, 会造成2遍下载

prefetch加载的资源可以获取非当前页面所需要的资源，并且将其放入缓存至少5分钟（无论资源是否可以缓存）；并且，当页面跳转时，未完成的prefetch请求不会被中断；





