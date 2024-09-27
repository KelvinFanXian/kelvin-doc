# code2-1 EchoServerHandler

```java
@Shareable // 标示一个Channel-Handler可以被多个Channel安全地共享
public class EchoServerHandler extends ChanelInboundHandlerAdapter{
  @Override
  public void channelRead(ChannelHandlerContext ctx, Object msg){
    ByteBuf in = (ByteBuf)msg;
    System.out.println(
      "Server received: " + in.toString(CharsetUtil.UTF_8)); // 将消息记录到控制台
    ctx.write(in); // 将接收到的消息写给发送者，而不冲刷出站消息
  }
  
  @Override
  public void channelReadComplete(ChannelHandlerContext ctx) {
    ctx.writeAndFlush(Unpooled.EMPTY_BUFFER)
      .addListener(ChannelFutureListener.CLOSE); // 将未决消息冲刷到远程节点，并且关闭该Channel
  }
  
  @Override
  public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause){
    cause.printStackTrace(); // 打印异常栈跟踪
    ctx.close(); // 关闭该Channel
  }
}
```



# code2-2 EchoServer类

```java
public class EchoServer {
  private final int port;
  
  public EchoServer(int prot) {
    this.port = prot;
  }
  
  public static void main(String[] args) throws Exception{
    if(args.length != 1) {
      System.err.println(
        "Usage:" + EchoServer.class.getSimpleName() + " ");
    }
    int port = Integer.parseInt(args[0]); //设置端口值
    new EchoServer(port).start(); // 调用服务器的start()方法
  }
  
  public void start() throws Exception{
    final EchoServerHandler serverHandler = new EchoServerHandler();
    EventLoopGroup group = new NioEventLoopGroup(); // 1.创建Event-LoopGroup
    try {
      ServerBootstrap b = new ServerBootstrap(); // 2.创建Server-Bootstrap
      b.group(group)
        .channel(NioServerSocketChannel.class) // 3.指定所使用的NIO传输Channel
        .localAddress(new InetSocketAddress(port)) // 4.使用指定的端口设置套接字地址
        .childHandler(new ChannelInitializer(){ // 5.添加一个EchoServerHandler到子Channel的ChannelPipeline
          @Override
          public void initChannel(SocketChannel ch) throw Exception {
            ch.pipeline().addLast(serverHandler); // EchoServerHandler 
          }
          
        });
      ChannelFuture f = b.bind().sync(); // 6.异步地绑定服务器；调用sync()方法堵塞等待直到绑定完成
      f.channel().closeFuture().sync(); // 7. 获取Channel的CloseFuture，并且阻塞当前线程直到它完成
    } finally {
      group.shutdownGracefully().sync(); // 8. 关闭EventLoopGroup，释放所有的资源
    }
  }
}
```



# code2-3 客户端的ChannelHandler

```java
@Sharable
public class EchoClientHandler extends SimpleChannelInboundHandler<ByteBuf> {
  @Override
  public void channelActive(ChannelHandlerContext ctx){
    // 当被通知Channel是活跃的时候，发送一条消息
    ctx.writeAndFlush(Unpooled.copiedBuffer("Netty rocks!", CharsetUtil.UTF_8));
  }
  
  @Override
  public void channelRead0(ChannelHandlerContext ctx, ByteBuf in){
    System.out.println( // 记录已接收消息的转储
      "Client received: " + in.toString(CharsetUtil.UTF_8));
  }
  
  @Override
  public void excptionCaught(ChannelHandlerContext ctx, Throwable cause) {
    // 在发生异常时，记录错误并关闭channel
    cause.printStackTrace();
    ctx.close();
  }
}
```



# code2-4 客户端的主类

```java
public class EchoClient {
  private final String host;
  private final int port;
  
  public EchoClient(String host, int port) {
    this.host = host;
    this.prot = port;
  }

  public void start() throws Exception {
    EventLoopGroup group = new NioEventLoopGroup();
    try {
      Bootstrap b = new Bootstrap();
      b.group(group)
        .channel(NioSocketChannel.class)
        .remoteAdress(new InetSocketAdress(host, port))
        .handler(new ChannelInitializer<SocketChannel>() {
          @Override
          public void initChannel(SocketChannel ch) throw Exception {
            ch.pipeline().addLast(new EchoClientHandler());
          }
        });
      ChannelFutrue f = b.connect().sync();
      f.channel().closeFuture().sync();
    } finally {
      group.shutdownGracefully().sync();
    }
  }
  
  public static void main(String[] args) throws Exception {
    if(args.length != 2) {
      System.err.println(
        "Usage: " + EchoClient.class.getSimpleName() + " <host> <port>");
      return;
    }
    String host = args[0];
    int port = Integer.parseInt(args[1]);
    new EchoClient(host, port).start();
  }
}
```







# Listing7-1 Executing tasks in an event loop

```java
while (!teminated) {
	// Blocks until there are events that are ready to run
  List<Runnable> readyEvents = blockUntilEventsReady();
  for (Runnable ev: readyEvents) {
    ev.run(); // Loops over and runs all the events
  }
}
```



# Listing7-2 EventLoop class hierarchy

![image-20230625151810801](https://oss-kelvinvan.oss-cn-chengdu.aliyuncs.com/img/image-20230625151810801.png)



# code7-2 使用ScheduledExecutorService调度任务

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(10); // 一个具有10个线程的线程池

ScheduledFuture<?> future = executor.schedule(
	new Runnable(){
    @Override
    public void run(){
      System.out.println("60 seconds later");
    }
  }
, 60, TimeUnit.SECONDs); // 60s后执行
...
executor.shutdown(); // 任务完成就释放资源
```





# Code7-3 使用EventLoop调度任务

## 60秒后执行

```java
Channel ch = ...
ScheduledFuture<?> future = ch.eventLoop().schedule(
	new Runnable() {
    @Override
    public void run(){
      System.out.println("60 seconds later");
    }
  }
, 60, TimeUnit.SECONDS);
// 经过60秒后，Runnable实例将由分配给Channel的
// EventLoop执行。
 如果要调度任务以60秒/次，请使用 scheduleAtFixedRate()
```

## 60秒后执行，且以后每隔60秒运行

```java
Channel ch = ...
ScheduledFuture<?> future = ch.eventLoop().scheduleAtFixedRate(
	new Runnable() {
    @Override
    public void run(){
      System.out.println("Run every 60 seconds"); // 将一直运行，直到ScheduledFuture被取消
    }
  }, 60, 60, TimeUnit.Seconds); // 调度在60秒后，且以后每隔60秒运行
```

## Scheduled-Future

```java
/// 想要取消或检查（被调度任务的）执行状态，可以使用每个异步操作所返回的 Scheduled-Future
ScheduledFuture<?> future = ch.eventLoop().scheduleAtFixedRate(...);
// some other code that runs...
boolean mayInterruptIfRunning = false;
future.cancel(mayInterruptIfRunning); // 取消该任务，防止它再次运行
```



# Figure7-3  EventLoop的执行逻辑









