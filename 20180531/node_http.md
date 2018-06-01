[node中文文档](http://nodejs.cn/api/http.html#http_http);

## http

Node.js中的HTTP接口被设计成支持协议的许多特性。比如：大块编码的消息。这些接口不缓冲完整的请求或响应，用户能够以流的形式处理数据。

HTTP消息头有一个对象表示，例如：
````
{
	'content-length':'123',
	'content-type':'text/plain',
	'connection':'keep-alive',
	'host':'mysite.com',
	'accept':'*/*'
}

````

键名是小写的，键值不能修改。

为了支持各种可能的HTTP应用，Node.js的HTTP API是非常底层的，它只涉及流处理和消息解析。它把一个消息解析成消息头和消息主体，但不解析具体的消息头或消息主体。

接收到的原始消息头保存在rawHeaders属性中，它是一个[key,value,key2,value2,...]数组。

### http.Agent类

Agent负责为HTTP客户端管理连接的持续与复用。它为一个给定的主机与端口维护着一个等待请求的队列，且为每个请求重复使用一个单一的socket连接直到队列为空，此时socket会被销毁或被放入一个连接池中，在连接池中等待被有着相同主机与端口的请求再次使用。是否被销毁或被放入连接池取决于keepAlive选项。

连接池中的链接的TCP keep-alive是开启的，但服务器仍然可能关闭闲置的连接，在这种情况下，这些连接会被移出连接池，且当一个新的HTTP请求被创建时再为指定的主机与端口创建一个新的连接。 服务器也可能拒绝允许同一连接上有多个请求，在这种情况下，连接会为每个请求重新创建，且不能被放入连接池。Agent仍然会创建请求到服务器，但每个请求会出现一个新的连接。

但一个连接被客户端或服务器关闭时，它会被移出连接池。连接池中任何未被使用的socket会被释放，从而使Node.js进程在没有请求时不用保持运行。

当Agent示例不再被使用时，建议destroy()它，因为未被使用的socket也会消耗操作系统资源。

当socket触发‘close’事件或‘agentRemove’事件时，它会被移出代理。当打算长时间保持打开一个HTTP请求且不想它留在代理中，则可以做如下处理：
````
http.get(options,(res)=>{
	//处理事情
	}).on('socket', (socket)=>{
           socket.emit('agentRemove');
		});

````

代理可被用户单独的请求。使用```{agent:false}``` 作为http.get()函数或```http.request()```函数的选项，则会为客户端连接创建一个默认配置的一次性使用的Agent。
````
  http.get({
      hostname:'localhost',
      port:80,
      path:'/',
      agent:false,//创建一个新的代理，只用于本次请求
  	}, (res) =>{
       //对响应进行处理

  	})

````

#### new Agent([options])

options : 代理的配置选项，有以下字段：
  - keepAlive :<boolean>保持socket可用即使没有请求，以便他们可被将来的请求使用而无需重新建立一个TCP连接。默认为false.

  - keepAliveMsecs 	:<number>当使用了keepAlive选项时，该选项指定TCP```keep-Alive```数据包的初始延迟。当keepAlive选项为false或undefined时，该选项无效。默认为1000.

  - maxSockets : <number>每个主机允许的最大socket数量。默认为Infinity.

  - maxFreeSockets: <number> 在空闲状态下允许打开的最大的socket数量。仅当keepAlive为true时才有效。默认为256.

http.request()使用的默认http.globalAgent即全局的Agent示例。的选项均为各自的默认值。

若要配置其中任何一个，则需要创建自定义的http.Agent实例。
````
const http = require('http');
const keepAliveAgent = new http.Agent({keepAlive:true});
options.agent = keepAliveAgent;
http.request(options,onResponseCallbak);
//options.agent的默认是是undefined,对该主机和端口使用http.globalAgent; options.agent = Agent对象，表示显式地使用传入的Agent; options.agent = false, 表示创建一个新的使用默认值的Agent

````

#### agent.createConnection(options[,callback])
创建一个用于HTTP请求的socket或流。

默认情况下，该函数类似于net.createConnection()。 但是如果期望更大的灵活性，自定义的代理可以重写该方法。

   - options : <Object> 包含连接详情的选项。查看net.createConnection()了解选项的格式。
   - callback : < Function>接收被创建的socket的回调函数
   返回 ： <net.Socket>

   	socket或流可以通过以下两种方式获取：从该函数返回，或传入callback。callback参数有（err,stream）

#### agent.keepSocketAlive(socket)

#### agent.reuseSocket(socket,request)

#### agent.destroy()
销毁当前正被代理使用的任何socket

#### agent.freeSockets
返回一个对象，包含当前正在等待被启用了keepAlive的代理使用的socket数组

#### agent.getName(options)

options: <Object>为名称生成程序提供信息的选项
  - host: <string>请求发送至的服务器的域名或IP地址
  - post: <number>远程服务器的端口
  - localAddress:<string>当发送请求时，为网络连接绑定的本地接口

  返回：<string>

  为请求选项的集合获取一个唯一的名称，用来判断一个连接是否可以被复用。对于HTTP代理，返回host:post:localAddress或host:port:localAddress:family。对于HTTPS代理，名称会包含CA、证书、密码、以及其他HTTPS/TLS特有的用户判断socket复用性的选项。

#### agent.maxFreeSockets
默认为256。对于已启用keepAlive的代理，该属性可设置要保留的空闲socket的最大数量

#### agent.maxSockets
默认为不限制。该属性可设置代理为每个来源打开的并发socket的最大数量。来源是agent.getName()的返回值。

#### agent.requests
返回一个对象，包含还未被分配的socket的请求队列。

#### agent.sockets
返回一个对象，包含当前正被代理使用的socket数组。

### http.clientRequest类
该对象在http.request()内部被创建并返回。它表示着一个正在处理的请求，其请求头已进入队列。请求头仍可使用setHeader(name,value)、getHeader(name)和removeHeader(name)API进行修改。实际的请求头会与第一个数据块一起发送或当调用request.end()时发送。

要获取响应，需为‘response’事件添加一个监听器到请求对象上。当响应头被接收到时，‘response’事件会从请求对象上被触发。‘response’事件被执行时带有一个参数，该参数是一个http.IncomingMessage实例。

在‘response’事件期间，可以添加监听器到响应对象上，比如监听‘data’事件。

如果没有添加‘response’事件处理函数，则响应会被整个丢弃。如果添加了‘response’事件处理函数，则必须消耗完响应对象的数据，可以通过调用response.read()、或添加一个‘data’事件处理函数、或调用```.resume() ```。数据被消耗完时会触发‘end’事件。在数据被读取完之前会消耗内存，可能会造成‘process out of memeory’错误。 

注意： Node.js不会检查Content-Length与已传输的请求主题的长度是否相等。

该请求实现了可写流接口。它是一个包含以下事件的EventEmitter:
  1. abort事件
     当请求已被客户端终止时触发。该事件仅在首次调用abort()时触发。

  2. connect事件
     每当服务器响应connect请求时触发。如果该事件未被监听，则接收到connect方法的客户端会关闭连接。

    参数：
     - response : <http.IncomingMessage>
     - socket : <net.Socket>
     - head : <Buffer>

    示例代码：

````
 const http = require('http');
const net = require('net');
const url = require('url');

// 创建一个 HTTP 代理服务器
const proxy = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
proxy.on('connect', (req, cltSocket, head) => {
  // 连接到一个服务器
  const srvUrl = url.parse(`http://${req.url}`);
  const srvSocket = net.connect(srvUrl.port, srvUrl.hostname, () => {
    cltSocket.write('HTTP/1.1 200 Connection Established\r\n' +
                    'Proxy-agent: Node.js-Proxy\r\n' +
                    '\r\n');
    srvSocket.write(head);
    srvSocket.pipe(cltSocket);
    cltSocket.pipe(srvSocket);
  });
});

// 代理服务器正在运行
proxy.listen(1337, '127.0.0.1', () => {

  // 发送一个请求到代理服务器
  const options = {
    port: 1337,
    hostname: '127.0.0.1',
    method: 'CONNECT',
    path: 'www.google.com:80'
  };

  const req = http.request(options);
  req.end();

  req.on('connect', (res, socket, head) => {
    console.log('已连接！');

    // 通过代理服务器发送一个请求
    socket.write('GET / HTTP/1.1\r\n' +
                 'Host: www.google.com:80\r\n' +
                 'Connection: close\r\n' +
                 '\r\n');
    socket.on('data', (chunk) => {
      console.log(chunk.toString());
    });
    socket.on('end', () => {
      proxy.close();
    });
  });
});

````

  3. continue事件

  当服务器发送了一个```100 continue```的http响应时触发，通常是因为请求包含Expect:100-continue。这是客户端将要发送请求主体的指令。

  4. response事件

  当请求的响应被接收到时，该事件只触发一次。

  5. socket事件

   当socket被分配到请求后触发。

  6. upgrade事件

   每当服务器响应upgrade请求时触发。如果该事件未被监听，则接收到upgrade请求头的客户端会关闭连接。
    参数：
     - response : <http.IncomingMessage>
     - socket : <net.Socket>
     - head : <Buffer> 

#### request.abort()
标记请求为终止。调用该方法将使响应中剩余的数据被丢弃且socket被销毁。

#### request.aborted
如果请求已被终止，则该属性的值为请求被终止的时间，毫秒数，从```utc时间1970 1月1日 ```到现在的毫秒数

#### request.connection

#### request.end([data[,endcoding]][,callback])
结束发送请求。如果部分请求主题还未被发送，则会刷新它们到流中。如果请求是分块的，则会发送终止符‘0\r\n\r\n’。如果指定了data,则相当于调用request.write(data,encoding)之后再调用request.end(callbak),如果指定了callback,则当请求流结束时会被调用。

#### request.flushHeaders()
刷新请求头。

处于效率的考虑，Node.js通常会缓存请求头直到request.end()被调用或第一块数据被写入。然后Node.js会将请求头和数据打包成一个单一的TCP数据包。

如果第一个数据块很长时间之后才被发送，request.flushHeaders()可以绕过最优选择并提前开始请求。

#### request.getHeader(name)
参数：name :<string>
返回:Returns :<string>

读出请求头，注意：参数name是大小写敏感的。
例如：
````
const contentType = request.getHeader('Content-Type');

````	

#### request.maxHeadersCount
默认为2000， 如果设为0， 就是没有限制

#### request.removeHeader(name)
 参数 ： name: <string>

移除一个已经在headers对象里面的header

````
 request.removeHeader('Content-Type');

````

#### request.setHeader(name,value)
参数：
  - name: <string>
  - value: <string>
为headers对象设置一个单一的header值。如果该header已经存在了，则将会被替换。这里使用一个字符串数组来设置相同名称的多个headers.
示例：
````
request.setHeader('Content-Type','application/json');

或

request.setHeader('Set-Cookie',['type=ninja','language=javascript']);

````

#### request.setNoDelay([noDelay])
参数：
  - noDelay:<boolean>
一旦socket被分配给请求且已连接，socket.setNoDelay()会被调用

#### request.setSocketKeepAlive([enable][,initalDelay])  	
参数：
  - enable:<boolean>
  - initialDelay:<number>
  
一旦socket被分配给请求且已连接，socket.setKeepAlive()会被调用

#### request.setTimeout(timeout[,callback])
参数：
  - timeout :<number>请求超时的好描述
  - callback:<Function>可选的函数，当超时发生时被调用。 等同于绑定到timeout事件。
一旦socket被分配给请求且已连接，socket.setTimeout()会被调用

返回request

#### reqeust.socket
引用底层socket。通常用户不想访问此属性。特别地，由于协议解析器连接到socket的方式，socket将
不会触发‘readable’事件。在response.end()之后，该属性为null。也可以通过request.connection
来访问socket。

示例代码：
````
const http = require('http');
const options = {
	host:'nodejs.cn'
};
const req = http.get(options);
req.end();
req.once('response',(res)=>{
   const ip = req.socket.localAddress;
   const port = req.socket.localPort;
   console.log(`你的IP地址是${ip},你的源端口是${port}。`);   
});


````

#### request.write(chunk[,encoding][,callback])
参数：
   - chunk : <string> 或 <Buffer>
   - endcoding:<string>
   - callback :<Function>
发送请求主体的一个数据块。通过多次调用该方法，一个请求主体可被发送到一个服务器，在这种情况下，当创建请求时，建议使用```['Transfer-Encoding','chunked'] ```请求头。

encoding参数是可选的，仅当chunk是一个字符串时才有效。默认为‘utf8’.

callback参数是可选的，当数据块被刷新时调用。

返回request

### http.Server类
该类继承自net.Server,且具有以下额外的事件


