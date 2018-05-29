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
