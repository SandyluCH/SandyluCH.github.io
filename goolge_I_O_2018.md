### [Flutter Beta3新特性](https://juejin.im/entry/5af3887b6fb9a07ace58df5e)
### [2018 web](https://juejin.im/post/5af2a6e1f265da0b9c109f8a)
####  [Service Worker（服务工作线程）](https://developers.google.com/web/fundamentals/primers/service-workers/)    


#### PWA渐进式应用Progressive Web Apps

[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)

   优点：
   
   + (1). Responsive : to fit any form factor
   + (2). Connectivity independent : 使用service workers 逐步提升让他们可以离线工作
   + (3). App-like-interactions ： Adopt a Shell + Content application model to create appy navigations & interactions
   + (4). Fresh ： 随着service worker 更新进程，它总是最新的 
   + (5). Safe安全：  由TLS(a service worker requirement)来提供服务 从而阻止嗅探
   + (6). Discoverable ：Are identifiable as “applications” thanks to W3C Manifests and Service Worker registration scope allowing search engines to find them
   + (7). Re-engageable:Can access the re-engagement UIs of the OS; e.g. Push Notifications
   + (8). Installable: to the home screen through browser-provided prompts, allowing users to “keep” apps they find most useful without the hassle of an app store
   + (9).Linkable: meaning they’re zero-friction, zero-install, and easy to share. The social power of URLs matters.
   








#### Web Media
   
   	Media Session API 允许页面为标准媒体交互提供自定义行为。

   	vimeo 通过这个接口实现了锁屏时暂停播放视频。

	Media Capabilities API 使得浏览器能够自动根据设备状况选择视频流的来源。

	Picture-in-Picture Support 使得视频能够在显示屏的任意位置播放。

	Presentation API 能够更加精确的控制在第二显示屏上展示的内容。

	AV1 视频编码格式也将在今年应用于 chrome。

#### WebAssembly

#### Speedometer 
Speedometer 是一个衡量 Web 应用性能的浏览器基准。
