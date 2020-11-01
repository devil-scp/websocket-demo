## websocket demo
### 介绍
基于springboot 关于websocket的简单使用

### 步骤
- pom文件引入依赖
````
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
````
- websocket处理器
创建方式多种，参考[Spring官网文档](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/spring-framework-reference/web.html#websocket-server)
````
@Component
public class MyHandler implements WebSocketHandler {
    @Override
    public void afterConnectionEstablished(WebSocketSession webSocketSession) throws Exception {

    }

    @Override
    public void handleMessage(WebSocketSession webSocketSession, WebSocketMessage<?> webSocketMessage) throws Exception {
        System.out.println("======打印接收到的消息====");
        System.out.println(webSocketMessage.getPayload());
    }

    @Override
    public void handleTransportError(WebSocketSession webSocketSession, Throwable throwable) throws Exception {

    }

    @Override
    public void afterConnectionClosed(WebSocketSession webSocketSession, CloseStatus closeStatus) throws Exception {

    }

    @Override
    public boolean supportsPartialMessages() {
        return false;
    }
}
````
- 注册处理器
````
@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry webSocketHandlerRegistry) {
        webSocketHandlerRegistry.addHandler(myHandler(),"/web-socket").setAllowedOrigins("*");
    }

    @Bean
    public WebSocketHandler myHandler() {
        return new MyHandler();
    }
}

````

### 使用
- 启动服务 
- 浏览器访问[websocket工具](https://www.idcd.com/tool/socket)

### WebSocket Handshake
相当于拦截器，可以定制化http握手前后的操作。比如获取token,进行认证等。

### 二进制消息
WebSocket协议定义了两种类型的消息，文本消息和二进制消息。
发送二进制消息
`session.sendMessage(new BinaryMessage(bytes));`
或者使用BinaryWebSocketHandler处理二进制消息
````
@Configuration
@EnableAutoConfiguration
@EnableWebSocket
public class AppWebSocket {

    public static void main(String[] args) {
        SpringApplication.run(AppWebSocket.class, args);
    }

    @Component
    public static class MyWebSocketConfigurer implements WebSocketConfigurer {

        @Override
        public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
            registry.addHandler(new MyTextHandler(), "/text").withSockJS();
            registry.addHandler(new MyBinaryHandler(), "/binary").withSockJS();
        }
    }

    @Component
    public static class MyTextHandler extends TextWebSocketHandler {
        public void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
            session.sendMessage(new TextMessage("hello world"));
        }
    }

    @Component
    public static class MyBinaryHandler extends BinaryWebSocketHandler {
        public void handleBinaryMessage(WebSocketSession session, BinaryMessage message) {
            try {
                session.sendMessage(new BinaryMessage("hello world".getBytes()));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}
````

### 跨域
 `registry.addHandler(myHandler(), "/myHandler").setAllowedOrigins("http://mydomain.com");`
 `setAllowedOrigins`可变参数，支持多个域名
 
 ### SockJS
 SockJS 是一个浏览器上运行的 JavaScript 库，如果浏览器不支持 WebSocket，该库可以模拟对 WebSocket 的支持，实现浏览器和 Web 服务器之间低延迟、全双工、跨域的通讯通道。
 ` registry.addHandler(myHandler(), "/myHandler").withSockJS();`
 
 ### STOMP
 STOMP(Simple Text-Orientated Messaging Protocol) 面向消息的简单文本协议。
 WebSocket是一个消息架构，不强制使用任何特定的消息协议，它依赖于应用层解释消息的含义。
 STOMP在websocket上提供了一中基于帧线路格式。
 简单一点，就是在我们的websocket（TCP）上面加了一层协议，使双方遵循这种协议来发送消息。
 //todo 对网络协议一直比较蒙蔽 看懂了回来更改