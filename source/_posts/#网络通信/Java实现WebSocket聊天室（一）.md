---
title: Java实现WebSocket聊天室（一）
date: 2020-02-26 23:39:28
categories:
- 网络
tags: 
- 网络协议
- websocket
keywors: 
description: 
top_img: 
cover: /img/websocket.jpg
---

## 什么是WebSocket
WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

现在，很多网站为了实现推送技术，所用的技术都是 Ajax 轮询。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。这种传统的模式带来很明显的缺点，即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分，显然这样会浪费很多的带宽等资源。

**WebSocket可以做聊天室，股票实时价格显示等应用**

![https://www.runoob.com/wp-content/uploads/2016/03/ws.png](https://www.runoob.com/wp-content/uploads/2016/03/ws.png)

## WebSocket和WebSocket API
WebSocket是一种应用协议，而我们常常看到了HTML5 WebSocket是API,不要将其进行混淆。

广义上的 HTML5 里面包含的是 WebSocket API，并不是 WebSocket。简单的说，可以把 WebSocket 当成 HTTP，WebSocket API 当成 Ajax。

## SpringBoot集成WebSocket的四种实现方式
1. 原生注解
2. Spring封装
3. TIO
4. STOMP

参考：https://blog.csdn.net/u011719271/article/details/102712113

下面我采用原生注解的方式

## 代码实现

### Maven依赖
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
```

### WebSocketConfig
启用WebSocket的支持
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * 开启WebSocket支持
 */
@Configuration
public class WebSocketConfig {

    @Bean
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }
}
```

### WebSocketServer
WebSocket是类似客户端服务端的形式(采用ws协议)，WebSocketServer其实就相当于一个ws协议的Controller直接@ServerEndpoint("/websocket")、@Component启用即可，这里采用Spring原生注解对的方式，然后在里面实现@OnOpen,@onClose,@onMessage等方法

```java
@Component
@ServerEndpoint("/websocket/{id}")
public class WebSocketServer {

    private static final Logger logger = LoggerFactory.getLogger(WebSocketServer.class);

    //静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
    private static int onlineCount = 0;
    //concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象。
    private static ConcurrentHashMap<String, WebSocketServer> webSocketMap = new ConcurrentHashMap<>();
    //与某个客户端的连接会话，需要通过它来给客户端发送数据
    private Session session;
    //接收sid
    private String id="";

    /**
     * 连接建立成功调用的方法*/
    @OnOpen
    public void onOpen(Session session, @PathParam("id")String id) {
        this.session = session;
        webSocketMap.put(id, this);
        addOnlineCount();           //在线数加1
        logger.info("有新窗口开始监听:" + id + ",当前在线人数为" + getOnlineCount());
        this.id = id;
        try {
            sendMessage("连接成功");
        } catch (IOException e) {
            logger.error("websocket IO异常");
        }
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose() {
        webSocketMap.remove(id);  //从Map中删除
        subOnlineCount();           //在线数减1
        logger.info("有一连接关闭！当前在线人数为" + getOnlineCount());
    }

    /**
     * 收到客户端消息后调用的方法
     * @param message 客户端发送过来的消息*/
    @OnMessage
    public void onMessage(String message, Session session) {
        logger.info("收到来自窗口" + id + "的信息:" + message);
        Collection<WebSocketServer> values = webSocketMap.values();
        //群发消息
        for (WebSocketServer item : values) {
            try {
                item.sendMessage(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * @param session
     * @param error
     */
    @OnError
    public void onError(Session session, Throwable error) {
        logger.error("发生错误");
        error.printStackTrace();
    }

    /**
     * 实现服务器主动推送
     */
    public void sendMessage(String message) throws IOException {
        this.session.getBasicRemote().sendText(message);
    }


    /**
     * 群发自定义消息
     * */
    public static void sendInfo(String message, @PathParam("id") String id) throws IOException {
        logger.info("推送消息到窗口"+id+"，推送内容:"+message);
        Collection<WebSocketServer> values = webSocketMap.values();
        for (WebSocketServer item : values) {
            try {
                //这里可以设定只推送给这个id的，为null则全部推送
                if(id == null) {
                    item.sendMessage(message);
                }else if(item.id.equals(id)){
                    item.sendMessage(message);
                }
            } catch (IOException e) {
                continue;
            }
        }
    }

    public static synchronized int getOnlineCount() {
        return onlineCount;
    }

    public static synchronized void addOnlineCount() {
        WebSocketServer.onlineCount++;
    }

    public static synchronized void subOnlineCount() {
        WebSocketServer.onlineCount--;
    }
}
```

### index.html
```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="utf-8">
    <title>WebSocket</title>

    <script type="text/javascript">
        function WebSocketTest()
        {
            if ("WebSocket" in window)
            {
                alert("您的浏览器支持 WebSocket!");

                // 打开一个 web socket
                var ws = new WebSocket("ws://127.0.0.1:8081/websocket/csliao");

                ws.onopen = function()
                {
                    // Web Socket 已连接上，使用 send() 方法发送数据
                    ws.send("发送数据");
                    alert("数据发送中...");
                };

                ws.onmessage = function (evt)
                {
                    var received_msg = evt.data;
                    alert(received_msg)
                    alert("数据已接收...");
                };

                ws.onclose = function()
                {
                    // 关闭 websocket
                    alert("连接已关闭...");
                };
            }

            else
            {
                // 浏览器不支持 WebSocket
                alert("您的浏览器不支持 WebSocket!");
            }
        }
    </script>

</head>
<body>

<div id="sse">
    <a href="javascript:WebSocketTest()">运行 WebSocket</a>
</div>

</body>
</html>
```

参考资料：
- https://www.runoob.com/html/html5-websocket.html
- https://juejin.im/post/5d968ec05188252d3a6a643a#heading-1
---
