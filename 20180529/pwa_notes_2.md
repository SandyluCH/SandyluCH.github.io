[Progressive Web Apps Training](https://developers.google.com/web/ilt/pwa/)

[Cache API](https://developer.mozilla.org/en-US/docs/Web/API/Cache)

## CacheStorage
   
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

2. addAll()

3. delete()

4. keys()

5. match()

6. matchAll()

7. put()

### Events事件






[Service Worker API](https://developers.google.com/web/ilt/pwa/lab-caching-files-with-service-worker)