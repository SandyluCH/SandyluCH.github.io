### 关于App与H5之间通信的笔记
android和ios区别：
- android与H5之间通信可以直接通过window下的自定义的方法
- iOS与H5之间通信可以通过原生、桥接等方法


iOS 
如果使用的是UIWebView则用桥接[WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)兼容性比较好； 
如果是WKWebView， 则有两种方式：
- 1、原生方法
  ```window.webkit.messageHandlers.[方法名].postMessage ```
- 2、桥接
  [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge), 操作参照官网文档，
  该库主要是对ios中navigationDelegate: WKNavigationDelegate (wkwebview)的代理 做了一层拦截， 针对https://__bridge_load_地址进行拦截操作js对象注入
- 3、古老的方法
  拦截url请求， 在url中带入参数或方法名  



