# linux  
  
> 作者：xcx  
> 时间：2020/6/23  10：06 
  
-----------------  
### nginx 反向代理
```conf
upstream game{
    server ok.wushudd.cn;
    server ok.wushudd.cn:9000;
}
server
    {
        listen 80;
        server_name ok.wushudd.cn;
        location /HeXiHe{
          server_name_in_redirect off;
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header REMOTE-HOST $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_pass http://ok.wushudd.cn:9000/HeXiHe/;
        }
        access_log logs/HeXiHe.access.log;
          location /minaData{
          server_name_in_redirect off;
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header REMOTE-HOST $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_pass http://ok.wushudd.cn:9000/minaData/;
        }
        access_log logs/minaData.access.log;
```

## nginx.conf
```conf
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user root;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /var/run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections  102400;
}


http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    client_max_body_size 1024m;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   75;
    types_hash_max_size 2048;
    proxy_connect_timeout 1000s; 
    proxy_send_timeout 300s; 
    proxy_read_timeout 1000s;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.

    upstream gxpt{ 
        server localhost:8800; 
    }



    

    server {
	    listen       80;    
            server_name  ip;

            client_max_body_size 1024m;

            #return      301 https://$server_name$request_uri;

	    location / {
		proxy_redirect off;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://gxpt;
	    }

   
	}
    
	
	
	
	

	#--------------------------------------------
	
	   upstream centro{
       # server 10.100.23.85:30005 weight=1;
        server 10.100.23.109:30005 weight=1;
    }
    upstream jxxt{
       # server localhost:30003 weight=1;
        server 10.100.23.109:30003 weight=1;
    }
    upstream tkksxt{
       # server localhost:30006 weight=1;
        server 10.100.23.109:30006 weight=1;
    }

    server {
        listen       8080;
        server_name  ip;

	location /service/centro {
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout 300s;
            proxy_send_timeout 300s;
            proxy_read_timeout 300s;
            proxy_pass http://centro/;
        }
       location /service/jxxt/ {
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout 300s;
            proxy_send_timeout 300s;
            proxy_read_timeout 300s;
            proxy_pass http://jxxt/;
        }
        location /service/tkksxt/ {
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_connect_timeout 300s;
            proxy_send_timeout 300s;
            proxy_read_timeout 300s;
            proxy_pass http://tkksxt/;
        }


    }


}

```

### 星球联盟使用的
```conf
user  www www;
worker_processes auto;
error_log  /www/wwwlogs/nginx_error.log  crit;
pid        /www/server/nginx/logs/nginx.pid;
worker_rlimit_nofile 51200;

events
    {
        use epoll;
        worker_connections 51200;
        multi_accept on;
    }

http
    {
        include       mime.types;
		#include luawaf.conf;

		include proxy.conf;

        default_type  application/octet-stream;

        server_names_hash_bucket_size 512;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 50m;

        sendfile   on;
        tcp_nopush on;

        keepalive_timeout 60;

        tcp_nodelay on;

        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
        fastcgi_buffer_size 64k;
        fastcgi_buffers 4 64k;
        fastcgi_busy_buffers_size 128k;
        fastcgi_temp_file_write_size 256k;
		fastcgi_intercept_errors on;

        gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml;
        gzip_vary on;
        gzip_proxied   expired no-cache no-store private auth;
        gzip_disable   "MSIE [1-6]\.";

        limit_conn_zone $binary_remote_addr zone=perip:10m;
		limit_conn_zone $server_name zone=perserver:10m;

        server_tokens off;
        access_log off;

        
upstream game{
    server 127.0.0.1:9000;
}


upstream agent-admin {
            #ip_hash;
            server 127.0.0.1:8088;

             }

     upstream admin {
            #ip_hash;
            server 127.0.0.1:8089;

             }
server
    {
        listen 80;
        server_name ok.wushudd.cn;
        location /HeXiHe{
          server_name_in_redirect off;
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header REMOTE-HOST $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_pass http://game;
        }
        access_log logs/HeXiHe.access.log;
          location /minaData{
          server_name_in_redirect off;
          proxy_set_header Host $host:$server_port;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header REMOTE-HOST $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_pass http://game;
        }
        access_log logs/minaData.access.log;
       location ^~/agent-admin/ {
             proxy_pass 
http://agent-admin;
        }

        location ^~/admin/ {
             proxy_pass 
http://admin;

        }
        index index.html index.htm index.php;
        root  /www/server/phpmyadmin;

        #error_page   404   /404.html;
        include enable-php.conf;

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log  /www/wwwlogs/access.log;
    }
include /www/server/panel/vhost/nginx/*.conf;
}


```星球联盟