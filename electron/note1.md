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


### 添加功能

#### [通知Notifications](http://www.electronjs.org/docs/tutorial/notifications)

windows,Linux，macOS 这三个操作系统都为应用程序向用户发送通知提供了手段。 在主进程和渲染进程中， 显示通知的技术不同。

为了渲染程序，Electron方便地允许开发者使用HTML5通知API发送通知， 使用当前运行中的系统的原生通知API以显示他


#### [Recent Documents 最近文档](http://www.electronjs.org/docs/tutorial/recent-documents)

#### [任务栏的进度条](http://www.electronjs.org/docs/tutorial/progress-bar)

#### 自定义Dock菜单

#### 自定义Window任务栏

#### 自定义Linux桌面动作

#### 键盘快捷键

- 本地快捷键
应用键盘快捷键仅在应用程序被聚焦时触发。

- 全局快捷键
要配置全局键盘快捷方式， 即使应用程序没有键盘焦点， 也需要使用globalShortcut模块来检测键盘事件。


##### 在浏览器窗口内的快捷方式
- 使用web APIs 
如果你想要在BrowserWindow中处理键盘快捷键， 你可以在渲染进程中使用addEventListener() API来监听```keyup ``` 和```keydown ``` DOM事件。

```` 
window.addEventListener('keyup', doSomething, true)

````
* 注意， 第三个参数true表示监听器总是在其他监听器之前接收按键， 因此不能对它们调用stopPropagation()。

- 拦截主进程中的事件
在调度页面中的```keydown ```和```keyup ```事件之前，会发出```before-input-event ```事件。他可以用于捕获和处理在菜单中不可见的自定义快捷方式。
示例：
````
const { app, BrowserWindow } = require('electron')

app.whenReady().then(() => {
  const win = new BrowserWindow({ width: 800, height: 600, webPreferences: { nodeIntegration: true } })

  win.loadFile('index.html')
  win.webContents.on('before-input-event', (event, input) => {
    if (input.control && input.key.toLowerCase() === 'i') {
      console.log('Pressed Control+I')
      event.preventDefault()
    }
  })
})


````

- 使用第三方库
如果不想手动进行快捷键解析， 可以使用一些库来进行高级的按键检测。例如mousetrap



#### 离线/在线侦测

#### 针对macOS系统BrowserWindows的展示文件

#### 原生文件拖放

#### 离屏渲染

#### 暗黑模式

#### 在Electron中嵌入网页

如果你想在electron BrowserWindow中嵌入第三方web内容， 那么这有三个选择提供给你：
<iframe>、 <webview>和BrowserViews.

- iframe
iframe在electron中的行为与普通浏览器中类似。页面中的<iframe>元素可以显示外部网页，前提是其内容安全策略允许。要限制<iframe>标记中站点的功能数量，建议使用sandbox属性，并且只允许您想要支持的功能。

- WebView

* 我们不建议你使用WebViews, 因为不稳定， 建议考虑使用其他方法， 比如iframe和Electron的BrowserView, 或者通过设计避免嵌入内容的架构


- BrowserView
BrowserViews不是DOM的一部分，而是在主进程中创建并由主进程控制的。它们只是现有窗口上的另一层web内容。这意味着它们完全独立于您自己的BrowserWindow内容，并且它们的位置不受DOM或CSS控制。相反，它是通过在主进程中设置边界来控制的。



BrowserViews提供对其内容的最大控制，因为它们实现webcontent的方式与BrowserWindow类似。但是，由于BrowserViews不是DOM的一部分，而是覆盖在它们上面，因此必须手动管理它们的位置。
