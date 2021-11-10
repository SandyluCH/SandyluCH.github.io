## 配置Workbox

### 配置缓存名

使用各种Workbox API时， 您会注意到一些缓存是自动创建的。如果不手动指定缓存名称， Workbox将使用中定义的默认名称workbox-core。 您可以看到当前的缓存名如下所示：
````
import {cacheNames} from 'workbox-core';

const precacheCacheName = cacheNames.precache;
const runtimeCacheName = cacheNames.runtime;
const googleAnalyticsCacheName = cacheNames.googleAnalytics;

````

每个缓存名称由三部分信息组成：
````
<prefix>-<cacheId>-<suffix>

````
更改默认缓存名称的方法：
````
import {setCacheNameDetails} from 'workbox-core';

setCacheNameDetails({
  prefix: 'my-app',
  suffix: 'v1'
});

````

#### 在策略中自定义缓存名称

在Workbox API的其他部分中，您可以提供cacheName 属性作为选项。例如，运行时策略 接受一个cacheName选项。在这种情况下，将完全按照您指定的方式使用缓存名称；不会使用前缀和后缀。

如果要对图像使用缓存，则可以配置如下路由：
````
import {registerRoute} from 'workbox-routing';
import {CacheFirst} from 'workbox-strategies';

registerRoute(
  ({request}) => request.destination === 'image',
  new CacheFirst({
    cacheName: 'my-image-cache',
  })
);

````

#### 在策略中自定义提取选项
当使用自定义策略进行运行时缓存时，您可能会发现需要自定义传出请求的某些方面。例如，默认情况下，请求可能不包括凭据（即cookie），但是您碰巧知道用例需要设置凭据。

要处理这种情况，可以传入一个名为fetchOptions策略构造函数的配置值，该值对应 于基础Fetch API中使用的 init选项。然后，这些选项将应用于该策略处理的所有传出请求。

例如，为确保与给定第三方URL匹配的所有传出请求最终都使用“ include”的凭据模式，您可以设置以下路由：
````
import {registerRoute} from 'workbox-routing';
import {NetworkFirst} from 'workbox-strategies';

registerRoute(
  ({url}) => url.origin === 'https://third-party.example.com',
  new NetworkFirst({
    fetchOptions: {
      credentials: 'include',
    },
  })
);

````

[fetch api完整文档](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#parameters)

#### 配置调试版本与生产版本
对于每个workbox库， 都有两个版本：一个用于开发， 一个用于生产。
- 调试版本： 精简版， 具有更多日志记录， 并执行严格的断言检查以使开发尽可能容易
- 生产版本：从版本中玻璃的任何可选日志记录和断言将其最小化；


通过workbox-sw导入workbox时， 无论何时在本地主机上进行开发， 它都会自动使用开发版本，否则将使用生产版本。
也可通过以下代码来强制使用版本：
````
// Force development builds
workbox.setConfig({ debug: true });

// Force production builds
workbox.setConfig({ debug: false });

````

如果直接使用模块（通过CDN或从npm模块使用）， 则可以通过```<module>.dev.js``` 和之间更改文件扩展名来在开发和生产版本直接切换```<module>.prod.js ``` 。

使用workbox-webpack-plugin时，  输出格式将根据您的webpack配置mode选项自动确定。


#### 禁用日志记录
要禁用所有日志， 您可以在使用任何workbox api之前在service worker中奖```self.__WB_DISABLE_DEV_LOGS```变量设置为true