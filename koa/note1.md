## [koa](https://koajs.com/)

### API

#### app.listen 端口监听
#### app.callback()   
返回一个处理request的回调函数来适配http.createServer()方法，
你可以在使用回调函数在Connect/Express app中去挂在你的koa app。
#### app.use(function) 
给应用添加给定的中间件函数， 获取更过信息查看[中间件](https://github.com/koajs/koa/wiki#middleware)
#### app.keys= 
设置签名的cookie keys.
这些被传递给KeyGrip，但是您也可以传递自己的KeyGrip实例。 例如，以下是可接受的：
````
app.keys = ['im a newer secret', 'i like turtle'];
app.keys = new KeyGrip(['im a newer secret', 'i like turtle'], 'sha256');
````
这些键可能会旋转，并在使用{signed：true}选项对cookie进行签名时使用： ```ctx.cookies.set('name', 'tobi', { signed: true }); ```

#### app.context
app.context 是被创建的ctx的属性值。你可以通过编辑app.context来给ctx添加额外的属性。 这对于在整个应用程序中使用的属性和方法（添加到ctx上的）是很有用的， 因为他们可能会更高效（无需中间件）和/或更容易
（需要更少的require()）但会更依赖于ctx, 被认为是反模式。
例如：
````
app.context.db = db();

app.use(async ctx => {
  console.log(ctx.db);
});

````

### app.on