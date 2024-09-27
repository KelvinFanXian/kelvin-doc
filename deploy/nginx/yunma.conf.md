```bash
# root@wj:/etc/nginx/conf.d# cat yunma.conf
server {
	listen   80;
	server_name  http://192.168.6.167;
	charset uft-8;
	location / {
		proxy_set_header Host  $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://127.0.0.1:18080/;
	}

	location /admin/ {
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://127.0.0.1:18080/;
	}

}



######################
# admin-ui
######################
server {
	listen       18080;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/admin-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# dealer-ui
######################
server {
	listen       18081;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/dealer-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# open-ui
######################
server {
	listen       18082;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/open-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# ten-ui
######################
server {
	listen       18083;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/ten-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# workbench
######################
server {
	listen       18084;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/workbench;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# member-ui
######################
server {
	listen       18085;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/member-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}

######################
# user-ui
######################
server {
	listen       18086;
	server_name  localhost;
	charset uft-8;
	location / {
		root   /yunma/ui/user-ui;
		try_files $uri $uri/ /index.html;
		index  index.html index.htm;
	}

	#错误寻找页面
	error_page   500 502 503 504  /50x.html;
		location = /50x.html {
	    root   html;
	}
}
```

