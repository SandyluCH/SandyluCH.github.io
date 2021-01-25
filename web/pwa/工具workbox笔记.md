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



### workbox使用-Precaching
除了在发出请求时进行缓存（有时称为运行时缓存）之外，Workbox还支持预缓存， 即在安装service worker时缓存资源的功能。 有许多非常适合预缓存的项目： Web应用程序的起始URL, 脱机后备页面以及关键的JavaScript和CSS文件。通过预缓存文件， 可以保证服务工作者控制页面时它们在缓存中可用。

您可以通过使用支持预缓存清单注入的捆绑程序插件（Webpack或 rollup）在新的service worker中使用预缓存：
````
import { precacheAndRoute } from 'workbox-precaching';

// Use with precache injection
precacheAndRoute(self.__WB_MANIFEST);

````

### workbox使用-Offline Fallback
是网站和应用在脱机工作时感觉更健壮的一种常见模式是提供备用页(fallback page),而不是现实浏览器默认的错误页面。 使用Workbox路由和预缓存， 您只需几行代码即可设置此模式：
````
import { precacheAndRoute, matchPrecache } from 'workbox-precaching';
import { setCatchHandler } from 'workbox-routing';

// Ensure your build step is configured to include /offline.html as part of your precache manifest.
precacheAndRoute(self.__WB_MANIFEST);

// Catch routing errors, like if the user is offline
setCatchHandler(async ({ event }) => {
  // Return the precached offline page if a document is being requested
  if (event.request.destination === 'document') {
    return matchPrecache('/offline.html');
  }

  return Response.error();
});

````
该服务工作者预缓存一个脱机页面， 如果用户处于脱机状态， 则返回预缓存的脱机页面的内容，而不产生浏览器错误。


### 使用bundlers和Workbox

#### 创建一个service worker bundle

- 一、选择一个bundler
  比较流行的bundler有： webpack, rollup, Parcel
- 二、编写你的service worker 代码
````
  // These JavaScript module imports need to be bundled:
import {precacheAndRoute} from 'workbox-precaching';
import {registerRoute} from 'workbox-routing';
import {CacheFirst} from 'workbox-strategies';

// Use the imported Workbox libraries to implement caching,
// routing, and other logic:
precacheAndRoute(self.__WB_MANIFEST);
registerRoute(
  ({request}) => request.destination === 'image',
  new CacheFirst({cacheName: 'images'}),
);

// Etc.

````
上述例子中代码在浏览器运行前需要在bundler中先处理
- 三、为开发模式或生产模式的build， 对你的bundler进行配置


#### 可选： 注入预缓存清单
一些开发人员使用Workbox进行预缓存， 这会在安装Service Worker期间将条目添加到缓存中。 如果使用预缓存， Workbox需要URL和修订信息的特殊格式列表， 以确定要缓存的内容。此列表称为预缓存列表。

预缓存清单通常在构建过程中生成， 然后交换到服务工作者代码中。惯例是使用符号self.__WB_MAINFEST作为清单注入位置的占位符。

self.__WB_MAINFEST从技术上讲，替换预缓存清单不是bundler的一部分，但是bundler通常是在较大的构建过程中进行。通常都希望bundler service worker并使用相同的构建工具进行预缓存清单注入

注入方法如下：
- Webpack设置
 如workbox-webpack-plugin插件， 该插件InjectMainfest插件可以为您处理bundler 和inject预缓存清单。 使用此插件时， 不需要将service worker的源代码配置为单独的条目
- rollup设置
 rollup-plugin-workbox-inject为您处理预缓存清单注入步骤，并假定您已经配置了汇总以捆绑service worker源文件
- cli设置
  可以使用workbox-cli, 让其在injectMainfest模式下运行，而不使用bundler程序插件。  如果使用这种方法， 则应在inejctMainfest每次成功构建后立即运行该步骤， 并将新捆绑的service worker文件作为swSrc选项传递





#### GenerateSW为您处理捆绑

如果你使用generateSW任何一种模式workbox-build或者workbox-cli，或者GenerateSW在workbox-webpack-plugin，那么你就需要配置捆绑。这些工具将自动为您生成可用于浏览器的服务工作者。在后台，根据您提供的声明性配置，汇总会自动用于生成最终捆绑包。