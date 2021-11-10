## 路由请求Route Requests
Workbox中的路由是router 将请求匹配到route, 然后route处理该请求

### 使用workbox-routing来匹配request有三种方式：
- 字符串
如：
````
import {registerRoute} from 'workbox-routing';

registerRoute(
  '/logo.png',
  handler
); // 匹配同源的图片

registerRoute(
  'https://some-other-origin.com/logo.png',
  handler
); // 匹配非同源的图片

````
- 正则表达式
如：
````
import {registerRoute} from 'workbox-routing';

registerRoute(
  new RegExp('\\.js$'),
  jsHandler
);

registerRoute(
  new RegExp('\\.css$'),
  cssHandler
);


````

- 回调callback
如：
````
import {registerRoute} from 'workbox-routing';

const matchFunction = ({url, request, event}) => {
  return url.href === 'https://example.com/app.js';
};

registerRoute(
  matchFunction,
  handler
);


````



### 处理匹配的请求的方式有两种：
- 使用workbox-strategies提供的workbox的策略
- 提供一个回调函数， 这个回调函数返回一个resolve to a response的promise

#### 使用workbox策略处理route

内置的缓存策略有：
- Stale While Revalidate
  如果请求可用， 此策略将使用缓存的响应， 并使用网络的响应在后台更新缓存。（如果未缓存， 它将等待网络响应并使用）。 这是一个相当安全的策略， 因为这意味着用户会定期更新其缓存。此策略的缺点是， 他总会用尽用户的带宽来向网络请求资产。
- Network First
  优先尝试从网络获得响应。 如果收到响应，将它传递给浏览器， 并将其保存在缓存中。 如果网络请求失败， 将使用最后一个缓存的响应。
  
- Cache First
  优先检查缓存是否有响应， 如果有响应， 则使用缓存； 如果请求不在缓存中， 则使用网络， 并将任何有效的响应添加缓存中， 然后再传递给浏览器

- Network Only
  强制响应只来自网络

- Cache Only
  强制响应只来自缓存

例如：
````
import {registerRoute} from 'workbox-routing';
import * as strategies from 'workbox-strategies';

registerRoute(
  match,
  new strategies.StaleWhileRevalidate()
);

registerRoute(
  match,
  new strategies.NetworkFirst()
);

registerRoute(
  match,
  new strategies.CacheFirst()
);

registerRoute(
  match,
  new strategies.NetworkOnly()
);

registerRoute(
  match,
  new strategies.CacheOnly()
);

````


#### 使用回调函数处理匹配的route

在某些情况下，您可能希望使用自己的不同策略来响应请求，或者只是通过模板在服务工作者中生成请求。 为此，您可以提供一个异步函数，该函数返回Response对象。 将使用包含url和event（FetchEvent）属性的参数对象来调用它。

例如：
````
import {registerRoute} from 'workbox-routing';

const handler = async ({url, event}) => {
  return new Response(`Custom handler response.`);
};

registerRoute(match, handler);

````

* 注意：如果你在匹配的回调函数中返回一个value, 它将作为一个参数传给handler callback， 如：
````
import {registerRoute} from 'workbox-routing';

const match = ({url, event}) => {
  if (url.pathname === '/example') {
    return {
      name: 'Workbox',
      type: 'guide',
    };
  }
};

const handler = async ({url, event, params}) => {
   // Response will be "A guide to Workbox"
  return new Response(
    `A ${params.type} to ${params.name}`
  );
};

registerRoute(match, handler);

````