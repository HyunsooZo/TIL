---
description: in Netty
---

# 🌬️ Channel Handler Context

## 머릿말

앞선 포스팅에서 언급 했듯 현재 Protocol Data <--> Json 을 파싱하는 웹플럭스 프로젝트를 맡고 있다.\
관련하여 `Netty`사용중 접하게 된 `ctx`에 대해 알아보고자 한다.

## ChannelHandlerContext ?

`ChannelHandlerContext`는 Netty의 채널 파이프라인 내에서 핸들러들이 상호작용하고 \
데이터를 처리할 수 있도록 돕는 컨텍스트 객체다. \
각 핸들러는 자신만의 `ChannelHandlerContext`를 가지며, 이를 통해 다음과 같은 작업을 수행한다.

* 데이터 읽기 및 쓰기
* 파이프라인 내 다른 핸들러와의 상호작용
* 채널 관련 정보 접근
* 이벤트 처리

즉, `ChannelHandlerContext`는 **핸들러 간의 통신을 중재하고, 데이터 흐름을 관리**하는 중심 역할을 한다.

## 주요 기능

### 1. 메시지 읽기 및 쓰기

`ctx`를 통해 클라이언트로부터 수신한 데이터를 읽거나, 서버로 데이터를 전송할 수 있다. \
주로 `write()`나 `writeAndFlush()` 메서드를 사용하여 데이터를 처리한다.

```java
ctx.write(msg); // 메시지 작성
ctx.flush();    // 버퍼에 있는 메시지 플러시
```

### 2. 파이프라인 접근

`ctx.pipeline()`을 통해 현재 채널의 핸들러 파이프라인에 접근할 수 있다. \
이를 통해 현재 핸들러의 앞이나 뒤에 위치한 다른 핸들러로 이벤트를 전달하거나 추가할 수 있다.

```java
ctx.pipeline().addLast(new AnotherHandler());
```

### 3. 채널 정보 접근

`ctx.channel()`을 사용하여 현재 연결된 클라이언트 소켓 채널에 대한 정보를 얻을 수 있다. \
이를 통해 채널의 상태나 속성을 확인하고 조작할 수 있다.

```java
Channel channel = ctx.channel();
```

### 4. 이벤트 처리

`ChannelHandlerContext`는 다양한 이벤트(예: 연결 수립, 연결 종료, 오류 발생)를 캡처하고 \
처리할 수 있는 메서드를 제공한다. 이를 통해 핸들러는 특정 이벤트에 대한 로직을 구현할 수 있다.

```java
@Override
public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
    cause.printStackTrace();
    ctx.close();
}
```

## ChannelHandlerContext 동작 흐름

<figure><img src="../../../.gitbook/assets/스크린샷 2024-10-30 오후 7.51.12.png" alt=""><figcaption><p>출처: 본인</p></figcaption></figure>

* **클라이언트가 데이터 전송**: 클라이언트가 서버로 데이터를 전송한다.
* **채널이 데이터 수신**: Netty의 채널이 데이터를 수신하고, 첫 번째 핸들러인 `HandlerA`에 전달한다.
* **HandlerA에서 HandlerB로 데이터 전달**: `HandlerA`는 `ctx.fireChannelRead(msg)`를 호출하여 데이터를 다음 핸들러인 `HandlerB`로 전달한다.
* **HandlerB에서 데이터 처리**: `HandlerB`는 수신한 데이터를 처리한다.
* **HandlerB에서 클라이언트로 데이터 전송**: 처리된 데이터를 `ctx.writeAndFlush`를 통해 클라이언트로 전송한다.
* **클라이언트가 데이터 수신**: 클라이언트는 서버로부터 전송된 데이터를 수신한다.



## 예제 코드 (Java)

```java
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;

// NettyServer 클래스: 서버를 설정하고 시작하는 역할을 한다. 
// HandlerA와 HandlerB를 파이프라인에 추가한다.
public class NettyServer {
    private final int port;

    public NettyServer(int port) {
        this.port = port;
    }

    public void run() throws InterruptedException {
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
             .channel(NioServerSocketChannel.class)
             .childHandler(new ChannelInitializer<Channel>() {
                 @Override
                 protected void initChannel(Channel ch) throws Exception {
                     ChannelPipeline p = ch.pipeline();
                     p.addLast(new HandlerA());
                     p.addLast(new HandlerB());
                 }
             });

            ChannelFuture f = b.bind(port).sync();
            System.out.println("Server started on port " + port);
            f.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new NettyServer(8080).run();
    }
}

//HandlerA 클래스: 수신한 데이터를 로그로 출력하고, 
//ctx.fireChannelRead(msg)를 호출하여 다음 핸들러로 데이터를 전달한다.
class HandlerA extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        System.out.println("HandlerA received: " + msg);
        // 데이터를 다음 핸들러로 전달
        ctx.fireChannelRead(msg);
    }
}

// HandlerB 클래스: 데이터를 처리하고, ctx.writeAndFlush를 사용하여 클라이언트로 응답을 전송한다.
class HandlerB extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        System.out.println("HandlerB processing: " + msg);
        // 처리된 데이터를 클라이언트로 전송
        ctx.writeAndFlush("Processed: " + msg);
    }
}

```
