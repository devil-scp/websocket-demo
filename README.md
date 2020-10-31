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
