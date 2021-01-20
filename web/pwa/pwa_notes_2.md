[Progressive Web Apps Training](https://developers.google.com/web/ilt/pwa/)


## CacheStorage
[Cache API](https://developer.mozilla.org/en-US/docs/Web/API/Cache)

   
   - CacheStorage.open()

   该方法返回一个Promise (that resolves to the Cache object matching the cacheName).

   示例代码：
   ````
   //caches is a global read-only wariable,which is an instance of  CacheStorage.
    caches.open(cacheName).then(cache => {
      // Do something with you cache
    });

   ````

### Cache Methods方法
1. add()
   相当于代码：
   ````
      fetch(url).then(response =>{
         if(!response.ok){
            throw new TypeError('bad response status');
         }
         return cache.put(url,response);
      });
   ````
   用法：
   ````
    cache.add(request).then(()=>{
        // request has been added to the cache
    });

   ````


2. addAll()

参数是urls的数组。 addAll()方法将会覆盖任意之前存放在cache中并与request想匹配的key/value键值对，但是 will fail if a resulting put() operation would overwrite a previous cahce entry created by the same addAll() method.

使用示例代码:
````
cache.addAll(requests[]).then(()=>{
  // requests have been added to the cache
});

````

3. delete()

4. keys()

5. match()

6. matchAll()

7. put()
该方法允许键值对被添加到当前cache的object中。

通常，你仅仅想去fetch()一个或多个requests， 然后直接添加结果到您的缓存中。这种情况下，您最好使用cache.add()和cache.addAll()，因为他们都是下面方法的一个和多个的简化形式。
````
fetch(url).then(response => {
	if(!response.ok){
	  throw new TypeError('Bad response status ');
	}
	return cache.put(url,response);
});

````


### Events事件

1. statechange
2. updatefound
3. controllerchange
4. error
5. message
6. install 
7. active
8. fetch



## Service Worker API
[Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

## [Working with Indexed DB](https://developers.google.com/web/ilt/pwa/working-with-indexeddb-slides)
如果我们想让app变成真正的离线优先， 我们需要一个储存数据的方式。IndexDB 就是一种解决方案。

IndexDB在浏览器上提供了一个object store.
object store:
- Non-relational database
- Stores:JS objects,files,blobs等

1. Opening a database

idb.open(name,version,{upgradeCallback})

2. Creating object stores

var dbPromise = idb.open('test-db',1,function(upgradeDb){
	if(!upgradeDb.objectStoreNames.contains('firstOS')){
        upgradeDb.createObjectStore('firstOS');
    }
});

3. 定义关键字 Defining the Primary Key

示例代码：
````
upgradeDb.createObjectStore('people',{keyPath:'email'});

upgradeDb.createObjectStore('notes',{autoIncrement:true});

upgradeDb.createObjectStore('logs',{keyPath:'id', autoIncrement:true});

````

4. Definint Indexes example

示例代码：
````
var dbPromise = idb.open('test-db',function(upgreadeDb){
    if(!upgradeDb.objectStoreNames.contains('people')){
    	var peopleOS = upgradeDb.createObjectStore('people');
    	peopleOS.createIndex('email','email',{unique:true});
    }
});

````


5. Operations
	- add
	- get
	- put
	- delete
	- getAll
	- cursor

6. Transactions
  
  A wrapper around a series of operations


7. Working with data

Each data operation has this form:
  - (1)get database object from idb.open
  - (2)open transaction on database
  - (3)open object store on transaction
  - (4)optionally open index on object store
  - (5)perform operation on object store or index

8. Add data

示例代码：
````
dbPromise.then(function(db){
   var transaction = db.transaction(['people'],'readwrite');
   var store  = transaction.objectStore('people');
   store.add({name:'Fred'});
   return transaction.complete;
});

````  


9. Read Data

示例代码：
````
  dbPromise.then(function(db){
     var tx = db.transaction(['people'],'readonly');
     var store = tx.objectStore('people');
     return store.get('Fred');
  });

````

10. Update Data

示例代码：
````
  dbPromise.then(function(db){
     var tx = db.transaction(['store'],'readwrite');
     var store = tx.objectStore('store');
     var item = {name:'Fred',email:'Fred@fred.com'};
     store.put(item);
     return tx.complete;
  });

````

11. Delete Data

示例代码：
````
  dbPromise.then(function(db){
     var tx = db.transaction(['people'],'readwrite');
     var store = tx.objectStore('people');
     store.delete('Fred');
     return tx.complete;
  });

````

12. getAll() method

示例代码：
````
  dbPromise.then(function(db){
       var tx = db.transaction(['store'],'readonly');
       var store = tx.objectStore('store');
       return store.getAll();
  });

````

13. Cursors(1)

示例代码：
````
  dbPromise.then(function(db){
     var tx = db.transaction(['store'],'readonly');
     var store = tx.objectStore('store');
     return store.openCursor();
  });

````

14. Cursors(2)

示例代码：
````
  dbPromise.then(function(db){
     var tx = db.transaction(['store'],'readonly');
     var store = tx.objectStore('store');
     return store.openCursor().then(fucntion showItems(cursor){
        if(!cursor){ return ;}
        for(var field in cursor.value){
        	console.log(cursor.value[field]);
        }
        return cursor.continue.then(showItems);
     });
  });

````

15. Specifying the range

IDBKeyRange.lowerBound(indexKey);

IDBKeyRange.upperBound(indexKey);

IDBKeyRange.bound(lowerIndexKey,upperIndexKey);

16. Range example

示例代码：
````
var range = IDBKeyRange.lowerBound('soda');
dbPromise.then(function(db){
   var tx = db.transaction(['store'],'readonly');
   var store = tx.objectStore('store');
   return store.getAll(range);
});

````



##  推送和通知的介绍（ Introduction to Push Notifications）

通知是在用户设备上弹出的消息。通知可以由一个打开的应用程序本地触发，或者即使在应用程序不运行时也可以从服务器“推”到用户。它们允许您的用户选择及时更新，并允许您有效地重新使用定制内容的用户。

主要包含：
- Client: (Web Page , Service worker, User Agent)
- Push Service
- App Server

### The Notification API
client端  web page中
1. Request permission
 在创建通知之前需要获取用户的授权。
 代码示例：
````
//main.js
Notification.requestPermission(function(status){
  console.log('Notification permission status :',status);
});

````

2. Display a notification

示例代码：
````
//main.js
function displayNotification(){
	if(Notification.permission === 'granted'){
		navigator.serviceWorker.getRegistration().then(function(reg){
			 reg.showNotification('Hello World');
		});
	}
}

````

3. Add notification options

示例代码：
````
//main.js
var options = {
	body:'Here is a notification body!',
	icon:'images/example.png',
	vibrate:[100,50,100],
	data:{primaryKey:1} //allows us to identify notification
};
reg.showNotification('Hellow world!',options);

````

4. Add notification actions

代码示例：
````
//main.js
var options = {
	body:'First notification!',
	actions:[
      {action:'explore',title:'Go to the site', icon:'img/check.png'},
      {action:'close', title:'No thank you', icon:'img/x.png'},
	]
};
reg.showNotification('Hello world',options);

````


### Notification interactions
client 端service worker

Two notification events you can listen for in a service worker:
- notificationclose

示例代码：
````
//service-worker.js
 self.addEventListener('notificationclose',function(event){
     var notification = event.notification;
     var primaryKey = notification.data.primaryKey;
     console.log('Closed notification:'+ primaryKey);
 });

````

- notificationclick

代码示例：
````
//service-worker.js
self.addEventListener('notificationclick',function(event){
	   var notification = event.notification;
	   var action = event.action;
	   if(action === 'close'){
	   	   notification.close();
	   	}else {
           client.openWindow('http://www.example.com');
	   	}
});

````


注意：不是所有的浏览器都能实现notification API 到相同的水平，另外操作系统也可能不支持通知的same features。

因此为了创建一个一直的体验，需要：
1. Check for Support

代码：
````

//main.js
if('Notification' in window && navigator.serviceWorker){
  //Display the UI to let the user toggle notifications
}

````

当用户浏览器不支持Notifications API时，可以：
   - Offer a simple inline “notification” on your web page.This works well when the user has the page open.
   - Integrate with another service, such as an SMS provider or email provider timely alerts to the user.

2. Check for permission

保持校验当前的permission已经是granted的状态是很重要的，因为这个状态有可能改变

代码：
````
//main.js
if(Notification.permission === 'granted'){
  /* do our magic*/
}else if(Notification.permission === 'blocked'){
   /* the user has previously denied push.Can't reprompt */
}else {
	/* show a prompt to the user */
}



````

3. 注意平台间的差异性

动作按钮和图像在平台上差别很大。例如，一些OSs可以显示有限数量的动作，而其他的可能不允许用户直接可见动作。

你可以调用Notification.maxActions来检查可能展示的动作按钮的最大数量。当你创建notification的时候需要检查这个，以适应动作按钮的最大数量。 在service worker文件中的noticationclick的handler也要检查这个以便于决定正确的response.



### Sending and Receiving Push Notifications

它工作的流程是：
1. In the app’s main JavaScript, call pushManager.subscribe() on the service worker registration object.
2. Get the subscription object and convert it to JSON. Get the endpoint URL and public key and save this to your server (for example, by using a fetch request).
3. Send the message payload from your server to the endpoint URL, encrypted with the public key.
4. The push message raises a “push” event in the service worker which we can handle in a push event handler.
5. In push event handler we get the data from the message and display a notification

### Push API

首先我们需要检查用户是否已经subscribed 和update 相应的the page UI。如果他们没有被subscribed,促使他们去subscribe。 如果他们已经被subscribed, 更新 server 用最新的subscription.

当用户赋予在你的site上的push的权限， 你subscrible browser的push service。 这会创建一个特殊的subsciption object ,这个object 包含了 the push service的 "endpoint URL"(这个值在每个浏览器上是不同)， 以及 public key。

我们为当前用户发送 这个 subscription object 到server上并保存它。

check if user is subscribed的代码如下：
````
navigator.serviceWorker.ready.then(function(reg){
    reg.pushManager.getSubscription().then(function(sub){
        if(sub == undefined){
             // ask user to register for push
        }else {
        	// you have subscription, update the database on your server
        }
    });
});

````

Subscribe to the push service的代码如下：
````
navigator.serviceWorker.getRegistration().then(function(reg){
    reg.pushManager.subscribe({
       userVisibleOnly:true
    }).then(function(subscription){
    	// send subscription.toJSON() to server
    });
});

````

The subscription object 的代码格式如下：
````
{
    "endpoint": "https://fcm.googleapis.com/fcm/send/f1LsxkKp...",
    "keys": {
        "p256dh": "BLc4xRzKlKORKWlbdgFaB1oEKgPpWC5cW8OCzVrOQRv-1n ...",
        "auth": "5I2Bu2oKdyy9CwL8QVF0NQ=="
    }
}

````

### Send a push message from the server

server生成一个message ,并用public key 加密，然后将它发送给endpoint URL 在
subscription object中。  这个URL 包含了push service的地址以及一个subscription ID(允许 push service 去验证接收message的客户端)。 这个消息在push service上接收，然后分发给正确的客户端。


send a message from the server的代码如下：
````
var webPush = require('web-push');
var payload = 'Here is a payload!';
var options = {
	TTL:60,// max time in seconds for push service to retry delivery
};
webPush.sendNotification(pushSubscription,payload,options);

````

你需要一种方式来保证user和server之间、 你的server和push service之间、 push service之间的安全通信。VAPID就是一种方案。

这个VAPID 识别信息可以被push service使用去将 相同的应用server发送的请求归于a single entity。 

VAPID介绍：
- Voluntary Application Server identification for Web Push(VAPID) protocol is an optional method to identify your service
- VAPID uses JSON Web Tokens(JWT) to carry identifying information
- A JWT contains  a three properties called a Claim. The claim has:
Audience attribute 、 Subscriber property 、 Expiration time value。


为了使用VAPID,我们需要生成 public/private key pair ，然后用 public key去 订阅push service. 这个public key 必须首先有 URL base64 转为 a Unit8Array.

“web-push” 库提供了一个方法generateVapidKeys 用来生成keys的。命令行：
```web-push generate-vapid-keys [--json] ```



Subscribe with the VAPID public key代码如下：
````
var applicationServerPublicKey = 'BLiZBfZJTwbWe_TzKaKuiT8GHqmcFU';
var applicationServerKey = urlB64ToUnit8Array(applicationServerPublicKey);

swRegistration.pushManager.subscribe({
   userVisibleOnly:true,
   applicationServerKey:applicationServerKey 
});

````


Send a message with VAPID代码如下：
````
var webPush = require('web-push');
var payload = 'Here is a payload!';
var options = {
	vapidDetails:{
		subject:'mailto:example-email@example.com',
		publicKey:vapidPublicKey,
		privateKey:vapidPrivateKey
	}
};

webPush.sendNotification(pushSubscription,payload,options);


````


下面是发送和接收push message以及展示push notification的总结：

客户端：
   1. Subscribe to the push service
   2. Send the subscription object to the server

服务端：
   1. Generate the data that we want to send to the user
   2. Encrypt the data with the user public key
   3. Send the data to the endpoint URL with a payload of encrypted data

这个消息会被路由到 user's device. 这个会激活 the browser 找到正确的service worker 然后触发 ‘push’ event.  

客户端：
   1. Receive the message data(if there is any) in the "push" event
   2. Perform some custom logic in the push event
   3. Show a notification


另外 push notification 需要翻墙

