## [WorkBox官网](https://developers.google.com/web/tools/workbox)

介绍： Workbox是一套库， 可以为PWA的生产就绪service worker提供支持

Workbox的优势：
- 预缓存
- 运行时缓存
- 策略
- 请求路由
- 后台同步
- 有用的调试

### Workbox功能
Workbox的主要功能：
- 路由和缓存策略


### Workbox功能之路由和缓存策略

Workbox允许您使用不同的缓存策略来管理Web应用程序的HTTP请求的缓存。第一步是确定正在处理的请求是否符合您的条件， 如果符合， 则对他应用缓存策略。匹配通过返回真实值的回调函数进行。缓存策略可以是Workbox的预定义策略之一， 也可以创建自己的策略。利用路由和缓存的基本service worker可能看起来像这样：
````
import { registerRoute } from 'workbox-routing';
import {
  NetworkFirst,
  StaleWhileRevalidate,
  CacheFirst,
} from 'workbox-strategies';

// Used for filtering matches based on status code, header, or both
import { CacheableResponsePlugin } from 'workbox-cacheable-response';
// Used to limit entries in cache, remove entries after a certain period of time
import { ExpirationPlugin } from 'workbox-expiration';

// Cache page navigations (html) with a Network First strategy
registerRoute(
  // Check to see if the request is a navigation to a new page
  ({ request }) => request.mode === 'navigate',
  // Use a Network First caching strategy
  new NetworkFirst({
    // Put all cached files in a cache named 'pages'
    cacheName: 'pages',
    plugins: [
      // Ensure that only requests that result in a 200 status are cached
      new CacheableResponsePlugin({
        statuses: [200],
      }),
    ],
  }),
);

// Cache CSS, JS, and Web Worker requests with a Stale While Revalidate strategy
registerRoute(
  // Check to see if the request's destination is style for stylesheets, script for JavaScript, or worker for web worker
  ({ request }) =>
    request.destination === 'style' ||
    request.destination === 'script' ||
    request.destination === 'worker',
  // Use a Stale While Revalidate caching strategy
  new StaleWhileRevalidate({
    // Put all cached files in a cache named 'assets'
    cacheName: 'assets',
    plugins: [
      // Ensure that only requests that result in a 200 status are cached
      new CacheableResponsePlugin({
        statuses: [200],
      }),
    ],
  }),
);

// Cache images with a Cache First strategy
registerRoute(
  // Check to see if the request's destination is style for an image
  ({ request }) => request.destination === 'image',
  // Use a Cache First caching strategy
  new CacheFirst({
    // Put all cached files in a cache named 'images'
    cacheName: 'images',
    plugins: [
      // Ensure that only requests that result in a 200 status are cached
      new CacheableResponsePlugin({
        statuses: [200],
      }),
      // Don't cache more than 50 items, and expire them after 30 days
      new ExpirationPlugin({
        maxEntries: 50,
        maxAgeSeconds: 60 * 60 * 24 * 30, // 30 Days
      }),
    ],
  }),
);

````
上述例子中该service worker使用network first策略将导航请求（用于新的HTML页面）缓存在名为页面的缓存中， 但前提是它返回200状态代码。它也使用“过时的重新验证”策略来缓存样式表， javascript和web worker， 仅当它们返回200状态代码时，该策略才能将缓存的资产存储在名为资产的缓存中。 最终，它使用“缓存优先”策略缓存图像，仅当缓存的图像带有200状态代码时才将其存储在名为“ images”的缓存中，并且在30天后过期的缓存项仅一次允许50个条目。


