## workbox插件

### 工具箱插件
workbox提供插件有：
- BackgroundSyncPlugin：如果网络请求失败，则将其添加到后台同步队列中，并在触发下一个同步事件时重试该请求。
- BroadcastUpdatePlugin：每当更新缓存时，就在广播频道上或通过发送消息 postMessage()。
- CacheableResponsePlugin：仅缓存满足特定条件的请求。
- ExpirationPlugin：管理缓存中项目的数量和最长期限。
- RangeRequestsPlugin：响应包含Range:标头的请求，该标头包含来自缓存的部分内容。

通过将实例添加到plugins属性，可以将这些插件与Workbox策略结合使用：
````
import {registerRoute} from 'workbox-routing';
import {CacheFirst} from 'workbox-strategies';
import {ExpirationPlugin} from 'workbox-expiration';

registerRoute(
  ({request}) => request.destination === 'image',
  new CacheFirst({
    cacheName: 'images',
    plugins: [
      new ExpirationPlugin({
        maxEntries: 60,
        maxAgeSeconds: 30 * 24 * 60 * 60, // 30 Days
      }),
    ],
  }),
);

````

### 自定义插件
您可以通过传入具有以下任何一种方法的对象来创建自己的插件：
- cacheWillUpdate：在Response 用于更新缓存之前调用。您可以在将响应添加到缓存之前对其进行更改，或者返回null以避免完全更新缓存。

- cacheDidUpdate：在将新条目添加到缓存或更新现有条目时调用。如果您希望在缓存更新后执行操作，则很有用。

- cacheKeyWillBeUsed：在将请求用作高速缓存键之前调用，同时针对高速缓存查找（时mode为'read'）和高速缓存写入（时mode 为'write'）。如果在使用URL进行缓存访问之前需要覆盖URL或对其进行规范化，这可能会派上用场。

- cachedResponseWillBeUsed：在使用缓存之前，调用此回调，此回调使您可以检查该响应，并可能返回null或使用其他响应。

- requestWillFetch：每当要发出网络请求时都会调用此方法。您可以 在此回调中更改请求。

- fetchDidFail：在网络请求失败时调用，很可能是由于引起的 NetworkError。请注意，当从网络返回带有错误状态的响应（如）时，不会调用此方法404 Not Found。

- fetchDidSucceed：在网络请求成功时调用，无论响应的HTTP状态如何。

- handlerWillStart：在任何处理程序逻辑开始运行之前调用。此回调可用于设置初始处理程序状态（例如，记录开始时间）。

- handlerWillRespond：在strategyhandle()方法返回响应之前调用。此回调可用于在将该响应返回到路由处理程序或其他自定义逻辑之前修改该响应。

- handlerDidRespond：在策略的handle()方法返回响应之后调用。此回调可用于记录任何最终响应详细信息，例如在其他插件进行更改之后。

- handlerDidComplete：在调用此策略后，添加到事件的所有延长生存期的诺言已实现后才调用。此回调可用于报告需要等待处理程序完成以进行计算的任何数据（例如，缓存命中状态，缓存延迟，网络延迟）。

- handlerDidError：如果处理程序无法从任何来源提供有效的响应，则调用此方法。此回调可用于提供“后备”内容，以替代网络错误。

await每当缓存或获取事件到达回调的相关点时，将使用所有这些函数。

使用所有这些回调的插件如下所示：

````
const myPlugin = {
  cacheWillUpdate: async ({request, response, event, state}) => {
    // Return `response`, a different `Response` object, or `null`.
    return response;
  },
  cacheDidUpdate: async ({cacheName, request, oldResponse, newResponse, event, state}) => {
    // No return expected
    // Note: `newResponse.bodyUsed` is `true` when this is called,
    // meaning the body has already been read. If you need access to
    // the body of the fresh response, use a technique like:
    // const freshResponse = await caches.match(request, {cacheName});
  },
  cacheKeyWillBeUsed: async ({request, mode, params, event, state}) => {
    // `request` is the `Request` object that would otherwise be used as the cache key.
    // `mode` is either 'read' or 'write'.
    // Return either a string, or a `Request` whose `url` property will be used as the cache key.
    // Returning the original `request` will make this a no-op.
    return request;
  },
  cachedResponseWillBeUsed: async ({cacheName, request, matchOptions, cachedResponse, event, state}) => {
    // Return `cachedResponse`, a different `Response` object, or null.
    return cachedResponse;
  },
  requestWillFetch: async ({request, event, state}) => {
    // Return `request` or a different `Request` object.
    return request;
  },
  fetchDidFail: async ({originalRequest, request, error, event, state}) => {
    // No return expected.
    // NOTE: `originalRequest` is the browser's request, `request` is the
    // request after being passed through plugins with
    // `requestWillFetch` callbacks, and `error` is the exception that caused
    // the underlying `fetch()` to fail.
  },
  fetchDidSucceed: async ({request, response, event, state}) => {
    // Return `response` to use the network response as-is,
    // or alternatively create and return a new `Response` object.
    return response;
  },
  handlerWillStart: async ({request, event, state}) => {
    // No return expected. 
    // Can set initial handler state here.
  },
  handlerWillRespond: async ({request, response, event, state}) => {
    // Return `response` or a different `Response` object.
    return response;
  },
  handlerDidRespond: async ({request, response, event, state}) => {
    // No return expected. 
    // Can record final response details here.
  },
  handlerDidComplete: async ({request, response, error, event, state}) => {
    // No return expected. 
    // Can report any data here.
  },
  handlerDidError: async ({request, event, error, state}) => {
    // Return `response`, a different `Response` object as a fallback, or `null`.
    return response;
  }
};

````
* 注意：所有以生命开头的生命周期回调handler都是Workbox v6中的新增功能。如果要实施自己的自定义策略，则不必担心自己调用这些回调中的任何一个。这全部由Strategy基类处理 。

event传递给上述每个插件回调的对象代表触发提取或缓存操作的原始事件。在某些情况下，不会发生原始事件，因此您的代码应在引用之前检查其是否存在。此外，在调用时 handle()的策略的方法，将event 传递给handle()将传递给插件回调的事件。

所有插件回调也会传递一个state对象，该对象对于特定插件对象和策略调用（即）的调用是唯一的handle()。这样就可以编写插件，其中一个回调可以根据同一个插件中另一个回调所做的事情有条件地执行某项操作（例如，计算运行requestWillFetch()和之间的时间差fetchDidSucceed() 或fetchDidFail()）。

### 第三方插件
开发人员以创造性的方式参与Workbox的生命周期事件，并将您的自定义插件作为模块发布。