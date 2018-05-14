##  [Service Worker（服务工作线程）](https://developers.google.com/web/fundamentals/primers/service-workers/)    

* process.env.PUBLIC_URL的origin 和 window.location的origin如果是在不同的origin，  service worker won't
work.

丰富的离线体验、定期的后台同步以及推送通知等通常需要将面向本机应用的功能将引入到网络应用中。
服务工作线程提供了所有这些功能所依赖的技术基础
   
### 简介
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

### 生命周期

#### 目的
服务工作线程生命周期的目的：
+ 实现离线优先
+ 允许新服务工作线程自行做好运行准备，无需中断当前的服务工作线程
+ 确保整个过程中作用域页面由同一个服务工作线程（或者没有服务工作线程）控制
+ 确保每次只运行网站的一个版本



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

如果Service Worker因前两个原因失败，我们在开发者工具中看到service workers相关信息
如果一存在前一版本的激活态Service Worker,它会继续保持对document的控制。




### [浏览器支持情况](https://jakearchibald.github.io/isserviceworkerready/#moar)

### 需要HTTPS支持

在开发过程中，可以通过localhost使用服务工作线程，但如果要在网站上部署服务工作线程，需要在服务器上
设置HTTPS.

使用服务工作线程，可以劫持链接，编撰以及过滤响应，为了避免中间人的恶意使用，可仅在通过HTTPS提供的页面
上注册服务工作线程，如此我们便知道浏览器接收的服务工作线程在整个网络传输过程中都没有被篡改。


### 注册服务工作线程

要安装服务工作线程，您需要通过在页面中对其进行注册来启动安装。这将告诉浏览器服务工作线程Javascript文件的
文件。
````
if ('serviceWorker' in navigator) {
  window.addEventListener('load', function() {
    navigator.serviceWorker.register('/sw.js').then(function(registration) {
      // Registration was successful
      console.log('ServiceWorker registration successful with scope: ', registration.scope);
    }).catch(function(err) {
      // registration failed :(
      console.log('ServiceWorker registration failed: ', err);
    });
  });
}

````

此代码用于检查Service Worker API是否可用，如果可用，则在页面加载后注册位于/sw.js的服务工作线程。

每次页面加载无误时，即可调用register();浏览器将会判断服务工作线程是否已注册并做出相应的处理。

register()方法的精妙之处在与服务工作线程文件的位置。您会发现在上述代码中服务工作线程文件位于根网域，
这以为着服务工作线程的作用域将是整个来源。换句话说，服务工作线程将接受此网域上所有的事项的fetch事件。
如果我们在/example/sw.js处注册服务工作线程文件，则服务工作线程将只能看到网址以/example/开头
（即/example/page1/、 /example/page2/）的页面的fetch事件。即，服务工作线程注册的默认作用域是与脚本
网址相对的'./'。这意味着如果您在//example.com/foo/bar.js注册一个服务工作线程，则它的默认作用域为
//example.com/foo/。我们调用页面、工作线程和共享的工作线程clients。您的服务工作线程只能控制位于作用域
内的客户端。在客户端“受控制”后，它在获取数据时将执行作用域内的服务工作线程。您可以通过navigator.serviceWorder.controller(其将为null或一个服务工作线程实例)检测客户端是否受控制。



首次实施服务工作线程时，可以通过chrome://serviceworker-internals来查看服务工作线程详情。
其他情况可以通过chrome://inspect/#service-workers来查看您的网站的服务工作线程启用情况

### 安装服务工作线程

在受控页面启动注册流程后，关于处理install事件的服务工作线程脚本内容示例如下：

加入您需要为安装事件定义回调，并决定想要缓存的文件。
````
self.addEventListener('install', function(event) {
  // Perform install steps
});

````

在install回调的内部，我们需要执行以下步骤：
1. 打开缓存 ——> 2. 缓存文件 ———> 3. 确认所有需要的资产是否缓存

````
var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});

````

上述代码中， 以所需的缓存名称调用caches.open(),之后再调用cache.addAll()并传入文件数组。event.waitUntil()
方法带有promise参数并使用它来判断安装所花费的时间以及安装是否成功。

如果所有文件都成功缓存，则将安装服务工作线程。如果有任何文件无法下载，则安装步骤将失败，这可让您所依赖与所定义的
所有资产，但也意味着需要对您决定在安装步骤缓存的文件列表格外留意。定义一个过长的文件列表将会增加文件缓存失败的几率，
从而导致服务工作线程未能安装。


实际您可以在install事件中执行其他任务，或完全避免设置install事件侦听器。


### 缓存和返回请求

在安装服务工作线程且用户转至其他页面或刷新当前页面后，服务工作线程将开始接收fetch事件。
示例如下：

````
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
      .then(function(response) {
        // Cache hit - return response
        if (response) {
          return response;
        }
        return fetch(event.request);
      }
    )
  );
});

````

如果发现匹配的响应，则返回缓存的值，否则将调用fetch以发出网络请求，并将从网络检索到的任何数据作为结果返回。上述代码
例子是 使用了在安装步骤中缓存的所有资产。

如果希望连续缓存新请求，可以通过处理fetch请求的响应并将其添加到缓存中来实现，示例代码如下：
````
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
      .then(function(response) {
        // Cache hit - return response
        if (response) {
          return response;
        }

        // IMPORTANT: Clone the request. A request is a stream and
        // can only be consumed once. Since we are consuming this
        // once by cache and once by the browser for fetch, we need
        // to clone the response.
        var fetchRequest = event.request.clone();

        return fetch(fetchRequest).then(
          function(response) {
            // Check if we received a valid response
            if(!response || response.status !== 200 || response.type !== 'basic') {
              return response;
            }

            // IMPORTANT: Clone the response. A response is a stream
            // and because we want the browser to consume the response
            // as well as the cache consuming the response, we need
            // to clone it so we have two streams.
            var responseToCache = response.clone();

            caches.open(CACHE_NAME)
              .then(function(cache) {
                cache.put(event.request, responseToCache);
              });

            return response;
          }
        );
      })
    );
});

````

执行的操作如下：
- 在fetch请求中添加对.then()的回调
- 获得响应后，执行以下检查
	- 确保响应有效
	- 检查并确保响应的状态为200
	- 确保响应类型为basic,亦即有自身发起请求。这意味着，对第三方资产的请求不会添加到缓存。
- 如果通过检查，则克隆响应（因为该响应是[Stream](https://streams.spec.whatwg.org/)）,
因此主体只能使用一次，由于我们想要返回能被浏览器使用的响应，并将其传递到缓存以供使用，一次
需要克隆一份副本，我们将一份发送给浏览器，另一份则保留在缓存里。

### 更改服务工作线程

在某个时间点，您的服务工作线程需要更新。此时，您需要遵循以下步骤：
	
- 更新您的服务工作线程JavaScript文件。用户导航至您的站点时，浏览器会尝试在后台重新下载定义服务工作线程
的脚本文本，如果服务工作线程文件与其当前所用的文件存在字节差异，则将其视为“新服务工作线程”
- 新服务工作线程将会启动，且将会触发install事件
- 此时，就服务工作线程仍控制着当前页面，因此新服务工作线程将进入waiting状态
- 当网站上当前打开的页面关闭是，旧服务工作线程将会被中止，新服务工作线程将会取得控制权。
- 新服务工作线程取得控制权后，将会触发其activate事件

出现在activate回调中的一个常见任务是缓存管理。 因为在安装步骤中清除了任何旧缓存，则继续控制所有当前页面
的任何旧服务工作线程将无法从缓存中提供文件。

比如说我们有一个名为 'my-site-cache-v1' 的缓存，我们想要将该缓存拆分为一个页面缓存和一个博文缓存。这就意味着在安装步骤中我们创建了两个缓存：'pages-cache-v1' 和 'blog-posts-cache-v1'，且在激活步骤中我们要删除旧的 'my-site-cache-v1'。

以下代码将执行此操作，具体做法为：遍历服务工作线程中的所有缓存，并删除未在缓存白名单中定义的任何缓存。

````
self.addEventListener('activate', function(event) {

  var cacheWhitelist = ['pages-cache-v1', 'blog-posts-cache-v1'];

  event.waitUntil(
    caches.keys().then(function(cacheNames) {
      return Promise.all(
        cacheNames.map(function(cacheName) {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
});

````

### 其他

如果工作线程注册后未在chrome://inspect/#service-workers 或chrome://serviceworker-internals中显示。
则可能是引发错误或想event.waitUntil()发送被拒绝的promise 而导致无法安装。

debug 方式：  在chrome://serviceworker-internals 并选中 “open DevTools window and pause JavaScript 
execution on service worker startup for debugging”,然后将调试程序语句置于安装事件开始处
，参见[exception breakpoint](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints#exceptions)

### fetch()默认值

默认情况下没有凭据
使用fetch时，默认情况下请求中不包含Cookie等凭据。如需凭据，改为调用：
````
fetch(url,{
	credentials: 'include'
	});

````

如果网址具有相同的来源，则默认发送凭据，否则忽略。获取的行为更接近于其他的CORS请求。

### 非CORS请求失败
默认情况下，从不支持CORS的第三方网址中获取资源将会失败。您可以向请求中添加no-CORS选项来克服
此问题，不过这可能会导致“不透明”的响应，这意味着您无法辨别响应是否成功。

````
cache.addAll(urlsToPrefetch.map(function(urlToPrefetch) {
  return new Request(urlToPrefetch, { mode: 'no-cors' });
})).then(function() {
  console.log('All resources have been fetched and cached.');
});

````


### [更多资料](https://jakearchibald.github.io/isserviceworkerready/resources.html#moar)



