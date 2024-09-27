# mysql

```bash
cd /root/mysql
vim docker-compose.yml
```

```yaml
version: '3.3'
services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - TZ=Asia/Shanghai
      - SET_CONTAINER_TIMEZONE=true
      - CONTAINER_TIMEZONE=Asia/Shanghai
    volumes:
      - /root/mysql/conf:/etc/mysql/conf.d
      - /root/mysql/data:/var/lib/mysql
      - /root/mysql/logs:/var/log/mysql
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 3306:3306
    restart: always
```

```bash
docker-compose up -d --build mysql
```



# redis

```bash
cd /root/redis
vim docker-compose.yml
```

```yaml
version: '3.3'
services:
  redis:
    image: redis:5.0.5
    container_name: redis
    restart: unless-stopped
    hostname: redis
    network_mode: bridge
    ports:
      - 6379:6379/tcp
    command: >
      --requirepass 123456
      --bind 0.0.0.0
      --port 6379
```

```bash
docker-compose up -d --build redis
# new 
docker compose up -d --build redis

redis-cli -h 124.222.8.90 -p 6379 -a 123456
```



