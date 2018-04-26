server {
   //端口
    listen      8888;
	//指向目录
    root        /usr/local/nginx/html/ybsc;
	//默认的index的指向
    index       index.php index.html index.htm;
    charset     utf-8;

	//开启gzip服务端压缩
    gzip on;
    gzip_disable "msie6";
    
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 2;
    gzip_min_length 1k;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain application/json application/javascript text/xml application/xml application/x-gzip application/octet-stream;
	
	
	
	
    location / {
       try_files $uri $uri/ /index.html = 404;
    }	
	
	//网络请求接口的地址导向	
    location /api/ {
       proxy_pass http://127.0.0.1:8000/;
    }
	
	
	//设置网页不缓存，js、css和图片缓存
    location ~* \.(html|htm)$ {
      expires -1;
    }
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|gz)$ {
      expires 1d;
      etag on;
      if_modified_since before;
    }
}