```bash
# Starting
nginx
# Stopping, and Reloading Configuration
nginx -s signal
  # stop — fast shutdown
  # quit — graceful shutdown
  # reload — reloading the configuration file
  # reopen — reopening the log files
  

```

# domain-->port

```nginx.conf
vim /etc/nginx/nginx.conf

user  jenkins;
worker_processes  3;

error_log /export/Logs/nginx/error.log;
error_log /export/Logs/nginx/error.log  notice;
error_log /export/Logs/nginx/error.log  info;

#pid        logs/nginx.pid;


events {
   worker_connections  1024;
}


http {
   include       mime.types;
   default_type  application/octet-stream;
   keepalive_timeout  65;

   upstream mysdr8098 {   
     server 127.0.0.1:8098;
   }
   upstream mysdr8095 {   
     server 127.0.0.1:8095;
   }
    upstream mysdr8090 {   
     server 127.0.0.1:8090;
   }
   server {
       listen       80;
       server_name  djcms-act.my.com;
    location  / {   
       proxy_next_upstream     http_500 http_502 http_503 http_504 error timeout invalid_header;
       proxy_set_header        Host  $host;
       proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;              
       proxy_pass  http://mysdr8098;  #请求转向mysvr 定义的服务器列表
       }        
   }

   server {
       listen       80;
       server_name  cms-yunying.my.com;
    location  / {   
       proxy_next_upstream     http_500 http_502 http_503 http_504 error timeout invalid_header;
       proxy_set_header        Host  $host;
       proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;              
       proxy_pass  http://mysdr8095;  #请求转向mysvr 定义的服务器列表
       }        
   }

  server {
       listen       80;
       server_name  dev-djcms.my.com;
    location  / {   
       proxy_next_upstream     http_500 http_502 http_503 http_504 error timeout invalid_header;
       proxy_set_header        Host  $host;
       proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;              
       proxy_pass  http://mysdr8090;  #请求转向mysvr 定义的服务器列表
       }        
   }
}
```

