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




## Service Worker API
[Service Worker API](https://developers.google.com/web/ilt/pwa/lab-caching-files-with-service-worker)

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



## Introduction to Push Notifications

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
 self.addEventListener('notificationclose',function(event){
     var notification = event.notification;
     var primaryKey = notification.data.primaryKey;
     console.log('Closed notification:'+ primaryKey);
 });

````

- notificationclick