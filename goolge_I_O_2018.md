### [Flutter Beta3新特性](https://juejin.im/entry/5af3887b6fb9a07ace58df5e)
### [2018 web](https://juejin.im/post/5af2a6e1f265da0b9c109f8a)
####  [Service Worker（服务工作线程）](https://developers.google.com/web/fundamentals/primers/service-workers/)    

丰富的离线体验、定期的后台同步以及推送通知等通常需要将面向本机应用的功能将引入到网络应用中。
服务工作线程提供了所有这些功能所依赖的技术基础
   
##### 简介
服务工作线程是浏览器在后台独立于网页运行的脚本，它打开了通向不需要网页或用户交互的功能的大门。
现在，他们已经包括如推送通知和后台同步等功能。将来，服务工作线程将会支持如定期同步或地理围栏等
其他功能。

注意事项：
1. 它是一种JavaScript工作线程，无法直接访问DOM。服务工作线程通过相应postMessage接口发送的消息来
与其控制的页面通信，页面可在必要时对DOM执行操作。
2. 服务工作线程是一种可编程网络代理，让您能够控制页面所发送网络请求的处理方式
3. 它在不用时会被中止，并在下次有需要时重启，因此，您不能依赖于服务工作线程的onfetch和onmessage
处理程序中的全局状态。如果存在您需要持续保存并在重启后加以重用的信息，服务工作线程可以访问IndexedDB API.
4. 服务工作线程广泛地利用了[promise](https://developers.google.com/web/fundamentals/primers/promises)。

##### 生命周期
服务工作线程的生命周期完全独立于网页。
要为网站安装服务工作线程，您需要现在页面的JavaScript中注册。注册服务工作线程将会导致浏览器在后台启动服务工作
线程安装步骤。

在安装过程中，您通常需要缓存某些静态资产。如果所有文件均已成功缓存，那么服务工作线程就安装完毕。如果任何文件下载
失败或者缓存失败，那么安装步骤将会失败，服务工作线程就无法激活（即不会安装）。如果发生这种情况，不必担心，它下次
会再试一次。如果安装完成，您可以知道您已在缓存中获得哪些静态资产。

安装之后，接下来是激活步骤，这是管理旧缓存的绝佳机会。

激活之后，服务工作线程将会对其作用域内的所有页面实施控制。不过，首次注册该服务工作线程的页面需要再次加载才会受其
控制。服务工作线程实施控制后，它将处于以下两种状态之一：服务工作线程终止以节省内存，或处理获取和消息事件，从页面
发出网络请求或消息后将会出现后一种状态。

生命周期如下：
![avatar](/img/service_worker_cycle.png)

Service Workder可能拥有以下六种状态的一种：解析成功(parsed)、正在安装（installing）、安装成功（installed）、正在
激活（activating）、激活成功（activated）、废弃（redundant）
+ 1、解析成功（parsed）

首次注册Serice workder时，浏览器解决脚本并获得入口点。如果解析成功（而且满足其他条件，如HTTPS协议），就可以访问到
Service Worker注册对象（registration object）,就可以访问到Service Worker注册对象（registration Object）,其中
包含Service Worker的状态及其作用域。

````
if ('serviceWorker' in navigator) {  
    navigator.serviceWorker.register('./sw.js')
    .then(function(registration) {
        console.log("Service Worker Registered", registration);
    })
    .catch(function(err) {
        console.log("Service Worker Failed to Register", err);
    })
} 

````

Service Worker注册成功，并不意味着它已经完成安装，也不能说明它已经激活，仅仅是脚本被成功解析，与document同源
，而且原协议是HTTPS。

+ 2、正在安装（Installing）

Service Worker脚本解析完成后，浏览器会试着安装，进入下一状态，‘installing’。在Service Worker（注册registration）
对象中，我们可以通过installing子对象检查该状态。

````
/* In main.js */
navigator.serviceWorker.register('./sw.js').then(function(registration) {  
    if (registration.installing) {
        // Service Worker is Installing
    }
}) 

````

在installing状态中，Service Worker脚本中的install事件被执行。 我们通常在安装事件中，为document缓存静态
文件。

````
/* In sw.js */
self.addEventListener('install', function(event) {  
  event.waitUntil(
    caches.open(currentCacheName).then(function(cache) {
      return cache.addAll(arrayOfFilesToCache);
    })
  );
}); 

````

若事件中有event.waitUntil()方法，则installing事件会一直等到该方法中的Promise完成之后才会成功；
若Promise被拒，则安装失败，Service worker直接进入废弃（redundant）状态。

````
/* In sw.js */
self.addEventListener('install', function(event) {  
  event.waitUntil(
   return Promise.reject(); // Failure
  );
}); 
// Install Event will fail

````


+ 3、安装成功/等待中（Installed/Waiting）

如果安装成功，服务工作线程进入安装成功（installed）(也称为等待中[waiting])状态。在此状态中，它
是一个有效的但尚未激活的worker.它尚未纳入document的控制，确切来说是在等待着从当前worker接手。

在service worker注（registration）对象中，可通过waiting子对象检查该状态。

````
/* In main.js */
navigator.serviceWorker.register('./sw.js').then(function(registration) {  
    if (registration.waiting) {
        // Service Worker is Waiting
    }
}) 

````

这是通过App用户升级新版本或自动升级的好时机。

+ 4.正在激活（Activating）

处于waiting状态的service worker,在以下之一的情况下，会被处罚activating状态。
+ (1)当前义务已激活状态的worker
+ (2) Service Workder脚本中的self.skipWaiting()方法被调用
+ (3) 用户已关闭Service Worker作用域下的所有页面，从而释放了此前处于激活态的worker
+ (4) 超出指定时间，从而释放此前处于激活态的worker

处于activating状态期间，Service Workder 脚本中的activate事件被执行。我们通常在activate事件中，清理
cache中的文件。

````
/* In sw.js */
self.addEventListener('activate', function(event) {  
  event.waitUntil(
    // 获取所有 cache 名称
    caches.keys().then(function(cacheNames) {
      return Promise.all(
        // 获取所有不同于当前版本名称 cache 下的内容
        cacheNames.filter(function(cacheName) {
          return cacheName != currentCacheName;
        }).map(function(cacheName) {
          // 删除内容
          return caches.delete(cacheName);
        })
      ); // end Promise.all()
    }) // end caches.keys()
  ); // end event.waitUntil()
}); 

````

与install事件类似，如果activate事件中存在event.waitUntil()方法，则在其中的Promise完成之后，激活才会成功。
如果Promise被拒，激活事件失败，Service Worker进入废弃（redundant）状态。

+ 5、激活成功（Activated）

如果激活成功，Service Worker进入active状态。在此状态中， 其成为接收document全面控制的激活态worker。在Service 
Worker 注册（register）对象中，可以通过active子对象检查此状态
````
/* In main.js */
navigator.serviceWorker.register('./sw.js').then(function(registration) {  
    if (registration.active) {
        // Service Worker is Active
    }
}) 

````

如果Service Worker处于激活态，就可以应对事件性事件————fetch和message
````
/* In sw.js */

self.addEventListener('fetch', function(event) {  
  // Do stuff with fetch events
});

self.addEventListener('message', function(event) {  
  // Do stuff with postMessages received from document
}); 

````

+ 6、废弃（Redundant）

Service Worker可能以下之一的原因被废弃（redundant）
   - (1)installing事件失败
   - (2)activating事件失败
   - (3)新的Service Worker替换其成为激活态worker

如果Service Worker因前两个原因失败，我们在开发者工具的resouces中看到service workers相关信息
如果一存在前一版本的激活态Service Worker,它会继续保持对document的控制。



#### PWA渐进式应用Progressive Web Apps

[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)

   优点：
   + (1). Responsive : to fit any form factor
   + (2). Connectivity independent : 使用service workers 逐步提升让他们可以离线工作
   + (3). App-like-interactions ： Adopt a Shell + Content application model to create appy navigations & interactions
   + (4). Fresh ： 随着service worker 更新进程，它总是最新的 
   + (5). Safe安全：  由TLS(a service worker requirement)来提供服务 从而阻止嗅探
   + (6). Discoverable ：Are identifiable as “applications” thanks to W3C Manifests and Service Worker registration scope allowing search engines to find them
   + (7). Re-engageable:Can access the re-engagement UIs of the OS; e.g. Push Notifications
   + (8). Installable: to the home screen through browser-provided prompts, allowing users to “keep” apps they find most useful without the hassle of an app store
   + (9).Linkable: meaning they’re zero-friction, zero-install, and easy to share. The social power of URLs matters.
   








#### Web Media
   
   	Media Session API 允许页面为标准媒体交互提供自定义行为。

   	vimeo 通过这个接口实现了锁屏时暂停播放视频。

	Media Capabilities API 使得浏览器能够自动根据设备状况选择视频流的来源。

	Picture-in-Picture Support 使得视频能够在显示屏的任意位置播放。

	Presentation API 能够更加精确的控制在第二显示屏上展示的内容。

	AV1 视频编码格式也将在今年应用于 chrome。

#### WebAssembly

#### Speedometer 
Speedometer 是一个衡量 Web 应用性能的浏览器基准。
