## body-parser
body-parser是express的中间件，用于nodejs的body的解析。
转换即将发送的中间件中的request body 在你handlers之前，
通过req.body的属性来是可用的。
### body-parser主要功能
  1. 处理不同类型的请求体： 如 text、json、 urlencoded ,对应的报文主体的格式不同
  2. 处理不同的编码： 如utf8、gbk
  3. 处理不同的压缩类型：如gzip、deflare
  4. 其他边界、异常的处理
## pako
    zlib的压缩和解压  
	
	压缩之后的数据   通过websocket传输得到的是blob对象
	<p>
	  <code>
	  blob对象转为 json
	function blobToString(blob,conn){
	//blob 转为 string
	var client = conn.client;
	var reader = new FileReader();
	reader.readAsArrayBuffer(blob);
	reader.onload = function (e) {
    var pako = require('pako');
    var output = pako.ungzip(reader.result,{to:'string'});	
    //如果是
    if (client.readyState === client.OPEN) {
      if(output  ){
        // console.info(output);
        let obj = JSON.parse(output);
        if(obj && obj.ping){
          let tobj = {pong:obj.ping};
          client.send(JSON.stringify(tobj));
        }else if(obj && obj.status == 'ok'){
        }else if(obj.tick){
          //显示数据
          conn.pobj.data = obj;
          }
        }
       }
     }
   }
	  
	  </code>
	</p>
	
  