### promise.all方法
   
Promise.all方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。
const p = Promise.all([p1, p2, p3]);
上面代码中，Promise.all方法接受一个数组作为参数，p1、p2、p3都是 Promise 实例，如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为 Promise 实例，再进一步处理。（Promise.all方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。）
p的状态由p1、p2、p3决定，分成两种情况。
（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

例子：

		var length = 0;
		var p1 = new Promise((resolve,reject)=>{
			setTimeout(function(){
				console.info('p1'); 
				length ++; 
				reject('reject p1'); 
			});
		});
		var p2 = new Promise((resolve,reject)=>{
			setTimeout(function(){
				console.info('p2'); 
				length ++; 
				reject('reject p2');  
			});
		});
		var p3 = new Promise((resolve,reject)=>{
			setTimeout(function(){
				console.info('p3');
				 resolve('resolve p3'); 
			});
		});
		var p = new Promise((resolve,reject)=>{ 
		     Promise.all([p1,p2,p3]).then(res=>{
		     	console.info(res);
		     	}).catch(err=>{
		     		length++;
		     		console.info('promise all err='+err); 
		     		if(length <=1){
		     			resolve(err);
		     		}else{
		     			reject(err);
		     		}  
		        });
		});
		p.then(res=>{
			console.info('--then------');
			 console.info(res); 
			  }).catch(err=>{
			  	console.info('--catch------'); 
			  	console.info(err); 
	    });
		打印结果是：
		 Promise {<pending>}
		 p1
		 promise all err=reject p1
		 --catch------
		 reject p1
		 p2
		 p3
		 从打印结果可以看到即时第一个p1运行结果reject了，后面的p2,p2依然会一次进行。
		 p对象是对promise.all进行改进，即在reject次数超过1的时候，p才返回reject,否则返回resolve






