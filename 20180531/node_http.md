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
该类继承自net.Server,且具有以下额外的事件:
  1. checkContinue事件

  参数：
   - request: <http.IncomingMessage>
   - response: <http.ServerResponse>
  每当接收到一个带有HTTP ```Expect:100-continue ```请求头的请求时触发。如果该事件未被监听，则服务器会自动响应```100-continue```.
  
  处理该事件时，如果客户端应该继续发送请求主题，则调用response.writeContinue(),否则生成一个适当的HTTP响应（例如：400错误请求）。

  注意： 当该事件被触发且处理后，‘request’事件不会被触发。

  2. checkExpectation事件

  参数：
    - reqeust: <http.IncomingMessage>
    - response: <http.ServerResponse>

  每当接收到一个带有HTTP Expect请求头（值不为```100-continue ```）的请求时触发。如果该事件未被监听，则服务器会自动响应```417 Expectation Failed ```。
  
  注意， 当该事件被触发且处理后，‘request’事件不会被触发。

  3. clientError事件
     如果客户端触发一个'error'事件，则它会被传递到这里。该事件的监听器负责关闭或销毁底层的socket。例如，用户可能希望更温和的用HTTP‘400 Bad Request’响应关闭socket,而不是突然地切断连接。

   默认情况下，请求异常时会立即销毁socket.
   当clientError事件发生时，不会有request或response对象，所以发送的任何HTTP响应，包括响应头和内容，必须被直接写入到socket对象。注意,确保响应是一个被正确格式化的HTTP响应消息。

  4. close事件
  
  当服务器关闭时触发

  5. connect事件

  参数:
    - request :<http.IncomingMessage> HTTP请求，同request事件 
    - socket :<net.Socket>服务器与客户端之间的网络socket
    - head :<Buffer>流的第一个数据包，可能为空

  每当客户端发送HTTP connect请求时触发。如果该事件未被监听，则发送CONNECT请求的客户端会关闭连接。

  当该事件被触发后，请求的socket上没有‘data’事件监听器，这意味着需要绑定‘data’事件监听器，用来处理socket上被发送到服务器的数据。

  6. connection事件

  参数：
   - socket : <net.Socket>

  当新的TCP流被建立时触发。socket是一个net.Socket类型的对象，通常用户无需访问该事件。注意，因为协议解析器绑定到socket的方式，socket不会触发‘readable’事件。socket也可以通过request.connection访问。
  
  7. request事件

  参数：
    - request :<http.IncomingMessage>
    - response :<http.ServerResponse>

 每次接收到一个请求时触发。注意，每个链接可能有多个请求（在HTTP keep-alive连接的情况下）

  8. upgrade事件

   参数：
      - request ：<http.IncomingMessage> http请求，同 request事件
      - socket  : <net.Socket>服务器与客户端之间的网络socket
      - head :<Buffer>流的第一个数据包，可能为空
   每当客户端发送HTTP upgrade请求时触发。如果该事件未被监听，则发送upgrade请求的客户端会关闭连接。
   
   当该事件被触发后，请求的socket上没有‘data’ 事件监听器，这意味着需要绑定‘data’事件监听器，用来处理socket上被发送到服务器的数据。

#### server.close([callback])
 停止服务端接受新的连接

#### server.listen()
开启HTTP服务器监听连接。方法与net.server的server.listen()相同。

#### server.listening
返回一个布尔值，表示服务器是否正在监听连接。
 
#### sever.maxHeadersCount
限制请求头的最大数量，默认为2000，如果设为0，则是没有限制

#### server.setTimeout([msecs][,callback])
参数：
  - msecs :<number>默认为120000（2分钟）
  - callback: <function>
设置socket的超时时间。如果发生超时，则触发服务器对象的timeout时间，并传入socket作为一个参数。

默认情况下，服务器的超时时间是2分钟，且超时后的socket会被自动销毁。但是，如果你为服务器的timeout事件分配了一个回调函数，则超时必须被显式地处理。

返回server

#### server.timeout
默认2分钟
socket被认定为超时的空闲毫秒数。值设为0 ，可禁用请求连接的超时行为。
注意：socket的超时逻辑是在连接上设定，所以改变这个值只影响服务器新建的链接，而不会影响任何已存在连接。

#### server.keepAliveTimeout
默认为5000（5秒）

服务器完成最后的响应之后需要等待的额外的传入数据的活跃毫秒数，socket才能被销毁。
如果服务器在keep-alive计时已激活时接收到新的数据，他会充值常规的非活动计时，即server.timeout

值为0时， 表示禁用传入连接的keep-alive的超时行为。

注意：socket的超时逻辑上取决于服务器连接，所以改变这个值只影响服务器的新连接，不影响任何已存在的连接。

### http.ServerResponse类

该对象在HTTP服务器内部被创建。它作为第二个参数被传入reqeust事件。
这个类是吸纳了（而不是继承自）可写流接口，它是一个有以下事件的EventEmitter:
  1. close事件

   当底层连接在response.end()被调用或能够刷新之前被终止时触发

  2. finish事件

   当响应已被发送时触发。更具体地说，当响应头和响应主题的最后一部分已被交给操作系统通过网络进行传输时，触发该事件。这并不以为着客户端已接收到任何东西。

   该事件触发后，响应对象上不在触发其他事件。


#### response.addTrailers(headers)
参数：
  - headers :<Object>
该方法会添加HTTP尾部响应头（一种在消息尾部的响应头）到响应。

仅当响应使用分块编码时，尾部响应头才会被发送；否则（比如：请求为HTTP/1.0）,尾部响应头会被丢弃。

注意，发送尾部响应头之前，需先发送Trailer响应头，并在值里戴胜尾部响应头字段的列表。例如：
````
  response.writeHead(200,{'Content-Type':'text/plain','Trailer':'Content-MD5'});
  response.write(fileData);
  response.addTrailers({'Content-MD5':'7895bf4b8828b55ceaf47747b4bca667'});
  response.end();

````

如果尾部响应头字段的名称或值包含无效字符，则抛出TypeError错误。

#### response.connection
同response.socket

#### response.end([data][,encoding][,callbak])
参数：
  - data:<string>
  - encoding:<string>
  - callback :<Function>

该方法会通知服务器，所有响应头和响应主题都已被发送，即拂去其将其视为已完成。每次响应都必须调用response.end()方法。
如果指定了data,则相当于调用response.write(data,encoding)之后再调用response.end(callback).
如果指定了callback,则当响应流结束时被调用

#### response.finished
返回一个布尔值，表示响应是否已完成。默认为false。执行response.end()之后，该值会变为true。

#### response.getHeader(name)
参数：
 - name:<string>不区分大小写
返回<string>

读取一个已入队列但尚未发送到客户端的响应头。 	

#### response.getHeaderNames()
返回一个包含当前响应唯一名称的http头信息名称数组。名称均为小写
示例：
````
response.setHeader('Foo', 'bar');
response.setHeader('Set-Cookie', ['foo=bar', 'bar=baz']);

const headerNames = response.getHeaderNames();
// headerNames === ['foo', 'set-cookie']

````

#### response.getHeaders()
返回当前响应头文件的浅拷贝。由于使用了浅拷贝，因此数组值可能会改变，无需对各种与响应头相关的http模块方法进行额外调用。返回对象的键是响应头名称，值是各自的响应头值。所以响应头名称都是小写的。

注意： response.getHeaders()方法返回的对象不会原型集成JavaScript Object。这意味着，没有定义典型的Object方法，如obj.toString(),obj.hasOwnProperty()和其他方法，并且不起作用。

例子：
````
 response.setHeader('Foo','bar');
 response.setHeader('Set-Cookie',['foo=bar','bar=baz']);
 cont headers = response.getHeaders();
 //headers === {foo:'bar','set-cookie':['foo=bar','bar=baz']}

````

#### response.hasHeader(name)
参数：
  - name : <string>
返回：
  Returns :<boolean>
如果响应头当前有设置name头部，返回true。请注意，名称匹配不区分大小写。
示例：
``` const hasContentType = response.hasHeader('content-type'); ```

#### response.headersSent
返回一个布尔值（只读）。如果响应头已被发送则为true, 否则为false。

#### response.removeHeader(name)
参数：
  - name: <string>
从隐式发送的队列中移除一个响应头。
例子：
```response.removeHeader('Content-Encoding'); ```

#### response.sendDate
当为true时，如果响应头里没有日期响应头，则日期响应头会自动生成并发送。默认为true。

该属性值可在测试时被禁用，因为HTTP响应需要包含日期响应头。

#### response.setHeader(name,value)
参数：
  - name: <string>
  - value: <string>或<string[]>
为一个隐式的响应头设置值。如果该响应头已存在，则值会被覆盖。如果要发送多个名称相同的响应头，则使用字符串数组。
例子：
```response.setHeader('Content-Type','text/html') ```
或
```response.setHeader('Set-Cookie',['type=ninja','language=javascript']) ```

如果响应头字段的名称或值包含无效字符，则抛出TypeError错误。
response.setHeader()设置的响应头会与response.writeHead()设置的响应头合并，且
response.writeHead()的优先。
````
//返回content-type = text/plain
const server = http.createServer((req,res)=>{
   res.setHeader('Content-Type','text/html');
   res.setHeader('X-Foo','bar');
   res.writeHead(200,{'Content-Type':'text/plain'});
   res.end('ok');
});

````

#### response.setTimeout(msecs,[,callback])
参数：
  - msecs: <number>
  - callback : <Function>
设置socket的超时时间为msecs。如果提供了回调函数，则它会作为监听器被添加到响应对象的‘timeout’事件。

如果没有timeout监听器被添加到请求、响应或服务器，则socket会在超时后被销毁。如果在请求、响应或服务器的timeout事件上分配了回调函数，则超时的socket必须被显式地处理。

返回response.

#### response.socket
引用底层socket。通常用户不想访问此属性。特别地，由于协议解析器连接到socket的方式，socket将不会发出readable事件。在response.end()之后，该属性为null。也可以通过response.connection来访问socket。
例如：
````
const http = require('http');
const server = http.createServer((req,res)=>{
   const ip = res.socket.remoteAddress;
   const port = res.socket.remotePort;
   res.end(`你的IP地址是${ip},你的源端口是${port}。`);
}).listen(3000);

````

#### response.statusCode
当使用隐式的响应头时（没有显式地调用response.writeHead()）,该属性控制响应头刷新时将被发送到客户端的状态码。

例子：
```response.statusCode = 404; ```

响应头被发送到客户端后，该属性表示被发出的状态码。

#### response.statusMessage
当使用隐式的响应头时（没有显式地调用response.writeHead()）,该属性控制响应头刷新时将被发送到客户端的状态信息。如果该值为undefined,则使用状态码的标准信息。
例子：
```response.statusMessage = 'Not found'; ```

#### response.write(chunk[,encoding][,callback])
参数：
  - chunk ： <string>或<Buffer>
  - encoding: <string>
  - callback: <Function>
  返回：<boolean>

如果该方法被调用且response.writeHead()没有被调用，则它会切换到隐式响应头模式并刷新隐式响应头。

该方法会发送一块响应主题。它可被多次调用，一遍提供联系的响应主体片段。

请注意在http模块中，当请求是HEAD请求时，响应主体被忽略。类似地，204和304响应不能包括
消息体。

chunk可以是一个字符串或一个buffer。如果chunk是一个字符串，则第二个参数指定如何将它编码成一个字节流。encoding默认为‘utf8’。当数据块被刷新时，callback会被调用。

注意：这是原始的HTTP主体，且与可能被使用的高级主体编码无关。

response.write()首次被调用时，会发送缓冲的响应头信息和响应主题的第一个块数据到客户端。
response.write()第二次被调用时，Node.js能够确定数据会被接收，于是开始传输新数据。也就是说，响应的完成取决于响应主体的第一块数据。

如果全部数据被成功刷新到内核缓冲区，则返回true。如果全部或部分数据还在内存中排队，则返回false。 当缓冲区再次空闲时，则触发drain事件。

#### response.writeContinue()
发送一个HTTP/1.1 100 Continue消息到客户端，表示请求主题可以开始发送。 
同server的‘checkContinue’事件。

#### response.writeHead(statusCode[,statusMessage][,headers])
发送一个响应头给请求。状态码是一个三位数的HTTP状态码，如404。 最后一个参数headers是响应头。
第二个参数statusMessage是可选的状态描述。

例子：
````
const body = 'hello world';
response.writeHead(200,{
  'Content-Length':Buffer.byteLength(body),
  'Content-Type':'text/plain'
  });

````

该方法在消息中只能被调用一次，且必须在response.end()被调用之前调用。

如果在调用该方法之前调用response.write()或response.end()， 则隐式的响应头会被处理并调用该函数。

response.setHeader()设置的响应头会与response.writeHead()设置的响应头合并，
且response.writeHead()的优先。
````
//返回content-type = text/plain
const server = http.createServer((req,res)=>{
  res.setHeader('Content-Type','text/html');
  res.setHeader('X-Foo','bar');
  res.writeHead(200,{'Content-Type':'text/plain'});
  res.end('ok');
});

````

注意，Content-Length是以字节（而不是字符）为单位的。上面的例子行的通是因为
字符串‘hello world’只包含单字节字符。如果响应主题包含高级编码的字符，则应使
用Buffer.byteLength()来确定在给定编码中的字节数。Node.js不会检查Content-Length
与已发送的响应主题的长度是否相同。

如果响应头字段的名称或值包含无效字符，则抛出TypeError错误。

#### response.writeProcessing()

### http.IncomingMessage类

IncomingMessage对象由http.Server或http.clientRequest创建，并作为第一个参数分别传递给
‘request’和‘response’事件。它可以用来访问响应状态、消息头、以及数据。

它实现了可读流接口，还有以下额外的事件、方法以及属性。
  1. aborted事件

  当请求已被终止且网络socket已关闭时触发。

  2. close事件

  当底层连接被关闭时触发。通end事件一样，该事件每个响应只触发一次。

#### message.aborted
如果请求已经被aborted， 那么message.aborted属性的值将为true.

#### message.destroy([error])
调用接收到IncomingMessage的socket上的destroy()方法。如果提供了error,则触发error事件，且把error作为参数传入事件的监听器。

#### message.headers
请求头或响应头的对象。
头信息的名称与值的键值对。头信息的名称为小写。
原始头信息中的重复数据会按一下方式根据头信息名称进行处理：
  - 重复的age 、 authorization 、 content-length 、 content-type 、 etag 、 expires 、 from 、 host 、 if-modified-since \ if-unmodified-since \ last-modified \ location
  \ max-forwards \ proxy-authorization \ referer\ retry-after \或user-agent会被丢弃。
  - set-cookie始终是一个数组。重复的会被添加到数组。
  - 对于其他头信息，其值使用','拼接。

#### message.httpVersion
在服务器其你去中，该属性返回客户端发送HTTP版本。在客户端响应中，该属性返回连接到的服务器的
HTTP版本。可能的值有‘1.1’ 或‘1.0’。

message.httpVersionMajor返回HTTP版本的第一个整数值，message.httpVersionMinor返回HTTP版本的第二个整数值。

#### message.method
仅在http.Server返回的请求中有效。
返回一个字符串，表示请求的方法。该属性只读

#### message.rawHeaders
接收到的原始的请求头或响应头列表。
注意，键和值在同一个列表中。偶数位的是键，奇数为的是对应的值。
头信息的名称不会被转换为小写，重复的也不会被合并。

#### message.rawTrailers
接收到原始的Trailer请求头或响应头的键和值。只在end事件时被赋值。

#### message.setTimeout(msecs,callback)
参数：
 - msecs :<number>
 - callback : <Function>
 调用message.connection.setTimeout(msecs,callback)
 返回message。

#### message.socket
返回与连接关联的net.Socket对象。
通过HTTPS的支持，使用request.socket.getPeerCertificate()获取客户端的认证信息。

#### message.statusCode
仅在http.clientRequest返回的响应中有效。

返回一个三位数的HTTP响应状态码。如404.

#### message.statusMessage
仅在http.clientRequest返回的响应中有效
返回HTTP响应状态消息（原因描述）。如OK或Internal Server Error.

#### message.trailers
返回Trailer请求头或响应头对象。只在end事件时被赋值。

#### message.url
仅在http.Server返回的请求中有效。
返回请求的URL字符串。仅包含实际HTTP请求中的URL。如：
````
GET /status?name=ryan HTTP/1.1\r\n
Accept: text/plain\r\n
\r\n

````

则request.url会是：
```'/status?name=ryan' ```

如果想将url解析成各个部分，可以使用require('url').parse(request.url)。
例子：
````
$ node
> require('url').parse('/status?name=ryan')
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?name=ryan',
  query: 'name=ryan',
  pathname: '/status',
  path: '/status?name=ryan',
  href: '/status?name=ryan' }

````

如果想从查询字符串中提取参数，可以使用require('querystring').parse函数，或为require('url').parse的第二个参数传入true,例子：
````
$ node
> require('url').parse('/status?name=ryan', true)
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?name=ryan',
  query: { name: 'ryan' },
  pathname: '/status',
  path: '/status?name=ryan',
  href: '/status?name=ryan' }

````

### http.METHODS
返回解析器支持的HTTP方法的列表

### http.STATUS_CODES
返回标准的HTTP响应状态码的集合，以及各自的简短描述。
例如：```http.STATUS_CODES[404] === 'Not Found' ```

### http.createServer([options][,requestListener])
返回：<http.Server>的实例

The requestListener is a function which is automatically added to the
'request' event.

### http.get(options[,callback])
参数：
 - options ： 同http.request()。 method设置为GET.
 - callback: <Function>
返回：<http.ClientRequest>
因为大多数请求都是GET请求且不带请求主体，所以Node.js提供了该便捷方法。该方法与http.request()唯一的区别是它设置请求方法为GET且自动调用req.end()。注意，回调函数务必消耗掉响应数据。原因见http.ClientRequest.

callback被调用时只传入一个参数，该参数是http.IncomingMessage的一个实例。
一个获取JSON的例子：
````
http.get('http://nodejs.org/dist/index.json',(res)=>{
  const {statusCode} = res;
  const contentType = res.headers['content-type'];
  let error;
  if(statusCode !== 200){
    error = new Error('请求失败。\n'+`状态码：${statusCode}`);
  }else if(!/^application\/json/.test(contentType)){
    error = new Error('无效的content-type.\n'+`期望application/json但获取的是${contentType}`);
  }

  if(error){
     console.error(error.message);
     //消耗响应数据以释放内存
     res.resume();
     return;
  }

  res.setEncoding('utf8');
  let rawData = '';
  res.on('data',(chunk)=>{ rawData += chunk; });
  res.on('end', () => {
       try{
          const parsedData = JSON.parse(rawData);
          console.log(parsedData);
        }catch(e){
          console.error(e.message);
        }
    }).on('error',(e) => {
         console.error(`错误：${e.message}`);
      });
});

````

### http.globalAgent
Agent的全局实例，作为所有HTTP客户端请求的默认Agent.

### http.request(options[,callback])
参数：
 - options: <Object>或<string>或 <URL> ，如果options是一个字符串，它会被自动使用url.parse()解析。如果它是一个URL对象，它会被默认转换成一个options对象。
   - protocol : <string>使用的协议。默认为：'http:'.
   - host: <string> 请求发送至的服务器的域名或IP地址。默认为localhost.
   - hostname: <string> host的别名。 为了支持url.parse(),hostname优先于host.
   - family: <number>当解析host 和hostname时使用的IP地址族。 有效值是4或6.当未指
    定时，则同时使用IP v4和v6.
   - port : <number>远程服务器的端口。默认为80.
   - localAddress: <string>为网络连接绑定的本地接口.
   - socketPath: <string> Unix域Socket(使用host:port或socketPath)
   - method : <string>指定HTTP请求方法的字符串。默认为‘GET’.
   - path: <string>请求的路径。默认为‘/’。应包括查询字符串（如有的话）。当请求的路径中包含
   非法字符时，会抛出异常。目前只有空字符会被拒绝，但未来可能会变化。
   - headers: <Object>包含请求头的对象。
   - auth: <string>基本身份验证，如‘user:password’用来计算Authorization请求头
   - agent: <http.Agent> 或 <boolean>控制Agent的行为。可能的值有：
     - undefined(默认)：对该主机和端口使用http.globalAgent.
     - Agent对象：显式地使用传入的Agent.
     - false: 创建一个新的使用默认值的Agent.
   - createConnection:<Function>当不使用agent选项时，为请求创建一个socket或流。这可以用户避免仅仅创建一个自定义的Agent类来覆盖默认的createConnection函数。
    详见agent.createConnection()。 
   - timeout: <number>指定socket超时的毫秒数。它设置了socket等待连接的超时时间。
 - callback:<Function>， 可选的callback参数会作为单词监听器被添加到‘response’事件。

 返回：<http.ClientRequest>
 Node.js为每台服务器维护多个连接来进行HTTP请求。该函数允许显式地发出请求。

http.request()返回一个http.ClientRequest类的示例。ClientRequest实例是一个可写流。如果需要通过POST请求上传一个文件，则写入到clientRequest对象。

例子：
````
  const postData = querystring.stringify({
   'msg':'Hello World!'
  });

  const options = {
    hostname:'www.google.com',
    post:80,
    path:'/upload',
    method:'POST',
    headers:{
      'Content-Type':'application/x-www-form-urlencoded',
      'Content-Length':Buffer.byteLength(postData)
    }
  };

  const req = http.request(options,(res) => {
     console.log(`状态码：${res.statusCode}`);
     console.log(`响应头：${JSON.stringify(res.headers)}`);
     res.setEncoding('utf8');
     res.on('data',(chunk)=>{
         console.log(`响应主体：${chunk}`);
     });
     res.on('end',() => {
       console.log('响应中已无数据.');
     });
  });
  req.on('error',(e)=>{
     console.error(`请求遇到问题：${e.message}`);
  });

  //写入数据到请求主体
  req.write(postData);
  req.end();

````

注意：在例子中调用了req.end()。使用http.request()必须总是调用req.end()来表明请求的结束，即使没有数据被写入请求主体。

如果请求过程中遇到任何错误（DNS解析错误、TCP级错误、或实际的HTTP解析错误），则在返回的请求对象中会触发‘error’事件。对于所有‘error’事件，如果没有注册监听器，则抛出错误。

以下是需要注意的几个特殊的请求头：
  - 发送‘Connection:keep-alive’会通知Node.js，服务器的链接应一致持续到下一个请求
  - 发送‘Content-Length’请求头会禁用默认的块编码
  - 发送‘Expect’请求头会立即发送请求头。 通常情况下，当发送‘Expect:100-continue’时，超时时间与continue事件的监听器都需要被设置。
  - 发送Authorization请求头会替代auth选项计算基本身份验证。

示例代码：
````
const {URL} = require('url');
const options = new URL('http://abc:xyz@example.com');
const req = http.request(options,(res)=>{
     // ...
  })

````














