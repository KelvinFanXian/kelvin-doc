## 3. Running and administering Rabbit

- Server management - starting and stoping nodes
- Permission configuration
- Usage statics
- Troubleshooting problems with RabbitMQ and Erlang

## 3.1 Server management

### 3.1.1 Starting nodes

```bash
./sbin/rabbitmq-server
./sbin/rabbitmq-server -detached
# see log
/var/log/rabbitmq/rabbit@[hostname].log
```

### 3.1.2 Stopping nodes

```bash
./sbin/rabbitmqctl stop -n rabbit@[hostname]
```

### 3.1.3. Stopping and restarting the application: what’s the difference?

```bash
# just stop app, not both app & Erlang node
./rabbitmqctl stop_app.
```

### 3.1.4 Rabbit configuration files

```bash
# location
/etc/rabbitmq/rabbitmq.config
## “but its location can be changed via the CONFIG_FILE environment variable set in the rabbitmq-server script”


```

```javascript
[
   {mnesia, [{dump_log_write_threshold, 1000}]},
   {rabbit, [{vm_memory_high_watermark, 0.4}]}
].
```



## 3.2 Asking permission

### 3.2.1 Managing users

```bash
# add user
# ./rabbitmqctl add_user username password
./rabbitmqctl add_user cashing-tier cashMe1

# delete user
./rabbitmqctl delete_user cashing-tier

# list
./rabbitmqctl list_users

# change password
./rabbitmqctl change_password cashing-tier compl3xPassword

```



### 3.2.2 Rabbit's permission system

- Access control entries can't span vhosts

```bash
./rabbitmqctl set_permissions -p sycamore cashing-tier ".*" ".*" ".*"
# -p sycamore  - specify target vhost
# cashing-tier  - the user
# ".*" ".*" ".*"  - permissions: configure, write, read
```



- permissions accept PCRE

```bash
".*"  -  any queue or exchange
"checks-.*" - only queues and exchanges starting with checks-
"" - no queues or exchanges
```



- eg.

```bash
./rabbitmqctl set_permissions -p oak -s all cashing-tier "" "checks-.*" ".*"
./rabbitmqctl list_permissions -p oak
./rabbitmqctl clear_permissions -p oak cashing-tier
./rabbitmqctl list_permissions -p oak

 ./rabbitmqctl list_user_permissions cashing-tier
```

- ⚠️ **clear_permissioins** will remove all permmissions for the user on the specified vhost

## 3.3 Checking up

### 3.3.1 Viewing statistics

- Listing Queues and Message Counts

```bash
./rabbitmqctl list_queues
./rabbitmqctl list_queues -p sycamore
./rabbitmqctl list_queues name messages consumers memory
./rabbitmqctl list_queues name durable auto_delete
```



- Viewing Exchanges and Bindings

```bash
./rabbitmqctl list_exchanges
# anonymous exchange be bound to any other queues
./rabbitmqctl list_exchanges name type durable auto_delete
./rabbitmqctl list_bindings

```



### 3.3.2 Understanding RabbitMQ's logs

- Reading the Logs on the Filesystem

```bash
# rabbitmq-server
LOG_BASE=/var/log/rabbitmq

# Inside above folder will create two log file:
RABBITMQ_NODENAME-sasl.log  # sasl- System Application Support Libraries
RABBITMQ_NODENAME.log

```



- Rotating the Logs

```bash
./rabbitmqctl rotate_logs suffix
./rabbitmqctl rotate_logs .1
ls /var/log/rabbitmq
```



- Accessing the Logs in Real Time Via Qmqp

```php
# config.php
<?php
define('HOST', 'localhost');
define('PORT', '5672');
define('USER', 'guest');
define('PASS', 'guest');
define('VHOST', '/');
?>
```

```php
# Log listeners
# 1. Declare three listening queues
list($errors_queue, , ) = $ch->queue_declare();
list($warnings_queue, , ) = $ch->queue_declare();
list($info_queue, , ) = $ch->queue_declare();

$exchange = 'amq.rabbitmq.log';

# 2. Bind queues to log exchanges
$ch->queue_bind($errors_queue, $exchange, "error");
$ch->queue_bind($warnings_queue, $exchange, "warning");
$ch->queue_bind($info_queue, $exchange, "info");

# 3. Create callback fucntions
$error_callback = function($msg) {
  echo 'error: ', $msg->body, "\n";
  $msg->delivery_info['channel']->basic_ack($msg->delivery_info['delivery_tag']);
}

$warning_callback = function($msg) {
  echo 'warning: ', $msg->body, "\n";
  $msg->delivery_info['channel']->basic_ack($msg->delivery_info['delivery_tag']);
}
```



## 3.4 Fixing a bad Rabbit: troubleshooting

### 3.4.1 badrpc, nodedown and other Erlang-induced problems

```bash
ps ax | grep rabbit
```

- Erlang Cookies

```bash
cat ~/.erlang.cookie
```



- Erlang Nodes

```bash
# edit rabbitmqctl and change where it says 
# -sname rabbitctl$ to -name rabbitmqctl$
./rabbitmqctl list_queues
```



- Mnesia and Host Names

```bash
# RabbitMQ uses the word rabbit as node name. If you changed it using the Erlang sname optioin, then Mnesia will encounter the same problem again.
```



- Erlang Troubleshooting Tips



## 3.5 Summary





# 4. Solving problems with rabbit: coding and patterns

- Designing applications toward messaging
- Messaging patterns
- Fire-and-forget models
- RPC with RabbitMQ

## 4.1 A decoupling story:what pushes us to messaging

### 4.1.1 An asynchornous state of mind (separating requests and actions)

### 4.1.2 Affording scale: a world without load balancers

### 4.1.3 Zero-effort APIs: why be locked into just one language?



## 4.2 Fire-and-forget models

### 4.2.1 Sending alerts

- 1 connect to broker
- 2 Declare and bind queues and exchanges for the alert topics
- 3 Attach the alert processors
- 4 Critical alerts processor
- 5 Alert consumer - alert_consumer.py, start to finish
- 6 Alert generator example - alert_producer.py



### 4.2.2 Parallel processing



## 4.3 Remember me: RPC over RabbitMQ and waiting for answers

### 4.3.1 Private queues and sending acknowledgements

### 4.3.2 Simple JSON RPC with reply_to

- rpc_server.py

```python
# rpc_server.pyxx
"""
实现一个ping调用：
接收来自客户端的ping调用，发回一个Pong！应答，
并携带了客户端初始调用时的时间戳
"""

import json
import pika
from pika.exchange_type import ExchangeType

creds_broker = pika.PlainCredentials("guest", "guest")
conn_params = pika.ConnectionParameters("i.go", virtual_host="/", credentials=creds_broker)
conn_broker = pika.BlockingConnection(conn_params)
channel = conn_broker.channel()

channel.exchange_declare(exchange="rpc", exchange_type=ExchangeType.direct, auto_delete=False)
channel.queue_declare(queue="ping", auto_delete=False)
channel.queue_bind(queue="ping", exchange="rpc", routing_key="ping")

""" 'ping' API call."""


def api_ping(channel_, method, header, body):
    channel_.basic_ack(delivery_tag=method.delivery_tag)
    msg_dict = json.loads(body)
    print("Received API call...replying...")
    # 在用于消费的同一条信道上发布应答消息，api_ping函数返回前，Pika库不会再次开始消费
    # 使用header中的reply_to作为应答消息的路由键
    # 不同于通过MQ发布的任何其他消息，这里没有交换器
    channel_.basic_publish(body="Pong!" + str(msg_dict["time"]), exchange="",
                           routing_key=header.reply_to)


channel.basic_consume(queue="ping", on_message_callback=api_ping, consumer_tag="ping")
print("Waiting for RPC calls...")
channel.start_consuming()
```

- rpc_client.py

```python
# rpc_client.py
import json
import pika
import time

creds_broker = pika.PlainCredentials("guest", "guest")
conn_params = pika.ConnectionParameters("i.go", virtual_host="/",
                                        credentials=creds_broker)
conn_broker = pika.BlockingConnection(conn_params)
channel = conn_broker.channel()

msg = json.dumps({"client_name": "RPC Client 1.0", "time": time.time()})

# result: 创建应答队列
result = channel.queue_declare(queue="", exclusive=True, auto_delete=True)
msg_props = pika.BasicProperties()
msg_props.reply_to = result.method.queue

channel.basic_publish(body=msg, exchange="rpc", properties=msg_props,
                      routing_key="ping")
print("Sent 'ping' RPC call. Waiting for reply...")


def reply_callback(channel_, method, header, body):
    """Receives RPC server replyies."""
    print("RPC Reply ---" + str(body))
    # 权限独占，自动删除
    channel_.stop_consuming()


channel.basic_consume(on_message_callback=reply_callback, queue=result.method.queue,
                      consumer_tag=result.method.queue)
channel.start_consuming()

```



# Chapter5 Clustering and dealing with failure

- Architecture of a RabbitMQ cluster
- Setting up a cluster on your laptop
- Creating a cluster with physical servers
- Upgrading cluster nodes
- Working with mirrored queues



## 5.1 Batteries included: RabbitMQ clustering

- Two goal in mind (clustering built in):
  - High available
  - linearly scaling messageing throughput by adding more nodes



## 5.2 Architecture of cluster

- Four kinds of internal meta:
  - Queue meta -- Queue names and their properties(are they durable or auto-delete?)
  - Exchange meta -- Exchange's name type, properties(durable?)
  - Binding meta -- a simple table showing how to route messages to queues
  - Vhost meta -- Namespacing and security attributes for the queues, exchanges, and bindings within a vhost





