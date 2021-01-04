## [Electon](http://www.electronjs.org/)

## Electron基础

### Electron组成
主要由以下组成：
- Chromium用于显示网页内容
- Node.js用于本地文件系统和操作系统
- 自定义APIs用于使用经常需要的OS本机函数

用Electron开发应用程序就像构建一个带有网页界面的Node.js应用程序或构建无缝集成的网页。

### Electron进程
Electron有两种进程：
#### 主进程
主进程通过创建浏览器窗口实例来创建网页。 每一个BrowserWindow实例在其渲染过程中运行网页， 当一个BrowserWindow实例被销毁时， 对应的渲染过程也被终止。

主进程管理所有网页及其对应的渲染进程。

#### 渲染进程
渲染进程只能管理相应的网页， 一个渲染进程的崩溃不会影响其他渲染进程。

渲染进程通过IPC与主进程通信在网页上执行GUI操作。出于安全和资源可能泄露的考虑， 直接从渲染器进程中调用与本地GUI有关的API受到限制。

进程之间的通信可以通过```Inter-Process Communication(IPC) ```模块进行：```ipcMain ``` 和```ipcRenderer ```

